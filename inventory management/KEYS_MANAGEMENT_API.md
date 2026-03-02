# Keys Management API Documentation

## Overview
The Keys Management system allows tracking and management of room keys with full audit logs.

## Models

### RoomKey Model
- `key_id`: Auto-increment primary key
- `room_name`: Name of the room (e.g., "Room 101")
- `key_number`: Unique key identifier (e.g., "K-001")
- `description`: Optional description of the key
- `status`: Key status (available, in_use, lost, maintenance)
- `assigned_to`: User who currently has the key (ForeignKey to User)
- `assigned_date`: When the key was assigned
- `last_location`: Last known location of the key
- `created_at`: When the key was created
- `updated_at`: Last update timestamp

### KeyAuditLog Model
- `log_id`: Auto-increment primary key
- `key`: Reference to RoomKey
- `action`: Action type (assigned, returned, lost, found, maintenance, restored, created)
- `performed_by`: User who performed the action
- `notes`: Additional notes about the action
- `timestamp`: When the action occurred

## API Endpoints

### List All Room Keys
```
GET /api/room-keys/
```

**Query Parameters:**
- `room_name`: Filter by room name
- `status`: Filter by status (available, in_use, lost, maintenance)
- `search`: Search by key_number or room_name

**Response:**
```json
[
  {
    "key_id": 1,
    "room_name": "Room 101",
    "key_number": "K-001",
    "description": "Master key for Room 101",
    "status": "available",
    "assigned_to": null,
    "assigned_to_name": null,
    "assigned_date": null,
    "last_location": "Storage Cabinet",
    "created_at": "2025-12-27T10:00:00Z",
    "updated_at": "2025-12-27T10:00:00Z"
  }
]
```

### Create New Key
```
POST /api/room-keys/
```

**Request Body:**
```json
{
  "room_name": "Room 101",
  "key_number": "K-001",
  "description": "Master key for Room 101",
  "status": "available",
  "last_location": "Storage Cabinet"
}
```

### Update Key
```
PATCH /api/room-keys/{key_id}/
```

### Delete Key
```
DELETE /api/room-keys/{key_id}/
```

### Assign Key to User
```
POST /api/room-keys/{key_id}/assign/
```

**Request Body:**
```json
{
  "user_id": 5,
  "notes": "Assigned for maintenance work"
}
```

**Response:**
```json
{
  "status": "assigned",
  "message": "Key assigned to John Doe"
}
```

### Return Key
```
POST /api/room-keys/{key_id}/return_key/
```

**Request Body:**
```json
{
  "location": "Key Cabinet",
  "notes": "Returned after use"
}
```

**Response:**
```json
{
  "status": "returned",
  "message": "Key returned successfully"
}
```

### Mark Key as Lost
```
POST /api/room-keys/{key_id}/mark_lost/
```

**Request Body:**
```json
{
  "notes": "Lost during maintenance"
}
```

**Response:**
```json
{
  "status": "lost",
  "message": "Key marked as lost"
}
```

### Send Key to Maintenance
```
POST /api/room-keys/{key_id}/send_maintenance/
```

**Request Body:**
```json
{
  "notes": "Broken lock, needs repair"
}
```

**Response:**
```json
{
  "status": "maintenance",
  "message": "Key sent to maintenance"
}
```

### Get Keys by Room
```
GET /api/room-keys/by_room/?room=Room%20101
```

**Response:** Array of RoomKey objects for the specified room

### Get Keys Status Summary
```
GET /api/room-keys/status_summary/
```

**Response:**
```json
{
  "total_keys": 15,
  "by_status": [
    {
      "status": "available",
      "count": 10
    },
    {
      "status": "in_use",
      "count": 3
    },
    {
      "status": "lost",
      "count": 1
    },
    {
      "status": "maintenance",
      "count": 1
    }
  ],
  "available_keys": 10,
  "in_use_keys": 3,
  "lost_keys": 1,
  "maintenance_keys": 1
}
```

### List Key Audit Logs
```
GET /api/key-audit-logs/
```

**Query Parameters:**
- `key`: Filter by key_id
- `action`: Filter by action type
- `search`: Search by key_number or room_name

**Response:**
```json
[
  {
    "log_id": 1,
    "key": 1,
    "key_number": "K-001",
    "room_name": "Room 101",
    "action": "assigned",
    "performed_by": 5,
    "performed_by_name": "John Doe",
    "notes": "Assigned for maintenance work",
    "timestamp": "2025-12-27T10:30:00Z"
  }
]
```

## Status Codes

- **200 OK**: Successful GET/PATCH request
- **201 Created**: Successful POST request
- **204 No Content**: Successful DELETE request
- **400 Bad Request**: Invalid request data
- **404 Not Found**: Resource not found
- **403 Forbidden**: Permission denied

## Usage Examples

### Python/Requests
```python
import requests

# Get all keys
response = requests.get('http://127.0.0.1:8000/api/room-keys/')
keys = response.json()

# Create a new key
data = {
    'room_name': 'Room 102',
    'key_number': 'K-002',
    'description': 'Key for Room 102'
}
response = requests.post('http://127.0.0.1:8000/api/room-keys/', json=data)

# Assign key to user
response = requests.post(
    'http://127.0.0.1:8000/api/room-keys/1/assign/',
    json={'user_id': 5, 'notes': 'For maintenance'}
)
```

### cURL
```bash
# Get all keys
curl http://127.0.0.1:8000/api/room-keys/

# Create key
curl -X POST http://127.0.0.1:8000/api/room-keys/ \
  -H "Content-Type: application/json" \
  -d '{
    "room_name": "Room 102",
    "key_number": "K-002",
    "description": "Key for Room 102"
  }'

# Assign key
curl -X POST http://127.0.0.1:8000/api/room-keys/1/assign/ \
  -H "Content-Type: application/json" \
  -d '{"user_id": 5, "notes": "For maintenance"}'
```

## Dashboard Features

The keys management section in the dashboard displays:

1. **Key Cards**: Visual representation of each key with:
   - Key number
   - Room name
   - Current status (color-coded)
   - Assigned person
   - Last known location
   - Assignment date

2. **Status Summary**: Overview showing:
   - Total number of keys
   - Count by status (Available, In Use, Lost, Maintenance)
   - Visual indicators for quick status assessment

3. **Real-time Updates**: Auto-refresh every 30 seconds

## Permissions

- **Admin**: Full access to all key operations
- **Manager**: Can view and manage key assignments
- **Viewer**: Can view keys only
- **Staff**: Can view keys only

## Audit Trail

Every action on a key is logged in the KeyAuditLog, including:
- Who performed the action
- What action was performed
- When it was performed
- Any additional notes

This provides a complete history of key movements and status changes.
