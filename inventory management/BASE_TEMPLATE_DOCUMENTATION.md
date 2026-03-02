# Base Template System Documentation

## Overview

A base template system has been implemented to reduce code duplication across all pages. This includes:

- **base.html** - Main template with common HTML structure
- **base.css** - Common CSS styles for all pages
- **base.js** - Common JavaScript functions and utilities

## Files

### 1. base.html (`/templates/base.html`)
The master template containing:
- HTML document structure
- Sidebar navigation
- Header with date and user info
- Error message container
- Profile modal
- Script and style includes

**Template Blocks:**
- `{% block title %}` - Page title
- `{% block page_title %}` - Page heading in header
- `{% block content %}` - Main page content
- `{% block extra_css %}` - Additional page-specific CSS
- `{% block extra_js %}` - Additional page-specific JavaScript

### 2. base.css (`/static/css/base.css`)
Contains all common styles:
- Global styles and reset
- Layout and grid system
- Sidebar navigation styling
- Header and controls
- Buttons and modals
- Forms
- Tables
- Status messages
- Responsive design

### 3. base.js (`/static/js/base.js`)
Contains common functions:
- **Constants:** `API_URL`, `token`, `userRole`, `csrftoken`
- **Utility Functions:**
  - `getCookie(name)` - Get CSRF token
  - `formatDate(dateStr)` - Format dates
  - `showError(msg)` - Display error message
  - `clearError()` - Clear error message
  - `logout()` - Logout user
  
- **User Profile Functions:**
  - `applyUserUI(user)` - Apply user info to UI
  - `loadUserProfile()` - Load and display user profile
  
- **Navigation Functions:**
  - `setActiveNav(pageId)` - Set active navigation item
  - `setupSidebarToggle()` - Setup sidebar toggle
  - `updateDate()` - Update date in header
  
- **Modal Functions:**
  - `openProfileModal()` - Open profile modal
  - `closeProfileModal()` - Close profile modal

## How to Create a New Page

### Step 1: Create Template File
Create a new template file (e.g., `mypage.html`) and extend base.html:

```html
{% extends "base.html" %}

{% block title %}CSE Inventory — My Page{% endblock %}

{% block extra_css %}
  <link rel="stylesheet" href="{% static 'css/mypage.css' %}">
{% endblock %}

{% block page_title %}My Page{% endblock %}

{% block content %}
  <!-- Your page-specific content here -->
  <div class="controls">
    <input type="text" id="searchBox" class="search-box" placeholder="Search...">
    <button class="add-btn" onclick="openModal()">+ Add Item</button>
  </div>
  
  <div class="table-container">
    <table>
      <thead>
        <tr>
          <th>Column 1</th>
          <th>Column 2</th>
        </tr>
      </thead>
      <tbody id="dataTable">
        <!-- Data goes here -->
      </tbody>
    </table>
  </div>
{% endblock %}

{% block extra_js %}
  <script src="{% static 'js/mypage.js' %}" defer></script>
{% endblock %}
```

### Step 2: Create Page-Specific CSS (Optional)
Create `mypage.css` in `/static/css/` with page-specific styles:

```css
.my-custom-style {
  color: #0066cc;
  font-size: 16px;
}
```

### Step 3: Create Page-Specific JavaScript
Create `mypage.js` in `/static/js/` with page logic:

```javascript
// Common variables from base.js are already available:
// - API_URL, token, userRole, csrftoken
// - Functions: getCookie, formatDate, showError, logout, etc.

document.addEventListener('DOMContentLoaded', () => {
  setActiveNav('pageNavId');  // Mark nav item as active
  loadData();
});

async function loadData() {
  try {
    const res = await fetch(`${API_URL}/myendpoint/`, {
      headers: { 'Authorization': 'Bearer ' + token }
    });
    const data = await res.json();
    renderData(data);
  } catch (e) {
    showError(e.message);
  }
}
```

### Step 4: Update Navigation
Add a nav link in `base.html`:

```html
<a href="/mypage/" class="nav-item" id="mypageNav">📄 My Page</a>
```

Update the nav ID to match in your JavaScript:
```javascript
setActiveNav('mypageNav');
```

## Common Pattern Examples

### Display User Error
```javascript
showError('Something went wrong!');
// Clears after 3 seconds or when action succeeds
clearError();
```

### Logout User
```javascript
logout();  // Clears storage and redirects to login
```

### Format Date
```javascript
const formatted = formatDate('2025-12-29T10:30:00');
// Result: "Dec 29, 2025, 10:30 AM"
```

### API Request with Authentication
```javascript
const res = await fetch(`${API_URL}/items/`, {
  headers: { 'Authorization': 'Bearer ' + token }
});
if (res.status === 401) logout();  // Auto-logout if unauthorized
const data = await res.json();
```

### Check User Permissions
```javascript
if (userRole === 'viewer') {
  // Hide edit/delete buttons
  document.getElementById('addBtn').style.display = 'none';
}
```

## Migrating Existing Pages

For pages already using the old structure:

1. **Remove duplicate HTML** from the page template
2. **Keep only the content block** inside `{% block content %}`
3. **Move page-specific CSS** to `{% block extra_css %}`
4. **Move page-specific JS** to `{% block extra_js %}`
5. **Update navigation IDs** in your JavaScript

### Before (Old Structure):
```html
<!doctype html>
<html>
  <head>
    <title>Page</title>
    <link rel="stylesheet" href="items.css">
  </head>
  <body>
    <div class="container">
      <div class="sidebar">...</div>
      <div class="main">
        <div class="header">...</div>
        <div class="content">YOUR CONTENT</div>
      </div>
    </div>
    <script src="items.js"></script>
  </body>
</html>
```

### After (With Base Template):
```html
{% extends "base.html" %}
{% block extra_css %}
  <link rel="stylesheet" href="{% static 'css/items.css' %}">
{% endblock %}
{% block page_title %}Items{% endblock %}
{% block content %}
  YOUR CONTENT
{% endblock %}
{% block extra_js %}
  <script src="{% static 'js/items.js' %}"></script>
{% endblock %}
```

## CSS Classes Available

All CSS classes from `base.css` are available globally:

### Layout
- `.container`, `.main`, `.header`, `.sidebar`

### Navigation
- `.nav`, `.nav-item`, `.nav-item.active`

### Controls
- `.controls`, `.search-box`, `.filter-select`
- `.add-btn`, `.toggle-btn`, `.toggle-btn.active`

### Tables
- `.table-container`, `table`, `th`, `td`
- `.status-badge`, `.status-available`, `.status-low`

### Modals
- `.modal`, `.modal.show`, `.modal-content`
- `.modal-header`, `.modal-close`, `.modal-body`

### Forms
- `.form-group`, `.form-row`
- `.form-actions`, `.btn-submit`, `.btn-cancel`

### Messages
- `.error`, `.loading`, `.empty`, `.hidden`

### Utilities
- `.spinner`, `.profile-item`

## Best Practices

1. **Don't duplicate code** - Use base.html and base.css/js
2. **Minimal page-specific CSS** - Only add styles unique to your page
3. **Reuse utility functions** - Use `showError()`, `formatDate()`, etc.
4. **Update active nav** - Call `setActiveNav()` in your page JS
5. **Handle auth errors** - Check for 401 and call `logout()`
6. **Use semantic HTML** - Follow the pattern of existing pages
7. **Responsive first** - Base CSS is already responsive
8. **Consistent styling** - Use utility classes from base.css

## File Structure

```
backend/
├── templates/
│   ├── base.html           (Master template)
│   ├── dashboard.html      (Extends base.html)
│   ├── items.html          (Extends base.html)
│   ├── stock.html          (Extends base.html)
│   └── ...
├── static/
│   ├── css/
│   │   ├── base.css        (Common styles)
│   │   ├── dashboard.css   (Dashboard-specific styles)
│   │   ├── items.css       (Items-specific styles)
│   │   └── ...
│   └── js/
│       ├── base.js         (Common functions)
│       ├── dashboard.js    (Dashboard-specific JS)
│       ├── items.js        (Items-specific JS)
│       └── ...
```

## Summary

The base template system provides:
- ✅ Consistent UI across all pages
- ✅ Reduced code duplication
- ✅ Easier maintenance
- ✅ Reusable components and functions
- ✅ Common authentication and navigation
- ✅ Mobile-responsive design
- ✅ Standard error handling

All new pages should extend `base.html` and use the provided utilities from `base.js`.
