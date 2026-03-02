# 📦 Inventory Management System  
### Room-wise Inventory & Room Keys Tracking with Audit Logging

A full-featured **Inventory Management System** built with Django that includes:

- 📦 Room-wise inventory tracking  
- 🔑 Complete Room Keys Management  
- 📊 Real-time dashboard visualization  
- 🔐 Role-based access control  
- 📝 Full audit logging  
- 🌐 RESTful API support  

---

## 🚀 Features

### 📦 Inventory Management
- Organize items by physical room
- Track quantities per location
- Low stock alerts
- Room-based filtering

### 🔑 Room Keys Tracking System
- Create and manage room keys
- Assign and return keys
- Mark keys as lost
- Send keys to maintenance
- Track current holder
- Track last known location
- Color-coded key status display

### 📝 Audit Logging
- Complete action history
- Assignment tracking
- Return tracking
- Lost & maintenance logs
- User-based activity records

### 🔐 Role-Based Access Control

| Role    | Permissions |
|----------|------------|
| Admin    | Full CRUD + All Actions |
| Manager  | CRUD + Assign/Return |
| Staff    | Read-only |
| Viewer   | Read-only |

---

## 🛠 Tech Stack

- **Backend:** Django 4.2
- **API:** Django REST Framework
- **Database:** SQLite (configurable)
- **Frontend:** HTML, CSS, JavaScript
- **Language:** Python 3.x

---

## 📂 Project Structure

```
inventory-management/
│
├── backend/
│   ├── inventory/
│   │   ├── models.py
│   │   ├── views.py
│   │   ├── serializers.py
│   │   ├── urls.py
│   │   ├── migrations/
│   │   └── management/commands/
│   │       └── seed_keys.py
│   │
│   ├── templates/
│   │   └── roomwise-inventory.html
│   │
│   ├── manage.py
│   └── db.sqlite3
│
├── KEYS_QUICK_START.md
├── KEYS_MANAGEMENT_API.md
├── IMPLEMENTATION_SUMMARY.md
├── TESTING_AND_TROUBLESHOOTING.md
└── README.md
```

---

## ⚙️ Installation Guide

### 1️⃣ Clone Repository

```bash
git clone https://github.com/your-username/inventory-management.git
cd inventory-management/backend
```

### 2️⃣ Create Virtual Environment

```bash
python -m venv venv
venv\Scripts\activate   # Windows
```

### 3️⃣ Install Dependencies

```bash
pip install -r requirements.txt
```

### 4️⃣ Apply Migrations

```bash
python manage.py migrate
```

### 5️⃣ Run Development Server

```bash
python manage.py runserver
```

Visit:

```
http://127.0.0.1:8000/
```

---

## 🔌 API Endpoints

### Room Keys

| Method | Endpoint | Description |
|--------|----------|------------|
| GET | `/api/room-keys/` | List all keys |
| POST | `/api/room-keys/` | Create key |
| GET | `/api/room-keys/{id}/` | Retrieve key |
| PATCH | `/api/room-keys/{id}/` | Update key |
| DELETE | `/api/room-keys/{id}/` | Delete key |
| POST | `/api/room-keys/{id}/assign/` | Assign key |
| POST | `/api/room-keys/{id}/return_key/` | Return key |
| POST | `/api/room-keys/{id}/mark_lost/` | Mark as lost |
| POST | `/api/room-keys/{id}/send_maintenance/` | Send to maintenance |
| GET | `/api/room-keys/status_summary/` | Status summary |
| GET | `/api/room-keys/by_room/?room=Room%20101` | Filter by room |

### Audit Logs

| Method | Endpoint | Description |
|--------|----------|------------|
| GET | `/api/key-audit-logs/` | View all logs |
| GET | `/api/key-audit-logs/?key=1` | Logs by key |
| GET | `/api/key-audit-logs/?action=assigned` | Logs by action |

---

## 📊 Key Status Types

| Status | Meaning |
|---------|---------|
| Available | Key is in storage |
| In Use | Currently assigned |
| Lost | Missing |
| Maintenance | Under repair |

---

## 🧪 Testing

### Seed Sample Data

```bash
python manage.py seed_keys
```

### Quick API Test

```bash
curl http://127.0.0.1:8000/api/room-keys/
```

---

## 📈 Dashboard

Includes:

- Room-wise inventory overview
- Visual key cards
- Status summary counters
- Auto-refresh (30 seconds)
- Responsive layout

Dashboard file:

```
backend/templates/roomwise-inventory.html
```

---

## 🔍 Database Schema Overview

### room_keys
- key_id (Primary Key)
- room_name
- key_number (Unique)
- description
- status
- assigned_to
- assigned_date
- last_location
- created_at
- updated_at

### key_audit_log
- log_id (Primary Key)
- key_id (Foreign Key)
- action
- performed_by
- notes
- timestamp

---

## 🚀 Production Notes

- Designed for scalability
- Easily configurable for PostgreSQL/MySQL
- Modular architecture
- RESTful API design

---

## 👨‍💻 Author

**Atikmorshed Sohan**

---

## 📄 License

This project is for educational and portfolio purposes.
