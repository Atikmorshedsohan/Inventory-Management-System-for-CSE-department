# Inventory Management System (CSE Department)

A full-stack Inventory Management System built with Django REST Framework (Backend) and React (Frontend) for managing CSE department inventory.

## Features

- 📊 **Dashboard** - Overview of inventory statistics
- 📦 **Item Management** - Add, edit, delete items with categories and units
- 🏢 **Supplier Management** - Track supplier information
- 💰 **Purchase Tracking** - Record purchases from suppliers
- 📋 **Requisitions** - Staff can request items with purpose
- ✅ **Approval Workflow** - Admin approves/rejects requisitions
- 📈 **Reports & Analytics** - Inventory, purchase, and requisition reports
- 🔍 **Audit Log** - Track all user actions
- 🔐 **User Authentication** - JWT-based authentication (admin/staff roles)
- 📱 **Responsive Design** - Works on desktop and mobile

## Database Schema

Based on the schema from: https://dbdiagram.io/d/67d9a11a75d75cc8448ecc04

### Tables
- **users** - User accounts (admin/staff)
- **categories** - Item categories
- **items** - Inventory items (CSE department)
- **suppliers** - Supplier information
- **purchases** - Purchase records
- **stock_transactions** - Stock IN/OUT/ADJUST
- **requisitions** - Item requests from staff
- **requisition_items** - Items in each requisition
- **audit_log** - Action tracking

## Tech Stack

### Backend
- Django 4.2.7
- Django REST Framework
- Django Filter
- SQLite/PostgreSQL
- JWT Authentication
- Django CORS Headers
- Swagger/ReDoc API Documentation

### Frontend
- React 18.2.0
- React Router DOM
- Axios

## Project Structure

```
inventory management/
├── backend/
│   ├── inventory_backend/      # Django project settings
│   ├── users/                   # User management (admin/staff)
│   ├── products/                # Items, categories, stock transactions
│   ├── suppliers/               # Suppliers and purchases
│   ├── requisitions/            # Requisitions and requisition items
│   ├── audit/                   # Audit logging
│   ├── reports/                 # Reports and analytics
│   └── manage.py
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/          # React components
│   │   ├── services/            # API services
│   │   ├── App.js
│   │   └── index.js
│   └── package.json
├── requirements.txt
├── README.md
└── MIGRATION_GUIDE.md
```

## Installation & Setup

### Prerequisites
- Python 3.8+
- Node.js 16+
- npm or yarn

### Backend Setup

1. **Navigate to the backend directory:**
   ```bash
   cd "d:/inventory management/backend"
   ```

2. **Create and activate a virtual environment:**
   ```bash
   # Windows
   python -m venv venv
   venv\Scripts\activate

   # Linux/Mac
   python3 -m venv venv
   source venv/bin/activate
   ```

3. **Install dependencies:**
   ```bash
   pip install -r ../requirements.txt
   ```

4. **Run migrations:**
   ```bash
   python manage.py makemigrations
   python manage.py migrate
5. **Create a superuser:**
   ```bash
   python manage.py createsuperuser
   # Enter email (e.g., admin@cse.edu), name, and password
   ```bash
   python manage.py createsuperuser
   ```

6. **Run the development server:**
   ```bash
   python manage.py runserver
   ```

   The backend will be available at `http://localhost:8000`

### Frontend Setup

1. **Open a new terminal and navigate to the frontend directory:**
   ```bash
   cd "d:/inventory management/frontend"
   ```

2. **Install dependencies:**
   ```bash
   npm install
   ```

3. **Start the development server:**
   ```bash
   npm start
   ```

   The frontend will be available at `http://localhost:3000`

## API Documentation

Once the backend is running, you can access:

- **Swagger UI:** http://localhost:8000/swagger/
## API Endpoints

### Authentication
- `POST /api/users/register/` - Register new user
- `POST /api/users/login/` - Login (email & password)
- `POST /api/users/token/refresh/` - Refresh JWT token
- `GET /api/users/profile/` - Get user profile
- `GET /api/users/list/` - List all users

### Categories
- `GET /api/products/categories/` - List categories
- `POST /api/products/categories/` - Create category

### Items
- `GET /api/products/items/` - List all items
- `POST /api/products/items/` - Create item
- `GET /api/products/items/{id}/` - Get item details
- `PUT /api/products/items/{id}/` - Update item
- `DELETE /api/products/items/{id}/` - Delete item
- `GET /api/products/items/low_stock/` - Get low stock items
- `GET /api/products/items/out_of_stock/` - Get out of stock items

### Stock Transactions
- `GET /api/products/transactions/` - List transactions
- `POST /api/products/transactions/` - Create transaction (IN/OUT/ADJUST)
  - Note: Automatically updates item quantities

### Suppliers
- `GET /api/suppliers/list/` - List all suppliers
- `POST /api/suppliers/list/` - Create supplier
- `PUT /api/suppliers/list/{id}/` - Update supplier
- `DELETE /api/suppliers/list/{id}/` - Delete supplier

### Purchases
- `GET /api/suppliers/purchases/` - List purchases
- `POST /api/suppliers/purchases/` - Create purchase
  - Note: Automatically creates IN transaction and updates stock

### Requisitions
- `GET /api/requisitions/` - List requisitions
- `POST /api/requisitions/` - Create requisition
- `GET /api/requisitions/{id}/` - Get requisition details
- `PUT /api/requisitions/{id}/` - Update requisition
- `POST /api/requisitions/{id}/approve/` - Approve requisition
- `POST /api/requisitions/{id}/reject/` - Reject requisition
- `POST /api/requisitions/{id}/issue/` - Issue items (creates OUT transactions)

### Reports
- `GET /api/reports/dashboard/` - Dashboard statistics
- `GET /api/reports/inventory/` - Inventory report
- `GET /api/reports/purchases/?days=30` - Purchase report (default 30 days)
- `GET /api/reports/requisitions/` - Requisition analyticscs
- `GET /api/reports/sales/` - Sales report
- `GET /api/reports/inventory/` - Inventory report
- `GET /api/reports/customers/` - Customer analytics

## Default Login Credentials

After creating a superuser, you can use those credentials to login.

For testing purposes, you can also register a new user through the registration page at `http://localhost:3000/register`

## Development Workflow

1. **Start Backend:**
   ```bash
   cd backend
   python manage.py runserver
   ```

2. **Start Frontend (in a new terminal):**
   ```bash
   cd frontend
   npm start
   ```

3. **Access the application:**
   - Frontend: http://localhost:3000
   - Backend API: http://localhost:8000
   - Admin Panel: http://localhost:8000/admin

## Database

The project uses SQLite by default for development. To use PostgreSQL:

1. Update `backend/inventory_backend/settings.py`:
   ```python
   DATABASES = {
       'default': {
           'ENGINE': 'django.db.backends.postgresql',
           'NAME': 'inventory_db',
           'USER': 'your_username',
           'PASSWORD': 'your_password',
           'HOST': 'localhost',
           'PORT': '5432',
       }
   }
   ```

2. Run migrations again:
   ```bash
   python manage.py migrate
   ```

## Production Deployment

### Backend
1. Set `DEBUG = False` in settings.py
2. Configure proper SECRET_KEY
3. Set up production database (PostgreSQL recommended)
4. Configure ALLOWED_HOSTS
5. Collect static files: `python manage.py collectstatic`
6. Use a production WSGI server (gunicorn, uWSGI)

### Frontend
1. Build the production version:
   ```bash
   npm run build
   ```
2. Serve the build folder using a web server (nginx, Apache)

## Contributing

1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Create a Pull Request

## License

This project is open source and available under the MIT License.

## Support

For issues and questions, please create an issue in the repository.
