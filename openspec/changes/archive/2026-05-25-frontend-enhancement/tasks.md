# Tasks: Frontend Enhancement

## 1. Dashboard Enhancement (仪表盘增强)

### 1.1 Analysis Page — Charts
- [x] Install `recharts` dependency
- [x] Create `src/components/chart.tsx` — recharts wrapper with Tesla theme colors
- [x] Implement DataOverview stats (PV, IP, Demo1, Demo2 cards)
- [x] Implement Geo chart (地区分布)
- [x] Implement Os chart (操作系统分布)
- [x] Implement Browser chart (浏览器分布)
- [x] Implement Module chart (模块使用分布)
- [x] Implement AccessTimeslot chart (访问时段)

### 1.2 Workplace Page — Full Layout
- [x] Enhance Welcome component (avatar, greeting, quote)
- [x] Implement Project cards (6 project links with status badges)
- [x] Implement QuickOperation (permission-gated shortcut links)
- [x] Implement LatestActivity component
- [x] Implement Carousel component
- [x] Implement Docs component (document links)
- [x] Redesign as two-column grid layout

---

## 2. File Management Enhancement (文件管理增强)

- [x] Implement file type sidebar with tree filter + storage statistics
- [x] Implement folder hierarchy breadcrumb navigation
- [x] Implement grid/list view toggle
- [x] Implement create folder functionality
- [x] Implement batch select + batch delete
- [x] Implement right-click context menu (delete/rename/detail/download)
- [x] Implement FileDetailModal
- [x] Implement FileRenameModal
- [x] Implement chunked multipart upload with MD5 hashing
- [x] Implement download functionality
- [x] Implement RecycleBinModal (recycle bin)
- [x] Implement audio/video preview modals
- [x] Implement image viewer (lightbox)

---

## 3. User Profile Enhancement (个人中心增强)

- [x] Implement avatar cropper (react-easy-crop)
- [x] Implement BasicInfoUpdateModal (edit profile dialog)
- [x] Implement Security card (phone/email/password bind status)
- [x] Implement Social card (Gitee/GitHub/WeChat bind/unbind)
- [x] Enhance layout to two-column card design
- [x] Show department, roles, registration date

---

## 4. User Message Enhancement (消息中心增强)

- [x] Implement tab-based layout (MyMessage + MyNotice)
- [x] Add unread count badges on tabs
- [x] Implement type/status filters
- [x] Implement batch delete + batch mark-read
- [x] Implement "mark all as read" button
- [x] Implement notice detail view navigation

---

## 5. Code Generator Enhancement (代码生成器增强)

- [x] Implement batch selection with checkboxes
- [x] Implement batch generate download
- [x] Implement GenConfigDrawer (generation config form)
- [x] Implement field config table (editable names/types/comments)
- [x] Implement field config options (show-in-list/form/query, form type, query type, dict code)
- [x] Implement sync from DB functionality
- [x] Enhance GenPreviewModal with tree-based file browser
- [x] Implement syntax highlighting in preview
- [x] Implement copy-to-clipboard in preview

---

## 6. Login Page Enhancement (登录页增强)

- [x] Implement responsive desktop/mobile split layout (left banner + right form)
- [x] Implement animated background component
- [x] Implement tenant detection from domain
- [x] Add copyright footer
- [x] Add theme toggle button
- [x] Create `src/views/login/pwd-expired.tsx` — password expiry page

---

## 7. Missing Pages (缺失页面)

- [x] Create `src/views/about/index.tsx` — About page
- [x] Create `src/views/about/document/api.tsx` — API documentation page
- [x] Create `src/views/about/document/changelog.tsx` — Changelog page
- [x] Create `src/views/redirect/index.tsx` — Redirect helper page
- [x] Update router to include new routes
