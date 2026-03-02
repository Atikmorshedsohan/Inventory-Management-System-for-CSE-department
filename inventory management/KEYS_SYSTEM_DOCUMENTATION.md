# Room Key Borrowing System

## Overview
The Room Key Borrowing System allows viewers (users) to request to borrow room keys from staff members. Staff can approve, reject, and manage these requests. The system tracks all key borrowing activities with audit logs.

## Features

### For Viewers
- **Browse Available Keys**: View all room keys that are currently available for borrowing
- **Request to Borrow**: Submit a borrowing request with purpose and expected return date
- **View My Requests**: Track the status of all their borrowing requests
- **Pick Up Keys**: Accept approved keys and pick them up
- **Return Keys**: Return borrowed keys with location notes
- **View History**: See complete history of all their borrowing activities

### For Staff/Admin
- **Manage Keys**: View and manage all room keys in the system
- **Approve Requests**: Approve or reject borrowing requests from viewers
- **Track Borrowed Keys**: Monitor all currently borrowed keys
- **Identify Overdue**: Automatically identify overdue keys and notify borrowers
- **Audit Trail**: Complete audit log of all key activities

## Database Models

### RoomKey
Represents a physical room key in the system.

**Fields:**
- `key_id` (AutoField): Primary key
- `room_name` (CharField): Name of the room (e.g., "Lab A", "Conference Room 1")
- `key_number` (CharField): Unique identifier for the physical key (e.g., "K001", "K002")
- `description` (TextField): Additional notes about the key
- `status` (CharField): Current status - available, in_use, lost, or maintenance
- `assigned_to` (ForeignKey to User): Currently assigned user
- `assigned_date` (DateTime): When the key was assigned
- `last_location` (CharField): Last known location of the key
- `created_at` (DateTime): When the key record was created
- `updated_at` (DateTime): Last update timestamp

**Status Values:**
- `available`: Key is available for borrowing
- `in_use`: Key is currently borrowed
- `lost`: Key is reported as lost
- `maintenance`: Key is in maintenance

### KeyBorrow
Represents a borrowing transaction/request.

**Fields:**
- `borrow_id` (AutoField): Primary key
- `key` (ForeignKey to RoomKey): The key being borrowed
- `borrower` (ForeignKey to User): The person requesting the key (must be viewer role)
- `approver` (ForeignKey to User): The staff member who approved/rejected
- `purpose` (TextField): Reason for borrowing the key
- `status` (CharField): Current status - pending, approved, rejected, borrowed, returned, overdue
- `requested_at` (DateTime): When the request was made
- `approved_at` (DateTime): When it was approved/rejected
- `borrowed_at` (DateTime): When the key was picked up
- `expected_return_at` (DateTime): When the key should be returned
- `returned_at` (DateTime): When the key was actually returned
- `rejection_reason` (TextField): Reason if request was rejected
- `notes` (TextField): Additional notes
- `created_at` (DateTime): Record creation timestamp

**Status Values:**
- `pending`: Awaiting staff approval
- `approved`: Approved by staff, waiting for pickup
- `rejected`: Rejected by staff
- `borrowed`: Key has been picked up
- `returned`: Key has been returned
- `overdue`: Key should have been returned but hasn't

### KeyAuditLog
Tracks all actions performed on keys.

**Fields:**
- `log_id` (AutoField): Primary key
- `key` (ForeignKey to RoomKey): The key being audited
- `action` (CharField): Type of action (assigned, returned, lost, found, etc.)
- `performed_by` (ForeignKey to User): User who performed the action
- `notes` (TextField): Details about the action
- `timestamp` (DateTime): When the action occurred

**Action Types:**
- `assigned`: Key was assigned to a user
- `returned`: Key was returned
- `lost`: Key was reported lost
- `found`: Lost key was found
- `maintenance`: Key sent to maintenance
- `restored`: Key was restored after maintenance
- `created`: Key record was created
- `borrowed`: Key borrow request made/approved
- `returned_borrow`: Key was returned from borrow

## API Endpoints

### Room Keys
- `GET /api/room-keys/` - List all room keys (staff only)
- `GET /api/room-keys/{id}/` - Get specific key details
- `GET /api/room-keys/available_keys/` - List available keys for borrowing
- `GET /api/room-keys/{id}/borrow_history/` - Get borrow history for a key

### Key Borrow Requests
- `GET /api/key-borrows/` - List borrow requests (filtered by role)
- `POST /api/key-borrows/` - Create new borrow request (viewers only)
- `GET /api/key-borrows/{id}/` - Get borrow request details
- `POST /api/key-borrows/{id}/approve/` - Approve request (staff only)
- `POST /api/key-borrows/{id}/reject/` - Reject request (staff only)
- `POST /api/key-borrows/{id}/pickup/` - Mark key as picked up
- `POST /api/key-borrows/{id}/return_key/` - Return borrowed key
- `GET /api/key-borrows/my_requests/` - Get current user's requests
- `GET /api/key-borrows/pending_approvals/` - Get pending requests (staff only)
- `GET /api/key-borrows/overdue_keys/` - Get overdue keys (staff only)

### Key Audit Logs
- `GET /api/key-audit-logs/` - List all audit logs
- `GET /api/key-audit-logs/?key={key_id}` - Logs for specific key
- `GET /api/key-audit-logs/?action={action_type}` - Filter by action

## Workflow

### Viewer Borrowing Workflow
1. Viewer navigates to Key Borrowing page
2. Views available keys and selects one
3. Submits borrowing request with:
   - Purpose of borrowing
   - Expected return date (1-30 days)
4. System creates request with "pending" status
5. Audit log entry: "borrowed" action created
6. Viewer sees request in "My Requests" tab with "pending" status

### Staff Approval Workflow
1. Staff views "Pending Approvals" tab
2. Reviews borrowing requests from viewers
3. For each request, can:
   - **Approve**: Request moves to "approved" status
   - **Reject**: Provide rejection reason, request marked "rejected"
4. Viewer is notified of approval/rejection status
5. If approved, viewer can pick up the key

### Key Pickup & Return Workflow
1. Viewer sees approved request
2. Clicks "Pick Up" button
3. System marks request as "borrowed"
4. Key status changes to "in_use"
5. When done, viewer clicks "Return"
6. Provides return location information
7. Key returned, status changes back to "available"
8. Request marked as "returned"

### Overdue Management
1. Staff can view "Overdue Keys" tab
2. Shows all borrowed keys past their expected return date
3. Displays days overdue
4. Staff can contact borrower to retrieve key

## Permission Rules

### Viewer (Request Creator)
- Can request available keys
- Can view only their own requests
- Can pick up approved keys
- Can return borrowed keys

### Staff/Admin/Manager
- Can view all room keys
- Can view all borrow requests
- Can approve/reject borrow requests
- Can view borrowed keys status
- Can identify and manage overdue keys
- Can view complete audit trail

### Creation Permissions
- Only viewers can create borrow requests
- Only approved viewers can pick up keys
- Borrower and staff can return keys

## Frontend Components

### HTML Pages
- `key-borrowing.html`: Main borrowing interface with tabs for different views

### Tabs
1. **Available Keys** - Browse and request keys
2. **My Requests** - View personal borrow requests (viewer only)
3. **Pending Approvals** - Staff approval interface
4. **Borrowed Keys** - Currently borrowed keys (staff only)
5. **Overdue Keys** - Overdue borrowed keys (staff only)

### Modals
1. **Request Key Modal** - Submit borrow request
2. **Return Key Modal** - Return a borrowed key
3. **Reject Request Modal** - Provide rejection reason

## Usage Examples

### Creating a Borrow Request (JavaScript)
```javascript
fetch('/api/key-borrows/', {
    method: 'POST',
    headers: {
        'Authorization': 'Bearer TOKEN',
        'Content-Type': 'application/json'
    },
    body: JSON.stringify({
        key: 1,
        purpose: 'Need to access Lab A for experiment',
        expected_return_at: '2025-01-05T17:00:00Z'
    })
})
```

### Approving a Request (Staff)
```javascript
fetch('/api/key-borrows/1/approve/', {
    method: 'POST',
    headers: {
        'Authorization': 'Bearer TOKEN',
        'Content-Type': 'application/json'
    },
    body: JSON.stringify({})
})
```

### Returning a Key
```javascript
fetch('/api/key-borrows/1/return_key/', {
    method: 'POST',
    headers: {
        'Authorization': 'Bearer TOKEN',
        'Content-Type': 'application/json'
    },
    body: JSON.stringify({
        location: 'Lab A, left on desk'
    })
})
```

## Testing the System

### Setup
1. Ensure migrations are applied: `python manage.py migrate`
2. Create test keys in admin panel or via API
3. Create test users with viewer and staff roles

### Test Scenarios

1. **Viewer Request**
   - Login as viewer
   - Navigate to key-borrowing page
   - Request an available key
   - Verify request appears in "My Requests"

2. **Staff Approval**
   - Login as staff
   - Check "Pending Approvals" tab
   - Approve a pending request
   - Verify status changes to "approved"

3. **Key Pickup & Return**
   - Login as viewer with approved request
   - Click "Pick Up"
   - Verify key status changes to "in_use"
   - Click "Return"
   - Verify key status changes back to "available"

4. **Overdue Tracking**
   - Create request with past expected return date
   - Pick up key
   - Login as staff
   - Check "Overdue Keys" tab
   - Verify key appears with overdue indicator

## Security Considerations

1. **Role-based Access**: Endpoints enforce user role restrictions
2. **Ownership Checks**: Viewers can only see their own requests
3. **State Validation**: System validates proper state transitions
4. **Audit Trail**: All actions logged with user and timestamp
5. **Authentication**: JWT tokens required for all API calls

## Future Enhancements

1. Email notifications for approvals/rejections
2. Automatic overdue reminders
3. Key reservation system for future dates
4. Multi-key requests
5. Key damage/loss reporting
6. QR code generation for key tracking
7. SMS notifications for overdue alerts
8. Recurring borrow patterns for frequent users
9. Key usage analytics and reports
10. Integration with access control systems

## Troubleshooting

### Keys not appearing as available
- Check RoomKey status in admin panel
- Verify key status is set to 'available'
- Check if key is already borrowed

### Request stuck in pending
- Verify staff user exists in system
- Check user role is 'staff', 'admin', or 'manager'
- Check browser console for errors

### Overdue not showing
- Verify expected_return_at is in the past
- Check that borrow status is 'borrowed' (not 'returned')
- Refresh the page

### Permission denied errors
- Verify user is logged in with correct role
- Check JWT token is valid
- Verify permission classes in viewset

## Support & Maintenance

For issues or feature requests, contact the development team with:
1. Detailed description of the issue
2. User role and involved keys/requests
3. Browser console errors (if any)
4. Steps to reproduce the issue
