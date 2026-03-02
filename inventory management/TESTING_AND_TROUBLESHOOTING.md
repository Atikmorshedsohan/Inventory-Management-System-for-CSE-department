# Room Keys System - Testing & Troubleshooting Guide

## Testing the API

### 1. Test Using cURL

#### Get All Keys
```bash
curl http://127.0.0.1:8000/api/room-keys/
```

**Expected Response**: 200 OK with JSON array of keys

#### Create a Test Key
```bash
curl -X POST http://127.0.0.1:8000/api/room-keys/ \
  -H "Content-Type: application/json" \
  -d '{
    "room_name": "Test Room",
    "key_number": "TEST-001",
    "description": "Test key",
    "status": "available",
    "last_location": "Storage"
  }'
```

**Expected Response**: 201 Created with key object

#### Get Key Status Summary
```bash
curl http://127.0.0.1:8000/api/room-keys/status_summary/
```

**Expected Response**: 200 OK with status counts

#### Get Audit Logs
```bash
curl http://127.0.0.1:8000/api/key-audit-logs/
```

**Expected Response**: 200 OK with array of audit logs

### 2. Test Using Python

```python
import requests
import json

BASE_URL = 'http://127.0.0.1:8000/api'

# Get all keys
response = requests.get(f'{BASE_URL}/room-keys/')
print(f"Status: {response.status_code}")
print(f"Keys: {response.json()}")

# Create a key
key_data = {
    'room_name': 'Test Room 2',
    'key_number': 'TEST-002',
    'description': 'Another test key',
    'status': 'available'
}
response = requests.post(f'{BASE_URL}/room-keys/', json=key_data)
print(f"Created - Status: {response.status_code}")
key_id = response.json()['key_id']

# Get status summary
response = requests.get(f'{BASE_URL}/room-keys/status_summary/')
print(f"Status Summary: {response.json()}")

# Assign key (need valid user_id)
assign_data = {'user_id': 1, 'notes': 'Test assignment'}
response = requests.post(f'{BASE_URL}/room-keys/{key_id}/assign/', json=assign_data)
print(f"Assigned - Status: {response.status_code}")

# Return key
return_data = {'location': 'Test Cabinet', 'notes': 'Returned after test'}
response = requests.post(f'{BASE_URL}/room-keys/{key_id}/return_key/', json=return_data)
print(f"Returned - Status: {response.status_code}")
```

### 3. Test Using Postman

#### Setup
1. Import API endpoints as a collection
2. Set base URL: `http://127.0.0.1:8000/api`
3. Create variables for `key_id` and `user_id`

#### Test Sequence
1. **GET** `/room-keys/` - Get all keys
2. **POST** `/room-keys/` - Create new key
3. **GET** `/room-keys/{key_id}/` - Get specific key
4. **PATCH** `/room-keys/{key_id}/` - Update key
5. **POST** `/room-keys/{key_id}/assign/` - Assign key
6. **POST** `/room-keys/{key_id}/return_key/` - Return key
7. **GET** `/room-keys/status_summary/` - Get summary
8. **GET** `/key-audit-logs/` - Get audit logs

## Common Issues & Solutions

### Issue 1: 404 Not Found on API Endpoints

**Problem**: Getting 404 when accessing `/api/room-keys/`

**Solution**:
```bash
# Check URLs are properly registered
cd backend
python manage.py show_urls | grep room-keys

# Restart Django server
python manage.py runserver
```

### Issue 2: KeyError on 'assigned_to_name' in Dashboard

**Problem**: Dashboard shows error loading keys

**Solution**:
```bash
# Ensure serializer includes all fields
# Check serializers.py has RoomKeySerializer defined
python manage.py check

# Clear browser cache and reload
# Press F5 or Ctrl+Shift+R for hard refresh
```

### Issue 3: Migration Errors

**Problem**: Migration fails when running `migrate`

**Solution**:
```bash
# Check migration status
python manage.py showmigrations inventory

# If stuck, check for conflicting migrations
python manage.py migrate inventory 0005_item_room

# Then apply the key migration
python manage.py migrate inventory 0006_roomkey_keyauditlog
```

### Issue 4: Permission Denied on Key Operations

**Problem**: Getting 403 Forbidden on POST requests

**Solution**:
```bash
# Verify user is authenticated
# Check role permissions in views.py
# For testing, use admin account

# Or disable permissions temporarily (not recommended for production)
# Check permission_classes = [RolePermission] in views
```

### Issue 5: Dashboard Not Showing Keys

**Problem**: Keys section is empty or shows "Loading..."

**Solution**:
```bash
# Check browser console for errors (F12)
# Verify API is returning data:
curl http://127.0.0.1:8000/api/room-keys/

# Check CORS settings if hosted remotely
# Ensure API URLs in HTML match your server URL

# Check server logs for errors:
# Look at Django console output for 500 errors
```

### Issue 6: Seed Data Command Error

**Problem**: `seed_keys` command fails

**Solution**:
```bash
# Fix the import issue in seed_keys.py
python manage.py seed_keys

# If it fails, check:
# 1. File exists at: inventory/management/commands/seed_keys.py
# 2. __init__.py files exist in management/ and commands/ directories
# 3. Syntax errors in the Python file

# Manual check
python -c "from inventory.management.commands.seed_keys import Command"
```

## Database Verification

### Check Tables Were Created

```bash
# Using SQLite (if that's your database)
sqlite3 db.sqlite3 ".tables" | grep -E "room_keys|key_audit"

# Using Django ORM
python manage.py dbshell
# Then in SQL:
.tables
SELECT COUNT(*) FROM room_keys;
SELECT COUNT(*) FROM key_audit_log;
```

### Check Data Integrity

```python
# In Django shell
python manage.py shell

# Check models
from inventory.models import RoomKey, KeyAuditLog
print(f"Total keys: {RoomKey.objects.count()}")
print(f"Available keys: {RoomKey.objects.filter(status='available').count()}")
print(f"Audit logs: {KeyAuditLog.objects.count()}")

# Check a specific key
key = RoomKey.objects.first()
if key:
    print(f"Key: {key.key_number} - Room: {key.room_name}")
    print(f"Audit logs for this key: {key.audit_logs.count()}")
```

## Performance Testing

### Load Test with Sample Data

```bash
# Seed initial data
python manage.py seed_keys

# Create many keys for load testing
python manage.py shell

from inventory.models import RoomKey
for i in range(1, 101):
    RoomKey.objects.create(
        room_name=f"Room {i}",
        key_number=f"K-{i:04d}",
        status="available"
    )
print("Created 100 test keys")
```

### API Response Time Test

```bash
# Using curl with timing
curl -w "Response Time: %{time_total}s\n" \
  http://127.0.0.1:8000/api/room-keys/

# Using Python requests
import requests
import time

start = time.time()
response = requests.get('http://127.0.0.1:8000/api/room-keys/')
end = time.time()

print(f"Response time: {(end - start) * 1000:.2f}ms")
print(f"Status: {response.status_code}")
```

## Dashboard Testing Checklist

- [ ] Page loads without errors
- [ ] Room inventory section displays correctly
- [ ] Keys section loads and displays cards
- [ ] Status summary shows correct counts
- [ ] Color coding matches status (Green=Available, Blue=In Use, etc.)
- [ ] Auto-refresh works (check every 30 seconds)
- [ ] Mobile view is responsive
- [ ] No console errors (F12 > Console tab)
- [ ] API calls are successful (F12 > Network tab)

## Browser Console Debugging

1. Open browser DevTools: `F12`
2. Go to `Console` tab
3. Test API calls:

```javascript
// Test basic fetch
fetch('http://127.0.0.1:8000/api/room-keys/')
  .then(r => r.json())
  .then(d => console.log('Keys:', d))
  .catch(e => console.error('Error:', e))

// Test status summary
fetch('http://127.0.0.1:8000/api/room-keys/status_summary/')
  .then(r => r.json())
  .then(d => console.log('Summary:', d))
  .catch(e => console.error('Error:', e))
```

## Logs to Check

### Django Console Output
- Look for migration messages
- Check for 500 errors during API calls
- Monitor performance warnings

### Browser Console (F12)
- JavaScript errors
- CORS issues
- Network request failures

### Database Logs
- Query execution times
- Integrity violations
- Connection issues

## Reset & Cleanup

### Complete Reset (if needed)

```bash
# WARNING: This deletes all data!

# Delete migrations (keep 0001-0005)
rm inventory/migrations/0006_*.py

# Delete database
rm db.sqlite3

# Recreate migrations
python manage.py makemigrations

# Reapply migrations
python manage.py migrate

# Create superuser
python manage.py createsuperuser

# Seed data
python manage.py seed_keys
```

### Clear Specific Data

```bash
# Clear all keys
python manage.py shell
from inventory.models import RoomKey
RoomKey.objects.all().delete()

# Clear audit logs
from inventory.models import KeyAuditLog
KeyAuditLog.objects.all().delete()
```

## Performance Optimization Tips

1. **Use Database Indexes**: Already configured in migrations
2. **Cache Results**: Consider caching status_summary endpoint
3. **Pagination**: Add pagination to large key lists
4. **Filtering**: Use filter parameters to reduce data

## Monitoring

### Regular Checks

```bash
# Daily health check
python manage.py check

# Database statistics
python manage.py shell -c "
from inventory.models import RoomKey, KeyAuditLog
print(f'Total keys: {RoomKey.objects.count()}')
print(f'Total audits: {KeyAuditLog.objects.count()}')
print(f'Keys in use: {RoomKey.objects.filter(status=\"in_use\").count()}')
"

# Check for lost keys
python manage.py shell -c "
from inventory.models import RoomKey
lost = RoomKey.objects.filter(status='lost')
for key in lost:
    print(f'Lost: {key.key_number} - {key.room_name}')
"
```

---

**Version**: 1.0  
**Last Updated**: 2025-12-27  
**Status**: Production Ready
