# Tasks: Frontend Migration

## Phase 1: Foundation (基础骨架)

### 1.1 Project Setup & Dependencies
- [x] Install core dependencies: `react-router-dom`, `zustand`, `axios`, `jsencrypt`, `dayjs`, `@tanstack/react-table`
- [x] Install Shadcn components (Phase 1): `sidebar`, `breadcrumb`, `tabs`, `dropdown-menu`, `avatar`, `separator`, `input`, `label`, `card`, `dialog`, `sheet`, `tooltip`, `scroll-area`, `badge`, `switch`, `select`
- [x] Create `.env` file with `VITE_API_BASE_URL`, `VITE_API_PREFIX`
- [x] Clean up `App.css` (remove Vite template leftovers)

### 1.2 HTTP Client & API Types
- [x] Create `src/types/api.d.ts` — `ApiRes<T>`, `PageRes<T>`, `PageQuery` types
- [x] Create `src/apis/http.ts` — Axios instance with request/response interceptors
- [x] Implement token injection (`Authorization: Bearer <token>`)
- [x] Implement tenant header injection (`X-Tenant-Id`)
- [x] Implement 401 auto-logout with redirect to `/login`
- [x] Implement error handling (short msg → toast, long msg → toast with description)
- [x] Create `src/lib/auth.ts` — `getToken()`, `setToken()`, `removeToken()`

### 1.3 Zustand Stores
- [x] Create `src/stores/user.ts` — token, userInfo, roles, permissions, login/logout actions
- [x] Create `src/stores/app.ts` — layout, theme, menuCollapse, tabMode, animateMode (persist to localStorage)
- [x] Create `src/stores/tabs.ts` — openTabs, cacheList, addTab, removeTab, closeOthers (persist to sessionStorage)
- [x] Create `src/stores/route.ts` — dynamicRoutes, flatRoutes, generateRoutes from backend
- [x] Create `src/stores/dict.ts` — dict cache with fetch-on-demand
- [x] Create `src/stores/tenant.ts` — tenantId, enabled, code

### 1.4 Auth API & Login Page
- [x] Create `src/apis/auth.ts` — login (account/phone/email/social), logout, getUserInfo, getUserRoute
- [x] Create `src/views/login/index.tsx` — login page with 4 methods (account, phone, email, social)
- [x] Implement RSA password encryption with jsencrypt
- [x] Implement captcha integration (`/captcha` API)
- [x] Implement social OAuth redirect flow (`/auth/{source}`)
- [x] Create `src/views/login/social-callback.tsx` — OAuth callback handler
- [x] Implement responsive login layout (mobile/desktop)

### 1.5 Router & Dynamic Routes
- [x] Create `src/app/router.tsx` — route configuration with lazy loading
- [x] Implement `import.meta.glob` for view module discovery
- [x] Implement dynamic route registration from `/auth/user/route` response
- [x] Create route guard component — checks auth, redirects to login if no token
- [x] Implement route flattening for breadcrumb and tabs
- [x] Create static routes: `/login`, `/social/callback`, `/403`, `/404`, `/:pathMatch(.*)*`
- [x] Create system routes: `/dashboard/workplace`, `/dashboard/analysis`, `/user/profile`, `/user/message`

### 1.6 Layout System
- [x] Create `src/layouts/index.tsx` — LayoutSwitcher reading `useAppStore.layout`
- [x] Create `src/layouts/default-layout.tsx` — classic sidebar (230px) + header + tabs + main
- [x] Create `src/layouts/mix-layout.tsx` — top nav (first-level) + left sidebar (second-level) + main
- [x] Create `src/layouts/columns-layout.tsx` — icon sidebar (68px) + text sidebar (200px) + header + tabs + main
- [x] Create `src/layouts/top-layout.tsx` — full horizontal nav + tabs + main
- [x] Create `src/layouts/components/sidebar.tsx` — collapsible menu with icons, recursive rendering
- [x] Create `src/layouts/components/header.tsx` — breadcrumb + right bar
- [x] Create `src/layouts/components/tabs-bar.tsx` — tab navigation with context menu (close/reload/close-others)
- [x] Create `src/layouts/components/breadcrumb.tsx` — route-based breadcrumb
- [x] Create `src/layouts/components/user-dropdown.tsx` — avatar, profile, messages, logout
- [x] Create `src/layouts/components/theme-toggle.tsx` — dark/light switch
- [x] Create `src/layouts/components/setting-drawer.tsx` — layout/theme/animation configuration
- [x] Create `src/layouts/components/logo.tsx` — app logo/title

### 1.7 Error Pages
- [x] Create `src/views/errors/403.tsx` — Forbidden page
- [x] Create `src/views/errors/404.tsx` — Not Found page

---

## Phase 2: Core CRUD Pages (核心管理页面)

### 2.1 Shared CRUD Components
- [x] Create `src/components/data-table.tsx` — @tanstack/react-table + Shadcn Table, with toolbar, column toggle, sorting, pagination
- [x] Create `src/components/crud-form.tsx` — dialog-based form with zod validation, create/edit modes
- [x] Create `src/components/search-form.tsx` — collapsible search form with reset
- [x] Create `src/components/delete-confirm.tsx` — AlertDialog-based delete confirmation
- [x] Create `src/hooks/use-crud.ts` — generic CRUD hook (list, create, update, delete, pagination, search)
- [x] Create `src/hooks/use-dict.ts` — dict data fetching hook

### 2.2 System — User Management
- [x] Create `src/apis/system/user.ts` — CRUD + export/import/password/role APIs
- [x] Create `src/views/system/user/index.tsx` — user list with CRUD, search, batch delete, export
- [x] Implement batch delete, export

### 2.3 System — Role Management
- [x] Create `src/apis/system/role.ts` — CRUD + permission tree + user assignment APIs
- [x] Create `src/views/system/role/index.tsx` — role list page with CRUD and permission config

### 2.4 System — Menu Management
- [x] Create `src/apis/system/menu.ts` — CRUD + tree + dict APIs
- [x] Create `src/views/system/menu/index.tsx` — menu tree list page with expand/collapse

### 2.5 System — Department Management
- [x] Create `src/apis/system/dept.ts` — CRUD + tree + dict APIs
- [x] Create `src/views/system/dept/index.tsx` — dept tree list page

### 2.6 System — Dictionary Management
- [x] Create `src/apis/system/dict.ts` — CRUD + dict item APIs
- [x] Create `src/views/system/dict/index.tsx` — dict list with item detail panel (two-panel layout)

### 2.7 System — File Management
- [x] Create `src/apis/system/file.ts` — upload, CRUD, statistics APIs
- [x] Create `src/views/system/file/index.tsx` — file list with upload and preview

### 2.8 System — Notice Management
- [x] Create `src/apis/system/notice.ts` — CRUD APIs
- [x] Create `src/views/system/notice/index.tsx` — notice list page with CRUD

---

## Phase 3: Monitor & Config (监控与配置)

### 3.1 Monitor — Login Log
- [x] Create `src/apis/monitor/log.ts` — log list + detail + export APIs
- [x] Create `src/views/monitor/log/login/index.tsx` — login log list with detail view

### 3.2 Monitor — Operation Log
- [x] Create `src/views/monitor/log/operation/index.tsx` — operation log list with detail view

### 3.3 Monitor — Online Users
- [x] Create `src/apis/monitor/online.ts` — list + kickout APIs
- [x] Create `src/views/monitor/online/index.tsx` — online user list with kickout action

### 3.4 System Config — Site
- [x] Create `src/apis/system/option.ts` — option CRUD APIs
- [x] Create `src/views/system/config/site/index.tsx` — site config form

### 3.5 System Config — Login
- [x] Create `src/views/system/config/login/index.tsx` — login config form

### 3.6 System Config — OAuth Client
- [x] Create `src/apis/system/client.ts` — client CRUD APIs
- [x] Create `src/views/system/config/client/index.tsx` — OAuth client CRUD page

### 3.7 System Config — Mail
- [x] Create `src/views/system/config/mail/index.tsx` — mail config form

### 3.8 System Config — SMS
- [x] Create `src/apis/system/sms.ts` — SMS config APIs
- [x] Create `src/views/system/config/sms/index.tsx` — SMS config form

### 3.9 System Config — Storage
- [x] Create `src/apis/system/storage.ts` — storage config APIs
- [x] Create `src/views/system/config/storage/index.tsx` — storage config form

### 3.10 System Config — Security
- [x] Create `src/views/system/config/security/index.tsx` — security config form

### 3.11 Monitor — SMS Log
- [x] Create `src/views/monitor/sms/log/index.tsx` — SMS log list

---

## Phase 4: Advanced Features (高级功能)

### 4.1 Schedule — Job Management
- [x] Create `src/apis/schedule/job.ts` — job CRUD + trigger + group APIs
- [x] Create `src/views/schedule/job/index.tsx` — job list with status toggle, trigger, CRUD

### 4.2 Schedule — Job Log
- [x] Create `src/apis/schedule/log.ts` — log list + stop + retry APIs
- [x] Create `src/views/schedule/log/index.tsx` — job log list with stop/retry

### 4.3 Tenant — Management
- [x] Create `src/apis/tenant/index.ts` — tenant CRUD + admin pwd APIs
- [x] Create `src/views/tenant/management/index.tsx` — tenant list page with CRUD and reset password

### 4.4 Tenant — Package
- [x] Create `src/apis/tenant/package.ts` — package CRUD + menu tree APIs
- [x] Create `src/views/tenant/package/index.tsx` — tenant package list with CRUD

### 4.5 Open Platform — App Management
- [x] Create `src/apis/open/app.ts` — app CRUD APIs
- [x] Create `src/views/open/app/index.tsx` — open API app CRUD page

### 4.6 Code Generator
- [x] Create `src/apis/code/generator.ts` — generator APIs
- [x] Create `src/views/code/generator/index.tsx` — code generator page with preview and download

---

## Phase 5: Dashboard & User Center (仪表盘与个人中心)

### 5.1 Dashboard — Workplace
- [x] Create `src/apis/common/dashboard.ts` — overview + chart + notice APIs
- [x] Create `src/views/dashboard/workplace/index.tsx` — workplace dashboard with stats cards and notices

### 5.2 Dashboard — Analysis
- [x] Create `src/views/dashboard/analysis/index.tsx` — data analysis page with stat cards and chart placeholder

### 5.3 User — Profile
- [x] Create `src/apis/user/profile.ts` — profile update APIs
- [x] Create `src/views/user/profile/index.tsx` — user profile page with edit and password change

### 5.4 User — Message Center
- [x] Create `src/apis/user/message.ts` — message APIs
- [x] Create `src/views/user/message/index.tsx` — message list page with read/delete actions

### 5.5 Tesla Design Polish
- [x] Apply Tesla color tokens to all Shadcn components via CSS variables (Electric Blue #3E6AE1 primary)
- [x] Ensure all buttons use 4px radius (--radius: 4px in index.css)
- [x] Implement dark mode with Carbon Dark background
- [x] Tab bar styling (flat, no shadows, active indicator)
- [x] Sidebar styling (clean, no decorative elements)
