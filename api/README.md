# Django + React Full Stack Application

This is a full-stack application with a Django REST API backend and a React frontend that communicates with authenticated endpoints.

## Project Structure

```
api/
├── api_project/          # Django project
│   ├── api_project/      # Django settings
│   ├── items/           # Django app for items
│   ├── manage.py
│   └── db.sqlite3
└── react-frontend/      # React application
    ├── src/
    │   ├── components/
    │   ├── services/
    │   └── App.jsx
    └── package.json
```

## Features

- JWT-based authentication
- CRUD operations for Items
- React frontend with login/logout functionality
- CORS enabled for frontend-backend communication

## Setup Instructions

### Backend (Django)

1. Navigate to the Django project:
   ```bash
   cd api/api_project
   ```

2. Activate virtual environment:
   ```bash
   # On Windows
   ..\..\.venv\Scripts\Activate.ps1
   ```

3. Run migrations (if needed):
   ```bash
   python manage.py migrate
   ```

4. Create a superuser:
   ```bash
   python manage.py createsuperuser
   ```

5. Start the Django server:
   ```bash
   python manage.py runserver
   ```

The API will be available at `http://127.0.0.1:8000/`

### Frontend (React)

1. Navigate to the React project:
   ```bash
   cd api/react-frontend
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

3. Start the development server:
   ```bash
   npm run dev
   ```

The React app will be available at `http://localhost:5173/`

## API Endpoints

### Authentication
- `POST /api/token/` - Obtain JWT token pair
- `POST /api/token/refresh/` - Refresh access token

### Items (Authenticated)
- `GET /api/items/` - List all items
- `POST /api/items/` - Create new item
- `GET /api/items/{id}/` - Get specific item
- `PUT /api/items/{id}/` - Update item
- `DELETE /api/items/{id}/` - Delete item

## Usage

1. Start both the Django backend and React frontend servers
2. Open the React app in your browser
3. Login with your Django superuser credentials
4. You can now create, read, update, and delete items

## Technologies Used

- **Backend**: Django, Django REST Framework, Simple JWT, CORS Headers
- **Frontend**: React, Axios, Vite
- **Database**: SQLite (development)</content>
<parameter name="filePath">c:\Users\henz\Desktop\api\README.md