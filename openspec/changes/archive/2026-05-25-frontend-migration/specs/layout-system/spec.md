# Spec: Layout System

## Overview

Four switchable layout modes, all sharing the same sidebar menu data, header, and tab bar — just arranged differently.

## Layout Modes

### Default (经典侧边栏)
```
┌──────────────────────────────────────────┐
│ Sidebar(230px) │  Header + Breadcrumb    │
│                │  Tabs Bar               │
│  Logo          │  ┌──────────────────┐   │
│  Menu          │  │                  │   │
│  (vertical)    │  │  Main Content    │   │
│                │  │                  │   │
│                │  └──────────────────┘   │
└──────────────────────────────────────────┘
```
- Sidebar collapses to icon-only (68px) on toggle
- Sidebar hidden on mobile (<1024px), accessible via hamburger

### Mix (混合布局)
```
┌──────────────────────────────────────────┐
│  Header: Logo + Horizontal Menu(1st) + RightBar │
│ Sidebar(200px) │  Tabs Bar              │
│  Vertical Menu │  ┌──────────────────┐  │
│  (2nd level)   │  │  Main Content    │  │
│                │  └──────────────────┘  │
└──────────────────────────────────────────┘
```
- First-level routes in horizontal header menu
- Second-level routes in left sidebar
- Sidebar hidden on mobile

### Columns (双栏布局)
```
┌──────────────────────────────────────────┐
│ Icons │ Sidebar(200px) │ Header + Tabs   │
│ (68px)│                │ ┌────────────┐  │
│       │ Menu           │ │ Main       │  │
│       │ (vertical)     │ │ Content    │  │
│       │                │ └────────────┘  │
└──────────────────────────────────────────┘
```
- Icon column shows top-level route icons with labels below
- Clicking an icon switches the secondary sidebar content
- Entire left section hidden on mobile

### Top (顶部导航)
```
┌──────────────────────────────────────────┐
│  Logo + Horizontal Menu + RightBar       │
│  Tabs Bar                                │
│  ┌────────────────────────────────────┐  │
│  │  Main Content                      │  │
│  └────────────────────────────────────┘  │
└──────────────────────────────────────────┘
```
- No sidebar at all
- All navigation via horizontal menu

## Shared Components

| Component | Description |
|-----------|-------------|
| Sidebar | Collapsible, scrollable menu with icons, recursive rendering |
| Header | Breadcrumb (hidden on xs) + right bar |
| TabsBar | Browser-style tabs with context menu (close/reload/close-others/close-left/close-right/close-all) |
| UserDropdown | Avatar + name → profile, messages, logout |
| ThemeToggle | Dark/light switch |
| SettingDrawer | Layout mode selector, theme color, animation toggle, tab mode |
| Logo | App logo + title, responsive sizing |

## State (in useAppStore)

```typescript
layout: 'default' | 'mix' | 'columns' | 'top'
menuCollapse: boolean
menuDark: boolean
tab: boolean          // show/hide tabs
tabMode: 'line' | 'card' | 'capsule'
animateMode: 'fade' | 'slide' | 'zoom' | 'none'
theme: 'light' | 'dark'
themeColor: string    // custom primary color override
```

## Responsive Rules

| Breakpoint | Behavior |
|-----------|----------|
| <1024px (mobile) | Sidebar hidden, hamburger menu, breadcrumb hidden |
| 1024-1200px | Sidebar auto-collapses to icon mode |
| >1200px | Full sidebar |
