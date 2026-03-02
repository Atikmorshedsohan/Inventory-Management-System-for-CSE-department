# Item Approval Workflow

## Overview
Staff members can add items, but they require admin/manager approval before being added to the inventory.

## Workflow Steps

### 1. Staff Creates Item
When a **staff** user creates an item via the API:

```http
POST /api/items/
Authorization: Bearer <staff_token>
Content-Type: application/json

{
  "item_name": "New Laptop",
  "category": 1,
  "room": 2,
  "unit": "pcs",
  "quantity": 5,
  "min_quantity": 2,
  "description": "Dell Latitude 5420"
}
```

**Response:**
```json
{
  "message": "Item submitted for admin approval",
  "pending_item": {
    "pending_item_id": 1,
    "item_name": "New Laptop",
    "status": "pending",
    "requested_by_name": "John Staff",
    "requested_at": "2026-01-03T20:00:00Z"
  },
  "status": "pending_approval"
}
```

The item is **NOT** created directly. Instead, a `PendingItem` record is created.

### 2. Admin Views Pending Items
Admins and managers can view pending items:

```http
GET /api/pending-items/?status=pending
Authorization: Bearer <admin_token>
```

**Or** view on the dashboard at: `http://localhost:8000/dashboard/`

### 3. Admin Approves Item
Admin approves the pending item:

```http
POST /api/pending-items/<id>/approve/
Authorization: Bearer <admin_token>
```

This will:
- Create the actual `Item` in the inventory
- Mark the `PendingItem` as `approved`
- Link the created item to the pending record
- Create an audit log entry

### 4. Admin Rejects Item (Optional)
If the admin rejects the item:

```http
POST /api/pending-items/<id>/reject/
Authorization: Bearer <admin_token>
Content-Type: application/json

{
  "rejection_reason": "Duplicate item already exists"
}
```

## User Roles

### Staff
- Can create items (creates pending items)
- Can view their own pending items
- **Cannot** approve or reject items
- **Cannot** create items directly

### Admin/Manager
- Can create items directly (no approval needed)
- Can view all pending items
- Can approve pending items
- Can reject pending items
- Can view all items

## API Endpoints

| Endpoint | Method | Role | Description |
|----------|--------|------|-------------|
| `/api/items/` | POST | Staff | Creates pending item |
| `/api/items/` | POST | Admin/Manager | Creates item directly |
| `/api/pending-items/` | GET | All | View pending items (filtered by role) |
| `/api/pending-items/?status=pending` | GET | Admin/Manager | View all pending |
| `/api/pending-items/<id>/approve/` | POST | Admin/Manager | Approve item |
| `/api/pending-items/<id>/reject/` | POST | Admin/Manager | Reject item |
| `/api/pending-items/pending_approvals/` | GET | Admin/Manager | Alias for pending list |

## Testing

Run the test script:
```bash
cd backend
python test_pending_workflow.py
```

## Dashboard View

Admins and managers will see a "Pending Item Approvals" section on the dashboard with:
- List of all pending items
- Item details (name, category, quantity, requester, date)
- Approve and Reject buttons for each item

## Implementation Details

### Modified Files
1. `backend/inventory/views.py` - Updated `ItemViewSet.create()` to intercept staff creation
2. `backend/templates/dashboard.html` - Added pending items section
3. `backend/static/js/dashboard.js` - Added pending items loading and approval functions

### Database Models
- `PendingItem` - Stores pending item creation requests
- `Item` - Actual inventory items (created after approval)
