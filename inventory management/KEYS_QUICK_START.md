# Key Borrowing System - Quick Start Guide

## What Was Built

A complete **Room Key Borrowing System** has been implemented in your Inventory Management application. This system allows viewers (regular users) to request and borrow room keys from staff members with proper approval workflow.

## Key Components

### 1. **Backend Models** (Django)
- **RoomKey**: Stores information about physical room keys
- **KeyBorrow**: Tracks borrowing requests with approval workflow
- **KeyAuditLog**: Maintains complete audit trail of all key activities

### 2. **REST API Endpoints**
All endpoints are prefixed with `/api/`:

**For Everyone:**
- `GET /room-keys/available_keys/` - Browse available keys to borrow

**For Viewers (Request Creators):**
- `POST /key-borrows/` - Request to borrow a key
- `GET /key-borrows/my_requests/` - View their own requests
- `POST /key-borrows/{id}/pickup/` - Pick up approved key
- `POST /key-borrows/{id}/return_key/` - Return borrowed key

**For Staff/Admin:**
- `GET /key-borrows/pending_approvals/` - View requests awaiting approval
- `POST /key-borrows/{id}/approve/` - Approve a request
- `POST /key-borrows/{id}/reject/` - Reject a request
- `GET /key-borrows/overdue_keys/` - View overdue borrowed keys
- `GET /room-keys/` - Manage all keys

### 3. **Frontend Interface**
New file: `key-borrowing.html` with:
- **Available Keys Tab**: Browse keys ready to borrow
- **My Requests Tab**: View personal borrow requests (viewer only)
- **Pending Approvals Tab**: Staff approval interface
- **Borrowed Keys Tab**: Currently borrowed keys (staff only)
- **Overdue Keys Tab**: Manage overdue borrowed keys (staff only)

Interactive modals for:
- Requesting to borrow a key
- Returning a borrowed key
- Rejecting a borrow request

### 4. **Styling**
- New CSS file: `key-borrowing.css` with responsive design
- Status badges for different key states
- Overdue indicators and warnings
- Mobile-friendly layout

### 5. **JavaScript**
- New file: `key-borrowing.js` with complete functionality
- User authentication and role-based UI
- Real-time data loading from API
- Modal handling for actions

## How to Use

### For Viewers (Regular Users)

1. **Access the System**
   ```
   Navigate to: /key-borrowing
   ```

2. **Find and Request a Key**
   - View "Available Keys" tab
   - Click "Request to Borrow" on desired key
   - Fill in:
     - Purpose (why you need the key)
     - Duration (how many days: 1-30)
   - Submit request

3. **Track Your Request**
   - Check "My Requests" tab
   - See status: Pending → Approved → Borrowed → Returned

4. **Pick Up Approved Key**
   - When approved, click "Pick Up"
   - Key becomes "borrowed" and unavailable to others

5. **Return the Key**
   - Click "Return" in my requests
   - Optionally note location/condition
   - Key becomes available again

### For Staff/Admin

1. **Access the System**
   ```
   Navigate to: /key-borrowing
   ```

2. **Review Pending Requests**
   - Check "Pending Approvals" tab
   - See viewer name, key, purpose, expected return date

3. **Approve or Reject**
   - Click "Approve" to allow borrowing
   - Click "Reject" and provide reason if needed
   - Viewer is notified of decision

4. **Monitor Borrowed Keys**
   - Check "Borrowed Keys" tab
   - See who has what key and when it's due

5. **Manage Overdue Keys**
   - Check "Overdue Keys" tab (red warning indicators)
   - Click "Contact" to note who to follow up with
   - Retrieve key from borrower

6. **View Complete History**
   - All actions are logged in audit trail
   - Access via `/api/key-audit-logs/`

## Database Structure

### RoomKey Table
```
key_id       | room_name      | key_number | status    | assigned_to | assigned_date | last_location
1            | Lab A          | K001       | in_use    | user_123    | 2025-01-02    | Lab A Desk
2            | Conference 1   | K002       | available | NULL        | NULL          | Main Office
```

### KeyBorrow Table
```
borrow_id | key_id | borrower_id | status   | requested_at | approved_at | borrowed_at | expected_return_at | returned_at
1         | 1      | user_123    | borrowed | 2025-01-02   | 2025-01-02  | 2025-01-02  | 2025-01-03        | NULL
2         | 2      | user_456    | pending  | 2025-01-03   | NULL        | NULL        | 2025-01-05        | NULL
```

### KeyAuditLog Table
```
log_id | key_id | action    | performed_by | notes                                  | timestamp
1      | 1      | borrowed  | user_123     | Requested to borrow key #1            | 2025-01-02 10:30
2      | 1      | borrowed  | staff_001    | Approved borrow request #1            | 2025-01-02 11:00
3      | 1      | borrowed  | user_123     | Key picked up by John Smith           | 2025-01-02 14:00
```

## API Examples

### Request a Key (Viewer)
```bash
curl -X POST http://localhost:8000/api/key-borrows/ \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "key": 1,
    "purpose": "Need to access Lab A for experiments",
    "expected_return_at": "2025-01-05T17:00:00Z"
  }'
```

### Approve a Request (Staff)
```bash
curl -X POST http://localhost:8000/api/key-borrows/1/approve/ \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{}'
```

### Return a Key
```bash
curl -X POST http://localhost:8000/api/key-borrows/1/return_key/ \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "location": "Lab A, left on main desk"
  }'
```

## Key Features

✅ **Role-Based Access Control**
- Viewers can only see/manage their own requests
- Staff can approve, reject, and monitor all requests
- Admin has full system control

✅ **Request Workflow**
- Pending → Approved → Borrowed → Returned
- Or Pending → Rejected (with reason)

✅ **Overdue Tracking**
- Automatic detection of borrowed keys past return date
- Visual indicators and warnings
- Staff can easily track overdue keys

✅ **Complete Audit Trail**
- Every action logged with user and timestamp
- Track key history and usage patterns

✅ **Responsive Design**
- Works on desktop, tablet, and mobile
- Touch-friendly buttons and modals
- Clear status indicators

✅ **Real-time Updates**
- Dynamic data loading from API
- No page refresh needed
- Instant status changes

## Security Features

🔒 **Authentication**
- JWT token-based authentication required
- Secure API endpoints
- Session management

🔒 **Authorization**
- Role-based permission checks
- Viewers can only access their own data
- Staff must approve requests

🔒 **Data Validation**
- All inputs validated on frontend and backend
- Proper HTTP status codes and error messages
- State validation (can't pick up unapproved keys, etc.)

🔒 **Audit Trail**
- Complete history of all actions
- Who did what and when
- Immutable logs for accountability

## Integration Points

The key borrowing system integrates with:

1. **User System** - Uses existing User model for roles and permissions
2. **Audit System** - Records actions in AuditLog
3. **API Framework** - Uses REST framework with JWT authentication
4. **Frontend Framework** - Uses Bootstrap, jQuery for UI

## Files Created/Modified

### New Files
- `backend/inventory/key_views.py` - API viewsets for key management
- `backend/inventory/key_serializers.py` - Data serializers
- `backend/templates/key-borrowing.html` - Frontend interface
- `backend/static/css/key-borrowing.css` - Styling
- `backend/static/js/key-borrowing.js` - Frontend logic
- `backend/inventory/migrations/0010_roomkey_keyborrow_keyauditlog.py` - Database migration
- `KEYS_SYSTEM_DOCUMENTATION.md` - Full documentation

### Modified Files
- `backend/inventory/models.py` - Added RoomKey, KeyBorrow, KeyAuditLog models
- `backend/inventory/serializers.py` - Added key serializers (appended)
- `backend/inventory/views.py` - Imported key viewsets
- `backend/inventory/urls.py` - Registered key endpoints

## Next Steps

1. **Add Test Keys**
   ```bash
   python manage.py shell
   >>> from inventory.models import RoomKey
   >>> RoomKey.objects.create(
   ...     room_name='Lab A',
   ...     key_number='K001',
   ...     description='Access to Lab A'
   ... )
   ```

2. **Test the System**
   - Create viewer and staff accounts
   - Test the complete workflow
   - Verify notifications work

3. **Customize**
   - Adjust borrow duration limits (currently 1-30 days)
   - Add custom email notifications
   - Integrate with access control systems

4. **Monitor & Maintain**
   - Review audit logs regularly
   - Archive old borrow records
   - Monitor overdue keys

## Troubleshooting

**Q: Keys not showing as available?**
A: Ensure RoomKey records are created in the database and their status is set to 'available'

**Q: Can't approve requests?**
A: Verify you're logged in as staff/admin user, not a viewer

**Q: Requests stuck in pending?**
A: Check browser console for JavaScript errors or API connection issues

**Q: Overdue keys not showing?**
A: Verify the expected_return_at date is in the past and status is 'borrowed'

## Support

For issues or questions, check:
1. Full documentation: `KEYS_SYSTEM_DOCUMENTATION.md`
2. API errors in browser console
3. Django logs for backend errors
4. Database records via admin panel

---

**System Ready!** 🎉 Your viewers can now request and borrow room keys with full staff oversight and audit trail.
