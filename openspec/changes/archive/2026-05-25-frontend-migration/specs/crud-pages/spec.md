# Spec: CRUD Pages Pattern

## Overview

All system management pages follow a consistent CRUD pattern. This spec defines the shared behavior.

## Standard Page Structure

```
┌─────────────────────────────────────────────┐
│ Page Header: Title + Action Buttons (新增/导出) │
├─────────────────────────────────────────────┤
│ Search Form: [field1] [field2] [搜索] [重置]   │
├─────────────────────────────────────────────┤
│ Table Toolbar: [批量删除] [列设置]              │
├─────────────────────────────────────────────┤
│ Data Table:                                 │
│  ┌───┬──────┬──────┬──────┬──────┬───────┐  │
│  │ ☐ │ 列1  │ 列2  │ 列3  │ 列4  │ 操作   │  │
│  ├───┼──────┼──────┼──────┼──────┼───────┤  │
│  │ ☐ │ data │ data │ data │ data │ 编辑   │  │
│  │   │      │      │      │      │ 删除   │  │
│  └───┴──────┴──────┴──────┴──────┴───────┘  │
├─────────────────────────────────────────────┤
│ Pagination: < 1 2 3 ... 10 > 共100条        │
└─────────────────────────────────────────────┘
```

## Data Table Features

- **Column sorting** — click header to sort
- **Column visibility** — toggle columns via toolbar
- **Row selection** — checkbox column for batch operations
- **Pagination** — page number + page size selector
- **Search** — collapsible search form above table
- **Batch operations** — delete selected, export selected

## Form Dialog

Create/Edit uses a dialog (not a separate page):
- Title: "新增XXX" or "编辑XXX"
- Fields generated from schema (zod validation)
- Submit → API call → refresh table → close dialog
- Cancel → close dialog without action

## Delete Confirmation

- Single delete: "确定删除该记录？"
- Batch delete: "确定删除选中的 N 条记录？"
- Uses AlertDialog component

## API Pattern

All CRUD modules follow:
```
GET    /module          → paginated list (query: page, size, sort, ...filters)
GET    /module/{id}     → single record
POST   /module          → create
PUT    /module/{id}     → update
DELETE /module          → batch delete (body: { ids: [] })
```

## Tree Pages (Menu, Dept)

- Use tree table instead of flat table
- Expand/collapse all button
- Parent-child indentation
- Drag to reorder (optional)

## Permission Integration

Each page checks permissions before showing action buttons:
```tsx
{has('system:user:create') && <Button>新增</Button>}
{has('system:user:delete') && <Button variant="destructive">删除</Button>}
{has('system:user:update') && <Button onClick={onEdit}>编辑</Button>}
```
