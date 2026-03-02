# Base Template System - Quick Start Guide

## Overview

A complete base template system has been created to eliminate code duplication across all pages. All pages now inherit from `base.html` which includes common navigation, styling, and functionality.

## Files Created

| File | Location | Purpose |
|------|----------|---------|
| `base.html` | `/backend/templates/` | Master template - all pages extend this |
| `base.css` | `/backend/static/css/` | All common styles |
| `base.js` | `/backend/static/js/` | All common JavaScript functions |
| Documentation | Root directory | Complete guides and examples |

## 5-Minute Setup

### For Existing Pages

Update any template that currently has the old structure:

**Before:**
```html
<!doctype html>
<html>
  <head>
    <title>Page</title>
    <link rel="stylesheet" href="{% static 'css/items.css' %}">
  </head>
  <body>
    <div class="container">
      <div class="sidebar"><!-- old code --></div>
      <div class="main"><!-- content here --></div>
    </div>
    <script src="{% static 'js/items.js' %}"></script>
  </body>
</html>
```

**After:**
```django
{% extends "base.html" %}
{% block title %}CSE Inventory — Items{% endblock %}
{% block page_title %}Item Management{% endblock %}
{% block extra_css %}
  <link rel="stylesheet" href="{% static 'css/items.css' %}">
{% endblock %}
{% block content %}
  <!-- Your page content only -->
{% endblock %}
{% block extra_js %}
  <script src="{% static 'js/items.js' %}"></script>
{% endblock %}
```

## What You Get

### Automatic Features (from base.html)
✅ Sidebar navigation  
✅ Header with date  
✅ User info display  
✅ Error message container  
✅ Profile modal  
✅ Responsive design  

### Automatic Functions (from base.js)
```javascript
// Authentication
logout()                        // Logout user
loadUserProfile()              // Load user from API

// Utilities
formatDate(dateStr)            // Format date nicely
showError(msg)                 // Show error message
clearError()                   // Hide error message
getCookie(name)                // Get CSRF token

// Navigation
setActiveNav(pageId)           // Mark nav item active
setupSidebarToggle()           // Sidebar collapse/expand
updateDate()                   // Update date in header

// Modals
openProfileModal()             // Show profile modal
closeProfileModal()            // Hide profile modal
```

### Automatic Variables (from base.js)
```javascript
API_URL        = '/api'                // Base API URL
token          = '...'                 // Auth token
userRole       = 'admin|staff|viewer'  // User's role
csrftoken      = '...'                 // CSRF token
```

### Automatic CSS Classes
```css
.container, .main, .sidebar, .header
.nav, .nav-item, .nav-item.active
.controls, .search-box, .add-btn
.table-container, .status-badge
.modal, .form-group, .button
.error, .loading, .spinner
/* ...and many more */
```

## Creating a New Page

### Step 1: Create Template File
Create `/backend/templates/mypage.html`:

```django
{% extends "base.html" %}
{% block title %}CSE Inventory — My Page{% endblock %}
{% block page_title %}My Page{% endblock %}
{% block content %}
  <div class="controls">
    <input type="text" class="search-box" placeholder="Search...">
    <button class="add-btn" onclick="openModal()">+ Add</button>
  </div>
  
  <div class="table-container">
    <table>
      <thead>
        <tr><th>Column 1</th><th>Column 2</th></tr>
      </thead>
      <tbody id="dataTable"></tbody>
    </table>
  </div>
{% endblock %}
{% block extra_css %}
  <link rel="stylesheet" href="{% static 'css/mypage.css' %}">
{% endblock %}
{% block extra_js %}
  <script src="{% static 'js/mypage.js' %}"></script>
{% endblock %}
```

### Step 2: Create Page JavaScript
Create `/backend/static/js/mypage.js`:

```javascript
// All variables from base.js available:
// API_URL, token, userRole, csrftoken

document.addEventListener('DOMContentLoaded', () => {
  setActiveNav('mypageNav');
  loadData();
});

async function loadData() {
  try {
    const res = await fetch(`${API_URL}/mydata/`, {
      headers: { 'Authorization': 'Bearer ' + token }
    });
    if (res.status === 401) logout();
    const data = await res.json();
    renderTable(data);
  } catch (e) {
    showError(e.message);
  }
}

function renderTable(data) {
  const table = document.getElementById('dataTable');
  table.innerHTML = data.map(item => `
    <tr>
      <td>${item.name}</td>
      <td>${formatDate(item.date)}</td>
    </tr>
  `).join('');
}
```

### Step 3: Add Navigation Item
In `base.html` sidebar, add:

```html
<a href="/mypage/" class="nav-item" id="mypageNav">📄 My Page</a>
```

## Common Patterns

### Check if User is Admin
```javascript
if (userRole !== 'viewer') {
  // Show edit/delete buttons
}
```

### API Request with Auth
```javascript
const res = await fetch(`${API_URL}/items/`, {
  headers: { 'Authorization': 'Bearer ' + token }
});
if (res.status === 401) logout();
const data = await res.json();
```

### Show/Hide Error
```javascript
showError('Item name is required');
// ... do something ...
clearError();
```

### Format Date
```javascript
const pretty = formatDate('2025-12-29T10:30:00');
// Result: "Dec 29, 2025, 10:30 AM"
```

### Mark Navigation Active
```javascript
setActiveNav('itemsNav');  // Must match nav item ID
```

## File Structure

```
inventory_management/
├── backend/
│   ├── templates/
│   │   ├── base.html           ← Master template
│   │   ├── dashboard.html
│   │   ├── items.html
│   │   ├── stock.html
│   │   └── ...
│   ├── static/
│   │   ├── css/
│   │   │   ├── base.css        ← Common styles
│   │   │   ├── items.css
│   │   │   └── ...
│   │   └── js/
│   │       ├── base.js         ← Common functions
│   │       ├── items.js
│   │       └── ...
│   ├── manage.py
│   └── ...
├── BASE_TEMPLATE_DOCUMENTATION.md      ← Full guide
├── BASE_TEMPLATE_SETUP_SUMMARY.md      ← Summary
└── ...
```

## Documentation

Three files explain everything:

1. **BASE_TEMPLATE_DOCUMENTATION.md** - Complete reference with all functions and patterns
2. **BASE_TEMPLATE_SETUP_SUMMARY.md** - Overview of what was created
3. **This file** - Quick start guide

Read the full documentation for advanced topics!

## Typical Page Structure

```
┌─ base.html (Master template)
│  ├─ <head>
│  │  └─ base.css + page CSS
│  └─ <body>
│     ├─ sidebar (from base)
│     ├─ header (from base)
│     ├─ content block
│     │  └─ {% block content %} YOUR HTML HERE
│     └─ scripts
│        ├─ base.js + page JS
│        └─ {% block extra_js %}
```

## Example: Items Page

```django
{% extends "base.html" %}

{% block title %}CSE Inventory — Items{% endblock %}
{% block page_title %}Item Management{% endblock %}

{% block content %}
  <!-- Controls -->
  <div class="controls">
    <input type="text" id="searchBox" class="search-box" placeholder="Search items...">
    <select id="categoryFilter" class="filter-select"></select>
    <button class="add-btn" onclick="openAddModal()">+ Add Item</button>
  </div>

  <!-- Table -->
  <div class="table-container">
    <table>
      <thead>
        <tr>
          <th>Item Name</th>
          <th>Category</th>
          <th>Quantity</th>
          <th>Status</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody id="itemsTable"></tbody>
    </table>
  </div>
{% endblock %}

{% block extra_css %}
  <link rel="stylesheet" href="{% static 'css/items.css' %}">
{% endblock %}

{% block extra_js %}
  <script src="{% static 'js/items.js' %}"></script>
{% endblock %}
```

## Summary

- ✅ Base template system ready
- ✅ Common code centralized
- ✅ Consistent UI guaranteed
- ✅ Reusable functions available
- ✅ Faster development
- ✅ Easy maintenance

Start using `{% extends "base.html" %}` in all new/updated templates!

## Need Help?

1. **Creating a new page?** → Follow the "Creating a New Page" section above
2. **Migrating existing page?** → Follow the "For Existing Pages" section
3. **Using a function?** → Check the "Common Patterns" section
4. **Full documentation?** → Read `BASE_TEMPLATE_DOCUMENTATION.md`

Happy coding! 🚀
