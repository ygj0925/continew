# Design: Frontend Migration

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                          App Root                                │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │  Providers (QueryClient, Theme, Router, Toaster)          │  │
│  │  ┌─────────────────────────────────────────────────────┐  │  │
│  │  │  Router                                              │  │  │
│  │  │  ├─ /login          → LoginPage                     │  │  │
│  │  │  ├─ /social/callback → SocialCallback               │  │  │
│  │  │  ├─ /               → AuthGuard → Layout            │  │  │
│  │  │  │   ├─ /dashboard/workplace                        │  │  │
│  │  │  │   ├─ /system/user                                │  │  │
│  │  │  │   ├─ /system/role                                │  │  │
│  │  │  │   ├─ ... (dynamic routes from backend)           │  │  │
│  │  │  │   └─ /user/profile                               │  │  │
│  │  │  ├─ /403             → ForbiddenPage                │  │  │
│  │  │  └─ /*               → NotFoundPage                 │  │  │
│  │  └─────────────────────────────────────────────────────┘  │  │
│  └───────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

## Key Design Decisions

### 1. Dynamic Routing Strategy

**Decision**: Use `import.meta.glob` to eagerly import all view modules at build time, then dynamically register routes from backend response at runtime.

**How it works**:
```typescript
// src/app/router.tsx
const viewModules = import.meta.glob('../views/**/*.tsx')

// Backend returns: [{ path: '/system/user', component: 'system/user', meta: { title: '用户管理' } }]
// Map component string to lazy module:
function resolveComponent(component: string) {
  const key = `../views/${component}/index.tsx`
  return viewModules[key] ? lazy(viewModules[key] as any) : NotFound
}
```

**Why**: This mirrors the Vue approach exactly. The backend controls menu/route structure; the frontend maps `component` strings to actual files. `import.meta.glob` is Vite-native and works in React the same as Vue.

### 2. Layout System

**Decision**: Single `Layout` wrapper component that switches between 4 modes via a `layout` field in the app store.

```
src/layouts/
├── index.tsx              # LayoutSwitcher - reads appStore.layout
├── default-layout.tsx     # Sidebar(230px) + Header + Tabs + Main
├── mix-layout.tsx         # Top nav (first-level) + Left sidebar (second-level) + Main
├── columns-layout.tsx     # Icon sidebar(68px) + Text sidebar(200px) + Header + Tabs + Main
├── top-layout.tsx         # Full horizontal nav + Tabs + Main
└── components/
    ├── sidebar.tsx        # Collapsible sidebar with menu
    ├── header.tsx         # Breadcrumb + right bar
    ├── tabs-bar.tsx       # Browser-style tab navigation
    ├── breadcrumb.tsx     # Route-based breadcrumb
    ├── user-dropdown.tsx  # Avatar + profile/logout
    ├── theme-toggle.tsx   # Dark/light switch
    └── setting-drawer.tsx # Layout/theme configuration
```

**Layout switching**: The `LayoutSwitcher` reads `useAppStore(s => s.layout)` and renders the corresponding layout. All 4 layouts share the same sidebar menu data, header components, and tab bar — just arranged differently.

### 3. State Management Architecture

**Decision**: 6 Zustand stores with selective persistence.

```typescript
// Stores with persistence:
useUserStore   → localStorage (token, userInfo, roles, permissions)
useAppStore    → localStorage (layout, theme, menuCollapse, tabMode, animateMode)
useTabsStore   → sessionStorage (openTabs, cacheList)
useRouteStore  → localStorage (dynamicRoutes, flatRoutes)
useDictStore   → no persistence (in-memory cache)
useTenantStore → no persistence (derived from login response)
```

**Why Zustand over Redux/Jotai**:
- Minimal boilerplate (no actions/reducers)
- Built-in `persist` middleware for localStorage/sessionStorage
- `subscribeWithSelector` for fine-grained reactivity
- Tiny bundle size (~1KB)

### 4. HTTP Client Design

**Decision**: Axios instance with interceptors, matching frontend-bck behavior exactly.

```typescript
// src/apis/http.ts
const http = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL,
  timeout: 30_000,
})

// Request: attach Authorization + X-Tenant-Id
// Response: unwrap ApiRes<T>, handle 401 → redirect to /login
// Error: short msg → toast.error(), long msg → toast.error() with description
```

**API response type**:
```typescript
interface ApiRes<T> {
  code: number      // 0 = success, 1 = failure
  data: T
  msg: string
  success: boolean
  timestamp: string
}

interface PageRes<T> {
  list: T[]
  total: number
}
```

### 5. Component Architecture

**Shadcn components to install** (via `npx shadcn@latest add`):

Phase 1 (layout + auth):
`sidebar`, `breadcrumb`, `tabs`, `dropdown-menu`, `avatar`, `separator`, `input`, `label`, `card`, `dialog`, `sheet`, `tooltip`, `scroll-area`, `badge`, `switch`, `select`

Phase 2 (CRUD pages):
`table`, `form`, `checkbox`, `radio-group`, `textarea`, `popover`, `command`, `calendar`, `date-picker`, `pagination`, `alert-dialog`, `sonner`

Phase 3 (advanced):
`accordion`, `collapsible`, `progress`, `skeleton`, `toggle`, `toggle-group`, `slider`, `toast`

**Custom business components**:
- `DataTable` — wraps @tanstack/react-table with Shadcn Table, adds toolbar, column toggle, pagination
- `CrudForm` — dialog-based form with zod validation, create/edit modes
- `IconSelector` — lucide icon picker with search
- `UserSelect` — async user search with multi-select
- `CronBuilder` — cron expression visual editor
- `RichEditor` — Tiptap-based rich text
- `FileUpload` — chunked upload with MD5 hashing
- `FilePreview` — PDF/Word/Excel/ImageViewer
- `Chart` — recharts wrapper with theme integration

### 6. Authentication Flow

```
┌──────────┐     ┌───────────┐     ┌──────────────┐     ┌─────────────┐
│  Login   │────▶│ POST /auth│────▶│ Store token  │────▶│ Fetch user  │
│  Page    │     │ /login    │     │ + tenantId   │     │ info + route│
└──────────┘     └───────────┘     └──────────────┘     └──────┬──────┘
                                                               │
                                                               ▼
                                                     ┌─────────────────┐
                                                     │ Register dynamic│
                                                     │ routes + render │
                                                     │ layout          │
                                                     └─────────────────┘
```

- Token stored in localStorage via `useUserStore`
- Axios interceptor reads token from store on each request
- 401 response → clear store → redirect to `/login?redirect=<current_path>`
- Password encrypted with jsencrypt (RSA) before sending

### 7. Theme System

**Decision**: CSS variables + Tailwind, matching the existing `index.css` theme tokens.

```css
/* Light mode (default) */
--background: oklch(1 0 0);           /* Pure White */
--foreground: oklch(0.15 0.005 250);  /* Carbon Dark #171A20 */
--primary: oklch(0.55 0.18 265);      /* Electric Blue #3E6AE1 */
--primary-foreground: oklch(1 0 0);   /* White */
--muted: oklch(0.96 0 0);             /* Light Ash #F4F4F4 */
--border: oklch(0.92 0 0);            /* Cloud Gray #EEEEEE */

/* Dark mode */
--background: oklch(0.15 0.005 250);  /* Carbon Dark */
--foreground: oklch(0.95 0 0);        /* Near white */
--primary: oklch(0.55 0.18 265);      /* Same Electric Blue */
```

Dark mode toggled by adding `dark` class to `<html>`, using Tailwind's `dark:` variant.

### 8. Tab Navigation & Keep-Alive

**Decision**: Zustand store tracks open tabs; React doesn't have `<keep-alive>`, so use a custom cache strategy.

```typescript
// When navigating away from a tab:
// 1. Keep the component mounted but hidden (display: none)
// 2. On return, show it again without re-rendering
// This is the React equivalent of Vue's <keep-alive>

// For "reload" action: unmount and remount the component (key change)
```

Tab operations: close current, close others, close left, close right, close all, reload. Affix tabs (like dashboard) cannot be closed.

### 9. Permission Model

```typescript
// usePermission hook
function usePermission() {
  const permissions = useUserStore(s => s.permissions) // e.g. ['system:user:create', 'system:user:delete']
  
  return {
    has: (perm: string) => permissions.includes('*') || permissions.includes(perm),
    hasAny: (perms: string[]) => perms.some(p => has(p)),
    hasAll: (perms: string[]) => perms.every(p => has(p)),
  }
}

// Usage in components:
const { has } = usePermission()
{has('system:user:create') && <Button>新增用户</Button>}
```
