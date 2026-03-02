# Base Template System - Architecture Overview

## System Architecture

```
┌────────────────────────────────────────────────────────────────┐
│                        base.html (Master)                      │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  <head>                                                  │  │
│  │    - Meta tags                                           │  │
│  │    - base.css (common styles)                            │  │
│  │    - {% block extra_css %} (page-specific styles)        │  │
│  └──────────────────────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  <body>                                                  │  │
│  │    ┌────────────┐  ┌──────────────────────────────┐     │  │
│  │    │  Sidebar   │  │        Main Content           │     │  │
│  │    │            │  │  ┌──────────────────────────┐ │     │  │
│  │    │ - Logo     │  │  │  Header                  │ │     │  │
│  │    │ - Nav Menu │  │  │  - Title                 │ │     │  │
│  │    │ - User     │  │  │  - Date                  │ │     │  │
│  │    │ - Logout   │  │  └──────────────────────────┘ │     │  │
│  │    └────────────┘  │  ┌──────────────────────────┐ │     │  │
│  │                    │  │ Error Container          │ │     │  │
│  │                    │  └──────────────────────────┘ │     │  │
│  │                    │  ┌──────────────────────────┐ │     │  │
│  │                    │  │ {% block content %}      │ │     │  │
│  │                    │  │ PAGE-SPECIFIC CONTENT    │ │     │  │
│  │                    │  └──────────────────────────┘ │     │  │
│  │                    └──────────────────────────────┘     │  │
│  │  ┌──────────────────────────────────────────────────────┐  │
│  │  │  Modals                                              │  │
│  │  │  - Profile Modal (from base)                         │  │
│  │  │  - Custom Modals (from page-specific JS)             │  │
│  │  └──────────────────────────────────────────────────────┘  │
│  │  ┌──────────────────────────────────────────────────────┐  │
│  │  │  Scripts                                             │  │
│  │  │  - base.js (common functions)                        │  │
│  │  │  - {% block extra_js %} (page-specific JS)           │  │
│  │  └──────────────────────────────────────────────────────┘  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────────┘

                    ↓ Extended by ↓

┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐
│  dashboard.html  │  │   items.html     │  │   stock.html     │
│                  │  │                  │  │                  │
│ {% extends ...%} │  │ {% extends ...%} │  │ {% extends ...%} │
│ Dashboard-      │  │ Items-specific   │  │ Stock-specific   │
│ specific content │  │ content          │  │ content          │
│ & CSS & JS       │  │ & CSS & JS       │  │ & CSS & JS       │
└──────────────────┘  └──────────────────┘  └──────────────────┘
```

## File Relationships

```
Input: User visits /items/
   ↓
Django loads: items.html
   ↓
items.html: {% extends "base.html" %}
   ↓
Renders:
  ┌─ base.html
  │  ├─ <head>
  │  │  ├─ base.css
  │  │  └─ {% block extra_css %} → items.css
  │  ├─ <body>
  │  │  ├─ Sidebar (from base.html)
  │  │  ├─ Header (from base.html)
  │  │  ├─ {% block content %} → ITEMS CONTENT
  │  │  └─ Scripts
  │  │     ├─ base.js
  │  │     └─ {% block extra_js %} → items.js
```

## Code Inheritance Flow

```
Global (Available to ALL pages):
├─ API_URL = '/api'
├─ token (from localStorage)
├─ userRole (admin|staff|viewer)
├─ csrftoken (CSRF protection)
├─ Functions:
│  ├─ formatDate(dateStr)
│  ├─ showError(msg) / clearError()
│  ├─ logout()
│  ├─ loadUserProfile()
│  ├─ setActiveNav(pageId)
│  ├─ updateDate()
│  ├─ openProfileModal() / closeProfileModal()
│  └─ setupSidebarToggle()
├─ CSS Classes:
│  ├─ Layout: .container, .main, .sidebar
│  ├─ Nav: .nav, .nav-item, .nav-item.active
│  ├─ Controls: .controls, .search-box, .add-btn
│  ├─ Tables: .table-container, .status-badge
│  ├─ Forms: .form-group, .form-row, .btn-submit
│  ├─ Modals: .modal, .modal-content
│  └─ Utils: .error, .loading, .spinner, .hidden
└─ HTML Structure:
   ├─ Sidebar with navigation
   ├─ Header with date/user
   ├─ Error message container
   ├─ Profile modal
   └─ Content area placeholder

Page-Specific (Each page adds):
├─ Page-specific content in {% block content %}
├─ Page-specific CSS in {% block extra_css %}
├─ Page-specific JS in {% block extra_js %}
├─ Page-specific modals
└─ Page-specific functions
```

## Request/Response Cycle

```
1. User navigates to /items/
   ↓
2. Django view renders items.html
   ↓
3. items.html extends base.html
   ↓
4. Browser receives merged HTML:
   - base.html structure + items.html content
   ↓
5. Browser loads CSS:
   - base.css (common styles)
   - items.css (page-specific styles)
   ↓
6. Browser loads JavaScript:
   - base.js (common functions)
   - items.js (page-specific functions)
   ↓
7. DOMContentLoaded event triggers:
   - setupSidebarToggle() from base.js
   - updateDate() from base.js
   - loadUserProfile() from base.js
   - Custom code in items.js
   ↓
8. Page is interactive and ready
```

## Common Use Case: Add New Page

```
Decision: Need to create "Reports" page
   ↓
1. Create /backend/templates/reports.html
   ├─ {% extends "base.html" %}
   ├─ {% block title %} → Page title
   ├─ {% block page_title %} → Page heading
   ├─ {% block content %} → Reports content
   ├─ {% block extra_css %} → reports.css link
   └─ {% block extra_js %} → reports.js link
   ↓
2. Create /backend/static/css/reports.css
   ├─ .report-table { }
   ├─ .report-chart { }
   └─ Custom styles
   ↓
3. Create /backend/static/js/reports.js
   ├─ Use: API_URL, token, userRole from base.js
   ├─ Use: showError(), formatDate() etc.
   ├─ Call: setActiveNav('reportsNav')
   ├─ Fetch: GET /api/reports/
   └─ Render: Report data
   ↓
4. Add to base.html navigation:
   <a href="/reports/" class="nav-item" id="reportsNav">
     📈 Reports
   </a>
   ↓
5. Test page
   ✓ Navigation highlights correctly
   ✓ User info displays
   ✓ Date shows in header
   ✓ Sidebar toggles work
   ✓ Custom content shows
```

## CSS Cascade & Specificity

```
Loaded Order (First to Last):
1. base.css (common styles)
   └─ .container, .sidebar, .nav-item, etc.
   
2. page-specific.css (from {% block extra_css %})
   └─ Can override base.css if needed
   └─ .custom-report-style { }
   
Result: Page-specific styles override base styles
Example:
  base.css:    .report-title { color: blue; }
  reports.css: .report-title { color: red; } ← This wins!
```

## JavaScript Execution Order

```
1. base.js loads
   ├─ Declares: API_URL, token, userRole, csrftoken
   ├─ Defines: All common functions
   └─ Waits for: DOMContentLoaded
   
2. reports.js loads
   ├─ Declares: Page-specific variables
   ├─ Defines: Page-specific functions
   ├─ Can access: API_URL, token, formatDate, etc.
   └─ Waits for: DOMContentLoaded
   
3. DOMContentLoaded event fires
   ├─ base.js runs:
   │  ├─ setupSidebarToggle()
   │  ├─ updateDate()
   │  └─ loadUserProfile()
   │
   └─ reports.js runs:
      ├─ setActiveNav('reportsNav')
      └─ loadReports()
   
Result: Both common and page-specific code runs
```

## CSS Class Inheritance

```
HTML Structure (from base.html):
<div class="container">
  <div class="sidebar">
    <div class="logo">...</div>
    <div class="nav">
      <a class="nav-item">Dashboard</a>
      <a class="nav-item active">Items</a> ← Current page
    </div>
  </div>
  <div class="main">
    <div class="header">...</div>
    <div id="error" class="error hidden">...</div>
    <!-- Page content goes here -->
  </div>
</div>

CSS Applied (from base.css + page CSS):
.container       ← All pages get this layout
.sidebar         ← All pages get this sidebar
.nav-item        ← All pages get nav styling
.nav-item.active ← Current page nav highlighted
.error           ← All pages can show errors
.hidden          ← Utility class to hide elements
.custom-xyz      ← Page-specific from reports.css
```

## Page-Specific Overrides

```
Example: Items page adds custom table styling

base.html provides:
  <table>
    <thead><tr><th>...</th></tr></thead>
    <tbody>...</tbody>
  </table>

base.css provides:
  table { width: 100%; border-collapse: collapse; }
  th { background: #f9f9f9; padding: 12px; }

items.css can override:
  table { border: 2px solid blue; }  ← Override
  th { background: #e6f0ff; }        ← Override
  th.highlighted { font-weight: bold; }  ← New

Result: Items page tables have custom styling
while other pages keep default styling
```

## Responsive Design Flow

```
Browser Width: 1200px (Desktop)
├─ .container = 250px sidebar + 1fr main
├─ Sidebar visible
├─ Full navigation
└─ Wide tables and forms

Browser Width: 768px (Tablet/Mobile)
├─ Media query activates (from base.css)
├─ .container = 1fr (sidebar hidden)
├─ .sidebar.collapsed = translateX(-250px)
├─ Hamburger menu for sidebar
├─ Stacked layouts
└─ Mobile-optimized forms

User Clicks Hamburger
├─ setupSidebarToggle() runs
├─ .container toggles .sidebar-collapsed
├─ Sidebar slides in/out
└─ State saved to localStorage
```

## Security Flow

```
User Login (Not in base, but base handles):
GET http://localhost:8000/
  ↓
Django login view
  ↓
User authenticated
  ↓
Token stored: localStorage.setItem('access', token)
  ↓
Redirect to /dashboard/
  ↓
User navigates and base.js:
├─ const token = localStorage.getItem('access')
├─ In fetch: headers: { Authorization: `Bearer ${token}` }
├─ API authenticates user
├─ If 401: logout() clears token & redirects

User Logout:
Click "Logout" button
  ↓
logout() function runs (from base.js)
  ↓
localStorage.removeItem('access')
localStorage.removeItem('refresh')
sessionStorage.removeItem('access')
sessionStorage.removeItem('refresh')
  ↓
window.location.href = '/'
  ↓
Redirects to login page
```

## Summary

- **base.html** = Master structure (all pages inherit)
- **base.css** = Common styles (all pages use)
- **base.js** = Common functions (all pages access)
- **Page-specific files** = Only unique content, CSS, JS
- **Result** = DRY principle, consistent UI, faster development
