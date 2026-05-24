# Spec: Dynamic Routing

## Overview

Routes are driven by the backend menu system. The frontend fetches route definitions from `/auth/user/route` and dynamically registers them as React Router routes.

## API Contract

**Endpoint**: `GET /auth/user/route`
**Response**: `ApiRes<RouteItem[]>`

```typescript
interface RouteItem {
  path: string          // e.g. '/system/user'
  component: string     // e.g. 'system/user' — maps to src/views/system/user/index.tsx
  meta: {
    title: string       // Display name
    icon?: string       // Lucide icon name
    svgIcon?: string    // Custom SVG icon name
    hidden?: boolean    // Hide from sidebar
    alwaysShow?: boolean // Force show as sub-menu even with single child
    affix?: boolean     // Pin as fixed tab (cannot close)
    cache?: boolean     // Enable keep-alive style caching
    badge?: string      // Badge text (e.g. "New")
  }
  children?: RouteItem[]
}
```

## Behavior

1. On login success, fetch `/auth/user/route`
2. Recursively map each route item:
   - `component` string → `lazy(() => import(`../views/${component}/index.tsx`))`
   - Flatten single-child routes (parent takes child's path/title/icon unless `alwaysShow`)
3. Register routes under the layout wrapper in React Router
4. Store flat route list in `useRouteStore` for breadcrumb and tab generation
5. Sidebar menu renders from the route tree, filtering `meta.hidden === true`

## Fallback

If a `component` string has no matching file in `src/views/`, render a "Page Not Found" placeholder instead of crashing.

## Menu Rendering Rules

- Single child + no `alwaysShow` → render as parent menu item (flatten)
- Multiple children → render as sub-menu group
- `meta.icon` → render Lucide icon; `meta.svgIcon` → render custom SVG
- `meta.badge` → show badge next to menu item text
- Routes with `meta.hidden === true` → excluded from sidebar but still accessible by URL
