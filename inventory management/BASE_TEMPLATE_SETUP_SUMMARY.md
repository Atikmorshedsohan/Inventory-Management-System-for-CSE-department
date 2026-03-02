# Base Template System Summary

## What Was Created

### 1. **base.html** (`/backend/templates/base.html`)
The master template that all pages should inherit from. Contains:
- Complete HTML structure (DOCTYPE, head, body)
- Sidebar navigation with all menu items
- Header with date and user info
- Error message container
- Profile modal
- Template blocks for content and styles

**Key Template Blocks:**
```django
{% block title %}         - Page title (appears in browser tab)
{% block extra_css %}     - Page-specific CSS links
{% block page_title %}    - Page heading in header
{% block content %}       - Main page content
{% block extra_js %}      - Page-specific JavaScript links
```

### 2. **base.css** (`/backend/static/css/base.css`)
Complete stylesheet with all common styles:
- **Global styles**: fonts, colors, spacing
- **Layout**: grid, container, sidebar
- **Navigation**: sidebar, menu items, active states
- **Controls**: buttons, search box, filters
- **Tables**: styling for data tables
- **Forms**: form groups, inputs, buttons
- **Modals**: modal dialogs and overlays
- **Responsive**: mobile breakpoints (768px)

### 3. **base.js** (`/backend/static/js/base.js`)
Common JavaScript functions available to all pages:

**Global Variables:**
- `API_URL = '/api'`
- `token` - authentication token from localStorage
- `userRole` - user's role (admin, staff, viewer)
- `csrftoken` - CSRF protection token

**Functions:**
- `getCookie(name)` - Get cookie value
- `formatDate(dateStr)` - Format dates nicely
- `showError(msg)` - Display error message
- `clearError()` - Clear error display
- `logout()` - Logout and redirect to login
- `applyUserUI(user)` - Display user info
- `loadUserProfile()` - Fetch and display user
- `setActiveNav(pageId)` - Mark nav item active
- `setupSidebarToggle()` - Sidebar collapse/expand
- `updateDate()` - Update date in header
- `openProfileModal()` / `closeProfileModal()` - Profile dialog

### 4. **dashboard-new.html** (Example)
Sample showing how to extend base.html:
```django
{% extends "base.html" %}
{% block title %}CSE Inventory — Dashboard{% endblock %}
{% block extra_css %}<link rel="stylesheet" href="{% static 'css/dashboard.css' %}">{% endblock %}
{% block page_title %}Dashboard{% endblock %}
{% block content %}
  <!-- Dashboard content here -->
{% endblock %}
{% block extra_js %}<script src="{% static 'js/dashboard.js' %}"></script>{% endblock %}
```

### 5. **BASE_TEMPLATE_DOCUMENTATION.md**
Complete guide with:
- File descriptions
- How to create new pages
- How to migrate existing pages
- Available CSS classes
- Common patterns and examples
- Best practices
- File structure

## How to Use

### Creating a New Page

1. **Create template** (`/backend/templates/mypage.html`):
```django
{% extends "base.html" %}
{% block title %}CSE Inventory — My Page{% endblock %}
{% block page_title %}My Page{% endblock %}
{% block extra_css %}<link rel="stylesheet" href="{% static 'css/mypage.css' %}">{% endblock %}
{% block content %}
  <!-- Your page content -->
{% endblock %}
{% block extra_js %}<script src="{% static 'js/mypage.js' %}"></script>{% endblock %}
```

2. **Create page-specific CSS** (`/backend/static/css/mypage.css`):
```css
.my-custom-class {
  color: #0066cc;
  font-size: 16px;
}
```

3. **Create page-specific JS** (`/backend/static/js/mypage.js`):
```javascript
// Variables from base.js are already available:
// API_URL, token, userRole, csrftoken

document.addEventListener('DOMContentLoaded', () => {
  setActiveNav('mypageNav');  // Mark nav as active
  loadData();
});
```

4. **Add navigation** in base.html sidebar:
```html
<a href="/mypage/" class="nav-item" id="mypageNav">📄 My Page</a>
```

## Benefits

✅ **No Duplication** - Common code in one place  
✅ **Consistent UI** - All pages look the same  
✅ **Easy Updates** - Change base.css once, affects all pages  
✅ **Reusable Functions** - Use common JS utilities  
✅ **Responsive Design** - Mobile-friendly by default  
✅ **Faster Development** - Less boilerplate code  
✅ **Easier Maintenance** - Single source of truth  
✅ **Better Organization** - Clear separation of concerns  

## Migration Checklist

To migrate existing pages to use base.html:

- [ ] Update `<!doctype html>` → `{% extends "base.html" %}`
- [ ] Remove duplicate sidebar HTML
- [ ] Remove duplicate header HTML
- [ ] Move content to `{% block content %}`
- [ ] Move CSS links to `{% block extra_css %}`
- [ ] Move JS links to `{% block extra_js %}`
- [ ] Update navigation IDs to match `setActiveNav()` calls
- [ ] Remove old `<script>` for sidebar/date functions (in base.js now)
- [ ] Remove old `<style>` tags (use base.css)
- [ ] Test page to ensure it displays correctly

## Quick Reference

### Common CSS Classes
```
.container, .main, .sidebar, .header
.nav, .nav-item, .nav-item.active
.controls, .search-box, .add-btn, .toggle-btn
.table-container, .status-badge, .status-low
.modal, .modal-content, .modal-header
.form-group, .form-row, .btn-submit, .btn-cancel
.error, .loading, .spinner, .hidden
```

### Common JS Functions
```javascript
formatDate(dateStr)           // Format date to readable string
showError(msg)                // Display error message
logout()                      // Logout and redirect to login
setActiveNav(pageId)          // Mark nav item as active
loadUserProfile()             // Load user info from API
openProfileModal()            // Show profile dialog
closeProfileModal()           // Hide profile dialog
```

## Next Steps

1. ✅ Base template system is ready
2. 📝 Migrate existing pages to extend base.html
3. 🔄 Update old CSS/JS to use base utilities
4. 🧪 Test each page after migration
5. 📚 Use documentation for future pages

## Files Created

```
/backend/templates/
├── base.html                     ✅ NEW - Master template
└── dashboard-new.html            ✅ NEW - Example of extending base

/backend/static/css/
├── base.css                      ✅ NEW - Common styles

/backend/static/js/
├── base.js                       ✅ NEW - Common functions

/
└── BASE_TEMPLATE_DOCUMENTATION.md ✅ NEW - Full documentation
```

All common code is now centralized and ready to be inherited by all pages!
