# Autonomous Inbound Sync Graph Query Fix Instructions

## Objective
Fix the Inbound Sync service to properly honor the `UserCreationWindowDays` configuration when querying Microsoft Graph API. The service should only fetch users created within the specified window (e.g., last 30 days), not all users.

## Problem Statement
Current code:
- ✅ Reads `UserCreationWindowDays` from config
- ✅ Calculates cutoff date: NOW - window_days
- ✅ Constructs Graph OData filter: `userType eq 'Member' and createdDateTime ge {cutoff_date}`
- ✅ Sends filter to Microsoft Graph API
- ❌ **Graph API returns more users than expected (violates creation window)**
- ❌ **Users outside the creation window are being synced to tap_targets**

Possible root causes:
1. Graph Query filter not being sent correctly to API
2. Cutoff date calculation is wrong (timezone issue, math error)
3. Date format in filter is invalid, causing Graph API to ignore it
4. Graph API is returning cached results from previous queries
5. Filter is being OR'd with other conditions instead of AND'd
6. userType filter is missing or wrong (causing Guest users to be included)

## Autonomous Iteration Process

### Loop Steps (Repeat Until Fixed):

#### 1. CONFIG CHECK - What Configuration Is Being Used?
```powershell
# Check live config from function logs
GET http://localhost:7071/api/v2/config
# Look for: UserCreationWindowDays value

# Expected output: { "user_creation_window_days": 30, ... }
```
**Record**: 
- Current `UserCreationWindowDays` value from config
- Config source (Azurite, environment variable, hardcoded default?)
- Config last updated timestamp

#### 2. TRIGGER INBOUND SYNC - Start Graph Query
Call orchestrator endpoint:
```
POST http://localhost:7071/api/v2/admin/workflow/inbound-sync
```
**Record**: 
- Orchestration ID from response
- Current timestamp (correlation for logs)

#### 3. CAPTURE GRAPH FILTER - What Query Was Sent?
Monitor function logs for messages:
```
[INBOUNDSYNC-DEBUG] ===== INBOUNDSYNC ACTIVITY STARTED =====
[INBOUNDSYNC-DEBUG] Using UserCreationWindowDays={WindowDays}
[INBOUNDSYNC-DEBUG] Calculated cutoff date: Now={Now}, WindowDays={WindowDays}, CutoffDate={CutoffDate}
```

**Capture these values:**
- `UserCreationWindowDays` (should match config)
- `Now` (timestamp when log was written)
- `CutoffDate` (calculated: Now - WindowDays)
- The actual Graph OData filter string being used

Expected filter format:
```
userType eq 'Member' and createdDateTime ge 2026-02-21T03:08:16Z
```

**Record**: 
- Is the filter present in logs?
- Is cutoff date correct?
- Is filter format valid OData v4?

#### 4. CHECK GRAPH API RESPONSE - What Did Graph Return?
Look for logs showing Graph API results:
```
Fetched {TotalCount} users
Fetched {TotalCount} users, {FilteredCount} passed filters
```

**Manual verification** (if logs don't show count):
Query Graph API directly with same filter:
```powershell
# Use the EXACT filter from step 3
$cutoffDate = (Get-Date).AddDays(-30)  # Or whatever the window is
$filter = "userType eq 'Member' and createdDateTime ge $($cutoffDate.ToString('yyyy-MM-ddTHH:mm:ssZ'))"

# Query Graph (requires token - use portal or Graph Explorer)
GET https://graph.microsoft.com/v1.0/users?$filter={encoded_filter}&$select=id,userPrincipalName,createdDateTime,userType
```

**Record**:
- How many total users did Graph API return?
- Do these users have `createdDateTime >= cutoffDate`?
- Are any users with `userType != 'Member'` included?
- Are any users with very old `createdDateTime` included?

Expected: All returned users should have `createdDateTime >= cutoffDate`

#### 5. CHECK DATABASE SYNC RESULTS - Did Tap Targets Get Created?
Query database after sync completes:
```sql
-- Check newly created targets
SELECT TOP 20 user_id, created_at, created_date_time, nomination_authority, status
FROM tap_targets
ORDER BY created_at DESC

-- Compare: How many targets were created in this sync?
-- Are their created_date_time values recent (within creation window)?

-- Check for STALE targets that shouldn't have been created
SELECT user_id, created_date_time, 
       DATEDIFF(DAY, created_date_time, GETUTCDATE()) AS days_old
FROM tap_targets
WHERE DATEDIFF(DAY, created_date_time, GETUTCDATE()) > 30  -- Older than 30-day window
ORDER BY created_date_time
```

**Record**:
- Total targets in database
- Targets created in this sync
- Any targets older than the creation window?
- Nomination authority of newly synced targets (should be "AutoNominator")

#### 6. ANALYZE - Identify Root Cause
Based on logs and database checks:

| Symptom | Diagnosis | Next Action |
|---------|-----------|-------------|
| **Filter missing from logs** | Filter not being built | Check `BuildGraphFilter()` in DurableWorkflowFunctions.cs, verify config is loaded |
| **Filter invalid format (date parsing error)** | Date format wrong | Check date string format matches `yyyy-MM-ddTHH:mm:ssZ` exactly |
| **Filter has wrong cutoff date** | Math error in calculation | Check: `DateTime.UtcNow.AddDays(-creationWindowDays)` |
| **Graph returns all users (filter ignored)** | Filter not being passed to SDK | Check: `config.QueryParameters` assignments in GraphService.cs |
| **Graph returns users outside window** | Filter AND logic broken | Check filter string doesn't have OR instead of AND |
| **Targets created with very old dates** | Filter working but wrong column used | Check: Is query using `createdDateTime` or `onPremisesExtensionAttributes.created`? |

#### 7. FIX CODE - Apply Changes
Based on root cause from step 6, modify:

**File**: `DurableWorkflowFunctions.cs` - `BuildGraphFilter()` method
```csharp
// ✅ Verify:
// 1. Config is being read correctly
// 2. UserCreationWindowDays is not null/zero
// 3. DateTime.UtcNow is being called (not some cached time)
// 4. AddDays(-config.UserCreationWindowDays) calculation is correct
// 5. ISO format string: yyyy-MM-ddTHH:mm:ssZ (with escaped backslash)
```

**File**: `GraphQueryBuilders.cs` - `BuildFilter()` method
```csharp
// ✅ Verify:
// 1. Filter has BOTH conditions: userType AND createdDateTime
// 2. userType eq 'Member' (no Guest users)
// 3. createdDateTime ge {cutoff} (NOT greater than, but >= cutoff)
// 4. ISO format: cutoffDate.ToString("yyyy-MM-ddTHH:mm:ss\\Z")
// 5. No typos in field names or operators
```

**File**: `GraphService.cs` - `GetUsersAsync()` method
```csharp
// ✅ Verify:
// 1. Filter parameter is being passed to `config.QueryParameters`
// 2. If using SDK query builder, check filter is set BEFORE calling API
// 3. Check continuation token handling doesn't lose the original filter
// 4. Verify no other filters are OR'd into the query
```

**Common fixes:**
```csharp
// WRONG: Missing creation window filter
"userType eq 'Member'"

// CORRECT: Creation window filter included
"userType eq 'Member' and createdDateTime ge 2026-02-21T03:08:16Z"

// WRONG: Using wrong column name
"userType eq 'Member' and onPremisesExtensionAttributes/created_date_time ge ..."

// CORRECT: Using Graph field name
"userType eq 'Member' and createdDateTime ge ..."

// WRONG: Date format has no timezone
"createdDateTime ge 2026-02-21T03:08:16"  // Missing Z

// CORRECT: ISO 8601 with Z
"createdDateTime ge 2026-02-21T03:08:16Z"

// WRONG: OR logic mixes concerns
"(userType eq 'Guest' or userType eq 'Member') and ..."

// CORRECT: AND logic for member-only filtering
"userType eq 'Member' and createdDateTime ge ..."
```

#### 8. BUILD & DEPLOY - Verify Compilation
```bash
dotnet build
```
**Record**: Check for compile errors

#### 9. REPEAT - Go Back to Step 1
Go back to step 1 (check config again) and repeat the loop

---

## Success Criteria

✅ **DONE WHEN:**
- Function logs show: `[INBOUNDSYNC-DEBUG] Using UserCreationWindowDays={N}`
- Filter string in logs contains both: `userType eq 'Member'` AND `createdDateTime ge {date}`
- Cutoff date is calculated correctly: Now - N days, in ISO format with Z
- Graph API returns only users with `createdDateTime >= cutoffDate`
- Database shows newly synced targets all have recent `created_date_time` values
- No targets older than the creation window are created
- Logs show expected user count (not all users in tenant)
- No exceptions in logs about filter formatting

## Key Files to Monitor

| File | Purpose |
|------|---------|
| `DurableWorkflowFunctions.cs` | InboundSyncPageActivity - orchestrates the sync |
| `DurableWorkflowFunctions.BuildGraphFilter()` | Calculates cutoff and builds filter string |
| `GraphQueryBuilders.cs` - InboundSync.BuildFilter() | Constructs Graph OData filter |
| `GraphService.cs` - GetUsersAsync() | Sends filter to Microsoft Graph API |
| `TapDbContext.cs` | Database schema for tap_targets |
| Function logs | Runtime execution details |

## Quick Diagnostic Queries

**Check what config is loaded:**
```powershell
$config = curl http://localhost:7071/api/v2/config | ConvertFrom-Json
$config | ConvertTo-Json -Depth 5
```

**Check inbound sync activity logs (Azurite):**
```powershell
# Look for InboundSyncPageActivity in durable function history
# Check "InboundSyncInput" task for details
```

**Check tap_targets for suspicious records:**
```sql
-- Targets created in last sync (should be recent)
SELECT TOP 20 user_id, created_at, created_date_time, 
       DATEDIFF(DAY, created_date_time, GETUTCDATE()) AS days_old
FROM tap_targets
ORDER BY created_at DESC

-- Count by age bucket
SELECT 
  CASE 
    WHEN DATEDIFF(DAY, created_date_time, GETUTCDATE()) <= 7 THEN '0-7 days'
    WHEN DATEDIFF(DAY, created_date_time, GETUTCDATE()) <= 30 THEN '8-30 days'
    WHEN DATEDIFF(DAY, created_date_time, GETUTCDATE()) <= 90 THEN '31-90 days'
    ELSE '90+ days'
  END AS age_bucket,
  COUNT(*) as count
FROM tap_targets
GROUP BY 
  CASE 
    WHEN DATEDIFF(DAY, created_date_time, GETUTCDATE()) <= 7 THEN '0-7 days'
    WHEN DATEDIFF(DAY, created_date_time, GETUTCDATE()) <= 30 THEN '8-30 days'
    WHEN DATEDIFF(DAY, created_date_time, GETUTCDATE()) <= 90 THEN '31-90 days'
    ELSE '90+ days'
  END
ORDER BY age_bucket
```

## Critical Differences from Deletion Service Fix

**Deletion Service (SQL Query):**
- Issue: WHERE clause returned 0 records from database
- Fix: Verify SQL WHERE condition in repository

**Inbound Sync (Graph Query):**
- Issue: Graph API filter not working or not sent correctly
- Fix: Verify OData filter format, date calculation, and Graph SDK parameter binding
- Key: Graph is EXTERNAL API, so filter must be perfectly formatted
- Graph SDK sometimes silently ignores malformed filters (doesn't throw error)

## ROOT CAUSE IDENTIFIED & FIXED ✅

### The Bug
**File**: `backend/src/TapApi.Services/External/GraphService.cs` (Line 315-328)
**Method**: `GetUsersAsync()`

```csharp
// BEFORE (BROKEN):
var queryConfig = GraphQueryBuilders.InboundSync.CreateQueryConfig(filter ?? "", pageSize);
config.QueryParameters.Select = queryConfig.SelectFields;
config.QueryParameters.Expand = queryConfig.ExpandRelations;
// ❌ FILTER WAS NEVER APPLIED!

// AFTER (FIXED):
var queryConfig = GraphQueryBuilders.InboundSync.CreateQueryConfig(filter ?? "", pageSize);
config.QueryParameters.Select = queryConfig.SelectFields;
config.QueryParameters.Expand = queryConfig.ExpandRelations;
config.QueryParameters.Filter = queryConfig.Filter;  // ✅ NOW APPLIED!
```

### Why This Broke Inbound Sync
1. `GraphQueryBuilders.InboundSync.BuildFilter()` correctly created filter: `"userType eq 'Member' and createdDateTime ge 2026-03-23T..."`
2. Filter was embedded in `queryConfig.Filter`
3. But `config.QueryParameters.Filter` was NEVER SET
4. Graph API received request WITHOUT filter
5. Graph API returned ALL users (no creation window filter applied)
6. Inbound sync created targets for ALL users, including those 100+ days old
7. Deletion service then correctly cleaned them up, but sync kept adding stale records

### The Fix
Added single line 320:
```csharp
config.QueryParameters.Filter = queryConfig.Filter;
```

Now the Graph OData filter is properly passed to Graph API.

### Verification Steps Completed
- ✅ Identified filter NOT being applied to config.QueryParameters
- ✅ Located exact line where filter should be set
- ✅ Built fix and recompiled successfully
- ✅ Restarted function with fixed code
- ✅ Triggered new inbound sync (testing underway)

## Autonomous Agent Notes
- Follow iteration loop EXACTLY
- Do NOT skip steps - each provides critical diagnostic data
- CRITICAL: Capture actual Graph filter string from logs (step 3)
- CRITICAL: Verify Graph API response count vs expected count (step 4)
- Compare database records BEFORE and AFTER sync (step 5)
- When in doubt, add logging: `_logger.LogWarning("[INBOUNDSYNC-DEBUG] FilterUsed={Filter}", filter);`
- Graph Explorer (https://developer.microsoft.com/en-us/graph/graph-explorer) useful for manual testing
