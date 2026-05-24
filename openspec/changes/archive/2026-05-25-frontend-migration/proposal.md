# Proposal: Frontend Migration (Vue → React)

## Summary

Migrate the entire `frontend-bck` (Vue 3 + Arco Design) admin panel to `frontend` (React 19 + Shadcn/ui + Tailwind CSS 4), replicating all 30+ pages, 4 layout modes, dynamic routing, authentication, and backend integration — with a new Tesla-inspired visual design.

## Motivation

The current `frontend-bck` is a fully functional Continew Admin UI built with Vue 3. The new `frontend` project uses a modern React 19 + Shadcn/ui stack that provides better TypeScript integration, a more composable component model, and access to the broader React ecosystem. The goal is to achieve feature parity while upgrading the visual design to follow the Tesla design system (minimal, flat, single-accent-color aesthetic).

## Scope

### In Scope

- **HTTP client** with JWT auth, tenant headers, error handling, 401 auto-logout
- **6 Zustand stores**: user, app, tabs, route, dict, tenant (with persistence)
- **Dynamic routing** from backend `/auth/user/route` API
- **4 layout modes**: Default (sidebar), Mix, Columns, Top
- **Login page** with 4 methods: account, phone, email, social/OAuth
- **20+ CRUD pages**: user, role, menu, dept, dict, file, notice, config, log, online, schedule, tenant, open API, code generator
- **Dashboard** with charts and statistics
- **User center**: profile, messages, notifications
- **Error pages**: 403, 404
- **Tesla-inspired design**: flat UI, no shadows, Electric Blue (#3E6AE1) accent, 4px radius, 0.33s transitions
- **Dark mode** support
- **Responsive design** (mobile sidebar collapse, breakpoint-aware components)
- **Tab navigation** with keep-alive equivalent, context menu (close/reload)
- **Breadcrumb** navigation
- **Setting drawer** for layout/theme configuration

### Out of Scope

- Mock data / vite-plugin-mock (use real backend)
- Mourning mode / color blindness filters (low priority, can add later)
- WebSocket notifications (backend dependency, add when ready)
- i18n (not present in frontend-bck either)

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Framework | React 19 + TypeScript |
| Build | Vite 8 |
| Styling | Tailwind CSS 4 + Shadcn/ui (radix-nova) |
| Routing | react-router-dom v7 |
| State | Zustand + zustand/middleware/persist |
| HTTP | Axios (familiar, proven) |
| Forms | @tanstack/react-form + zod |
| Tables | @tanstack/react-table |
| Charts | recharts |
| Code Editor | @uiw/react-codemirror |
| Rich Text | Tiptap |
| File Preview | react-pdf, mammoth, xlsx |
| Drag & Drop | @dnd-kit |
| Icons | lucide-react |
| Toasts | sonner (already installed) |

## Design System

Follow Tesla design principles from DESIGN.md:
- **Colors**: Electric Blue (#3E6AE1) primary, Carbon Dark (#171A20) headings, Graphite (#393C41) body, Pure White backgrounds
- **Typography**: Geist font (already installed), 400/500 weights only
- **Borders**: 4px radius on interactive elements, no shadows, no gradients
- **Transitions**: 0.33s cubic-bezier for all state changes
- **Spacing**: 8px base unit
- **Dark mode**: Carbon Dark (#171A20) background, inverted text hierarchy

## Non-Goals

- Pixel-perfect recreation of Arco Design components — the visual identity changes completely
- Server-side rendering (SSR) — this is a SPA admin panel
- Offline support / PWA
- Unit tests in the initial migration (add incrementally after)

## Risks

1. **Dynamic routing complexity** — backend returns route component paths that must map to React lazy-loaded modules. Requires careful `import.meta.glob` equivalent in React.
2. **Component parity** — some Arco Design components (tree-select, cascader, transfer) may not have direct Shadcn equivalents; may need headless Radix + custom styling.
3. **API contract drift** — frontend-bck API calls are the source of truth, but backend may have evolved. Need to verify against live `/v3/api-docs`.
