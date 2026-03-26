---
applyTo: "frontend/**/*"
---

# Frontend Copilot Instructions — React + SPFx + Fluent UI v9

## Stack Overview

| Aspect | Value |
|--------|-------|
| **React** | 17.0.1 |
| **TypeScript** | ~5.3.3 (strict mode) |
| **Build (Dev/Standalone)** | Vite ^7.1.4 |
| **Build (SharePoint)** | Heft (SPFx 1.22.2) |
| **UI Library** | Fluent UI v9 (`@fluentui/react-components`) |
| **Styling** | `makeStyles()` CSS-in-JS (Fluent UI) + CSS Modules (`.module.scss`) |
| **State Management** | React Context API + custom hooks (no Redux/Zustand) |
| **Auth** | MSAL (`@azure/msal-react`) + `@gisc/spfx-sdk` `useAuth` |
| **API Client** | Axios via `tapApiClient.ts` |
| **Localization** | `@gisc/spfx-sdk` `useLocalization` hook |

---

## 1. TypeScript Strictness Rules

The `tsconfig.json` in `frontend/packages/spfx-sdk/` enables `"strict": true`. The SPFx build additionally enforces TypeScript strictness via `frontend/config/typescript.json` (which extends the SPFx rig profile). All code must comply:

- **No `any`** — use specific types or `unknown` with narrowing.
- **No implicit returns** — all code paths in a function must return a value.
- **Strict null checks** — guard against `null` / `undefined` before use.
- **`React.FC<Props>`** — always type functional components with a named `Props` interface.
- Import React explicitly: `import * as React from 'react';`

```ts
// ✅ Correct
const count: number = 0;
const name: string | null = null;

// ❌ Wrong
const count: any = 0;
const name = null; // inferred as `null` type only
```

---

## 2. Component Structure

### 2.1 File Layout (folder-per-component)

Each non-trivial component lives in its own folder:

```
shared/components/[category]/[ComponentName]/
├── [ComponentName].tsx          # Component implementation
├── [ComponentName].types.ts     # Props interface and shared types
├── [ComponentName].module.scss  # CSS Module styles (if not using makeStyles)
├── index.ts                     # Re-export barrel
└── __tests__/                   # Jest unit tests
    └── [ComponentName].test.tsx
```

**Barrel `index.ts` pattern:**

```ts
export { ComponentName } from './ComponentName';
export type { ComponentNameProps } from './ComponentName.types';
```

### 2.2 Component Categories

| Folder | Contents |
|--------|---------|
| `shared/components/common/` | Generic reusable UI (modals, loading, error display) |
| `shared/components/tap/` | TAP-domain components (expiry, reissue, welcome letter) |
| `shared/components/navigation/` | Navigation, language switcher, theme switcher |
| `shared/components/user/` | User-related widgets (badges, nomination, delegation) |
| `shared/components/admin/` | Admin dashboard components |
| `react/components/` | Standalone React app-specific components |

### 2.3 Naming Conventions

| Element | Convention | Example |
|---------|-----------|---------|
| **Component file / folder** | PascalCase | `BaseModal/`, `TAPExpiresInPopover.tsx` |
| **Props interface** | `[ComponentName]Props` | `BaseModalProps` |
| **Custom hooks** | `use[Name]` | `useThemeTokens`, `useApiError` |
| **Context files** | `[Name]Context.tsx` | `AppContext.tsx` |
| **Types file** | `[ComponentName].types.ts` | `BaseModal.types.ts` |
| **CSS Module** | `[ComponentName].module.scss` | `BaseModal.module.scss` |
| **Style hook** | `use[Component]Styles` | `useComponentStyles` (via `makeStyles`) |
| **Event handlers** | `on[Event]` (prop), `handle[Event]` (handler) | `onClose`, `handleClose` |
| **Boolean props** | `is[State]` / `has[Feature]` | `isOpen`, `hasError` |

---

## 3. Styling Rules

### 3.1 Preferred: `makeStyles()` for component-level styles

```tsx
import { makeStyles } from '@fluentui/react-components';

const useComponentStyles = makeStyles({
  container: {
    display: 'flex',
    flexDirection: 'column',
    gap: '8px',
    padding: '16px',
  },
  title: {
    fontWeight: 'bold',
    fontSize: '16px',
  },
});
```

- Define `useComponentStyles` at **module level** (outside the component function) for performance.
- Use Fluent spacing tokens (`spacingVerticalM`, etc.) from `useThemeTokens()` when consistency with the design system is required.
- Never use inline `style={{}}` for layout; use `makeStyles`.

### 3.2 CSS Modules (`.module.scss`) for global or complex styles

Use only when `makeStyles` is insufficient (e.g., global animation keyframes, complex selectors):

```scss
// BaseModal.module.scss
.overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
}
```

### 3.3 Theme Access

Access theme tokens via `useThemeTokens()` hook — never hard-code color hex values:

```tsx
const themeTokens = useThemeTokens();
// Use themeTokens.colorNeutralBackground1, themeTokens.colorBrandForeground1, etc.
```

---

## 4. State Management

### 4.1 Contexts (available globally)

| Context | Hook | Purpose |
|---------|------|---------|
| `AppContext` | `useAppContext()` | Auth state, config, unified theme |
| `ServerConfigContext` | `useServerConfig()` | Runtime server configuration |
| `GraphApiContext` | `useGraphApi()` | Microsoft Graph API client |

**Import pattern:**

```tsx
import { useAppContext } from '../../contexts/AppContext';
const { config, theme } = useAppContext();
```

### 4.2 Custom Hooks

Business logic must be extracted into custom hooks in `shared/hooks/`. Rules:

- One concern per hook (single responsibility).
- Hooks must be named `use[Name]`.
- Return a typed object (not a tuple) unless the hook returns exactly two values of different types.
- Handle loading and error states explicitly.

```ts
// shared/hooks/useApiHealth.ts
export function useApiHealth() {
  const [isHealthy, setIsHealthy] = React.useState<boolean | null>(null);
  const [isLoading, setIsLoading] = React.useState(false);

  // ... fetch logic ...

  return { isHealthy, isLoading };
}
```

### 4.3 Local Component State

Use `useState` / `useReducer` for state that does not need to be shared:

```tsx
const [isOpen, setIsOpen] = React.useState(false);
const [errorMessage, setErrorMessage] = React.useState<string | null>(null);
```

---

## 5. Auth & API Call Patterns

### 5.1 Authentication

Use `useAuth()` from `@gisc/spfx-sdk` to get the MSAL instance and active account:

```tsx
import { useAuth } from '@gisc/spfx-sdk';

const { msalInstance, activeAccount, loading } = useAuth();
```

Acquire tokens silently before calling any API:

```ts
const token = await msalInstance?.acquireTokenSilent({
  scopes: ['https://graph.microsoft.com/.default'],
  account: activeAccount,
});
```

### 5.2 TAP API Client

Use `tapApiClient` from `shared/api/tap/tapApiClient.ts`; never create ad-hoc Axios instances in components.

### 5.3 Lifecycle Safety

Use `isMountedRef` to prevent state updates on unmounted components:

```tsx
const isMountedRef = React.useRef(true);
React.useEffect(() => {
  return () => { isMountedRef.current = false; };
}, []);

// Inside async callbacks:
if (isMountedRef.current) setState(value);
```

---

## 6. Golden Template — New React Component

Use this as the starting point for every new shared component:

```tsx
// shared/components/[category]/[ComponentName]/[ComponentName].tsx

import * as React from 'react';
import { makeStyles } from '@fluentui/react-components';
import { useThemeTokens } from '../../../hooks/useThemeTokens';
import type { [ComponentName]Props } from './[ComponentName].types';

// --- Styles (defined at module level for perf) ---
const useComponentStyles = makeStyles({
  root: {
    display: 'flex',
    flexDirection: 'column',
    gap: '8px',
  },
  // Add more style rules as needed
});

// --- Component ---
export const [ComponentName]: React.FC<[ComponentName]Props> = ({
  // destructure required props here
}) => {
  const styles = useComponentStyles();
  const themeTokens = useThemeTokens();

  // State
  const [isLoading, setIsLoading] = React.useState(false);
  const [error, setError] = React.useState<string | null>(null);

  // Lifecycle safety ref
  const isMountedRef = React.useRef(true);
  React.useEffect(() => {
    return () => { isMountedRef.current = false; };
  }, []);

  // Handler example
  const handleAction = React.useCallback(async () => {
    try {
      setIsLoading(true);
      // ... perform async operation ...
    } catch (err) {
      if (isMountedRef.current) {
        setError(err instanceof Error ? err.message : 'An unexpected error occurred');
      }
    } finally {
      if (isMountedRef.current) setIsLoading(false);
    }
  }, [/* deps */]);

  if (isLoading) {
    return <div className={styles.root}>Loading…</div>;
  }

  return (
    <div className={styles.root}>
      {error && <span style={{ color: themeTokens.colorStatusDangerForeground1 }}>{error}</span>}
      {/* Component content */}
    </div>
  );
};
```

**Companion `[ComponentName].types.ts`:**

```ts
// shared/components/[category]/[ComponentName]/[ComponentName].types.ts

export interface [ComponentName]Props {
  /** Brief description of each required prop */
  requiredProp: string;
  /** Optional props use `?` */
  onAction?: () => void;
}
```

**Companion `index.ts`:**

```ts
// shared/components/[category]/[ComponentName]/index.ts

export { [ComponentName] } from './[ComponentName]';
export type { [ComponentName]Props } from './[ComponentName].types';
```

**Checklist before committing a new component:**

- [ ] Props typed with `[ComponentName]Props` interface in `.types.ts`
- [ ] `React.FC<Props>` — no implicit `any`
- [ ] `makeStyles` defined at module level (not inside the component)
- [ ] Theme colors via `useThemeTokens()`, not hard-coded hex values
- [ ] `isMountedRef` guard on all async state updates
- [ ] `index.ts` barrel exports the component and its types
- [ ] Unit test in `__tests__/[ComponentName].test.tsx`

---

## 7. Hook Template

```ts
// shared/hooks/use[Name].ts

import * as React from 'react';

export interface Use[Name]Result {
  data: SomeType | null;
  isLoading: boolean;
  error: string | null;
  refresh: () => void;
}

export function use[Name](): Use[Name]Result {
  const [data, setData] = React.useState<SomeType | null>(null);
  const [isLoading, setIsLoading] = React.useState(false);
  const [error, setError] = React.useState<string | null>(null);

  const fetchData = React.useCallback(async () => {
    try {
      setIsLoading(true);
      setError(null);
      // ... fetch ...
      setData(result);
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Unknown error');
    } finally {
      setIsLoading(false);
    }
  }, []);

  React.useEffect(() => {
    void fetchData();
  }, [fetchData]);

  return { data, isLoading, error, refresh: fetchData };
}
```

---

## 8. SPFx vs. Vite Runtime Modes

The frontend supports two runtime environments:

| Mode | Entry | Build tool | Use case |
|------|-------|-----------|---------|
| **SPFx** | `src/webparts/` | Heft | Production SharePoint deployment |
| **Standalone** | `src/react/main.tsx` | Vite | Local development & testing |

Shared code in `src/shared/` must work in **both** modes. Avoid SPFx-only or Vite-only APIs in shared components. Use the `@gisc/spfx-sdk` abstractions (auth, config, theme) which handle both environments.

---

## 9. Testing Conventions

- Tests live in `__tests__/` directories next to the component/hook they test.
- Use Jest (configured via `jest.config.js` in `frontend/packages/spfx-sdk/`) with `@testing-library/react`.
- Test file naming: `[ComponentName].test.tsx` or `[hookName].test.ts`.
- Test the component's behaviour (user interactions, rendered output), not implementation details.
- Mock API calls and external dependencies; never hit real endpoints in unit tests.

---

## 10. Key Files Reference

| File | Purpose |
|------|---------|
| `frontend/src/shared/contexts/AppContext.tsx` | Global context — auth, config, theme |
| `frontend/src/shared/hooks/useThemeTokens.ts` | Fluent UI theme token access |
| `frontend/src/shared/api/tap/tapApiClient.ts` | Central Axios client for TAP API |
| `frontend/src/shared/config/centralConfig.ts` | Central config manager |
| `frontend/src/react/main.tsx` | Vite standalone entry point |
| `frontend/src/webparts/tapApiClientV2/` | SPFx WebPart entry point |
