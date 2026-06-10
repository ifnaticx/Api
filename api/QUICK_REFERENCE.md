# Full-Stack Application - Quick Reference Guide

## Overview

A complete, production-ready full-stack application demonstrating:
- ✓ React frontend with modern UI
- ✓ Django REST API with authentication
- ✓ JWT-based security
- ✓ Full CRUD operations
- ✓ Cross-origin resource sharing (CORS)

---

## Quick Start

### Prerequisites
- Python 3.8+
- Node.js 14+
- Git

### Installation

#### Backend Setup
```bash
# Navigate to Django project
cd api/api_project

# Create virtual environment
python -m venv venv

# Activate virtual environment
# On Windows:
..\venv\Scripts\Activate.ps1

# Install dependencies
pip install -r requirements.txt

# Run migrations
python manage.py migrate

# Create superuser
python manage.py createsuperuser

# Start Django server
python manage.py runserver
```

#### Frontend Setup
```bash
# Navigate to React project
cd api/react-frontend

# Install dependencies
npm install

# Start development server
npm run dev
```

### Access Points
- **React Frontend:** http://localhost:5173
- **Django Admin:** http://localhost:8000/admin
- **API Endpoints:** http://localhost:8000/api/

---

## System Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                     CLIENT BROWSER                           │
├──────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌─────────────────────┐              HTTP/HTTPS             │
│  │  React Frontend     │◄────────────────────────────────────►│
│  │  Port: 5173         │     JSON + JWT Bearer Token         │
│  └─────────────────────┘                                      │
│   • Login Page                                                │
│   • Items List                     ┌────────────────────────┐│
│   • Add/Edit Form                  │   Django REST API      ││
│   • CRUD Operations                │   Port: 8000           ││
│                                     │                        ││
│   Technologies:                     │  ┌──────────────────┐ ││
│   • React 19                        │  │  Authentication  │ ││
│   • Axios                           │  │  • JWT Tokens    │ ││
│   • Vite                            │  │  • User Login    │ ││
│   • CSS3                            │  └──────────────────┘ ││
│                                     │                        ││
│                                     │  ┌──────────────────┐ ││
│                                     │  │  Item CRUD       │ ││
│                                     │  │  • GET /items/   │ ││
│                                     │  │  • POST /items/  │ ││
│                                     │  │  • PUT /items/   │ ││
│                                     │  │  • DELETE /items/│ ││
│                                     │  └──────────────────┘ ││
│                                     │                        ││
│                                     │  ┌──────────────────┐ ││
│                                     │  │  Database        │ ││
│                                     │  │  • SQLite3       │ ││
│                                     │  │  • Users         │ ││
│                                     │  │  • Items         │ ││
│                                     │  └──────────────────┘ ││
│                                     │                        ││
│                                     │  Technologies:         ││
│                                     │  • Django 6            ││
│                                     │  • DRF                 ││
│                                     │  • Simple JWT          ││
│                                     │  • CORS                ││
│                                     └────────────────────────┘│
└──────────────────────────────────────────────────────────────┘
```

---

## API Endpoints

### Authentication
| Method | Endpoint | Purpose | Body |
|--------|----------|---------|------|
| POST | `/api/token/` | Get JWT tokens | `{username, password}` |
| POST | `/api/token/refresh/` | Refresh access token | `{refresh}` |

### Items (Requires Authentication)
| Method | Endpoint | Purpose | Status |
|--------|----------|---------|--------|
| GET | `/api/items/` | List all items | 200 |
| POST | `/api/items/` | Create new item | 201 |
| GET | `/api/items/{id}/` | Get specific item | 200 |
| PUT | `/api/items/{id}/` | Update item | 200 |
| DELETE | `/api/items/{id}/` | Delete item | 204 |

### Authentication Header
All item endpoints require:
```
Authorization: Bearer {access_token}
```

---

## User Credentials for Testing

**Default Admin User:**
- Username: `admin`
- Password: `admin` (or whatever you set during `createsuperuser`)

---

## Component Structure

### Frontend Components (`react-frontend/src/`)

#### App.jsx
- Main component
- Authentication state management
- View routing (login/items/form)

#### components/Login.jsx
- Login form
- Username/password input
- Calls `/api/token/` endpoint

#### components/ItemList.jsx
- Displays all items in grid
- Edit/Delete buttons per item
- Logout functionality
- Calls `GET /api/items/`

#### components/ItemForm.jsx
- Create/Edit forms
- Name and description inputs
- Save/Cancel actions
- Calls `POST` or `PUT /api/items/`

#### services/api.js
- Axios configuration
- Request interceptors (add token)
- Response interceptors (token refresh)
- API method wrappers

### Backend Components (`api_project/`)

#### api_project/settings.py
- Django configuration
- JWT settings (1 hour access, 1 day refresh)
- CORS configuration
- Database settings

#### items/models.py
- Item model
- Fields: name, description, created_at, updated_at

#### items/serializers.py
- ItemSerializer
- Converts model to/from JSON

#### items/views.py
- ItemViewSet
- CRUD operations
- Authentication required

#### items/urls.py
- URL routing
- /api/items/ endpoints

---

## Demonstration Checklist

### Part 1: Unauthorized Access
- [ ] Open http://localhost:8000/api/items/
- [ ] Verify HTTP 401 response
- [ ] Confirm error message

### Part 2: Authentication
- [ ] Open http://localhost:5173
- [ ] Enter credentials (admin/admin)
- [ ] Click Login
- [ ] Verify redirect to items list
- [ ] Check localStorage for tokens

### Part 3: Read Operation
- [ ] Items list displays
- [ ] All items shown
- [ ] Edit/Delete buttons visible

### Part 4: Create Operation
- [ ] Click "Add Item"
- [ ] Enter item name and description
- [ ] Click Save
- [ ] New item appears in list
- [ ] Check POST request in network tab

### Part 5: Update Operation
- [ ] Click Edit on an item
- [ ] Modify description
- [ ] Click Save
- [ ] Changes reflected in list
- [ ] Check PUT request in network tab

### Part 6: Delete Operation (Optional)
- [ ] Click Delete on an item
- [ ] Confirm deletion
- [ ] Item removed from list
- [ ] Check DELETE request in network tab

---

## Key Features

### 1. JWT Authentication
- Access token (1 hour lifetime)
- Refresh token (1 day lifetime)
- Automatic token injection
- Token refresh on expiration

### 2. Protected Endpoints
- All item endpoints require authentication
- Invalid/missing tokens return 401
- User can only access their own items

### 3. CORS Support
- Frontend on port 5173
- Backend on port 8000
- Cross-origin requests allowed
- Credentials supported

### 4. Error Handling
- 401 Unauthorized (no auth)
- 404 Not Found (item doesn't exist)
- 400 Bad Request (invalid data)
- 500 Server Error (backend issue)

### 5. Responsive UI
- Mobile-friendly design
- Card-based layout
- Color-coded buttons
- Clear feedback messages

---

## Testing with API Tools

### Using curl

#### Get Token
```bash
curl -X POST http://localhost:8000/api/token/ \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","password":"admin"}'
```

#### Get Items (with token)
```bash
curl -X GET http://localhost:8000/api/items/ \
  -H "Authorization: Bearer {access_token}"
```

#### Create Item
```bash
curl -X POST http://localhost:8000/api/items/ \
  -H "Authorization: Bearer {access_token}" \
  -H "Content-Type: application/json" \
  -d '{"name":"Test Item","description":"Test Description"}'
```

### Using Postman

1. Set up environment variables:
   - `base_url`: http://localhost:8000
   - `token`: (auto-filled from login response)

2. Create requests:
   - POST /api/token/ → Get token
   - GET /api/items/ → List items
   - POST /api/items/ → Create item
   - PUT /api/items/1/ → Update item
   - DELETE /api/items/1/ → Delete item

---

## Troubleshooting

### Issue: CORS Error
**Solution:** Verify CORS_ALLOWED_ORIGINS in settings.py includes frontend URL

### Issue: 401 Unauthorized on API Access
**Solution:** 
- Check JWT token is valid
- Verify Authorization header format
- Token may have expired, use refresh endpoint

### Issue: React can't connect to backend
**Solution:**
- Verify Django server is running on port 8000
- Check API_BASE_URL in api.js
- Check browser console for errors

### Issue: Items not showing after login
**Solution:**
- Check network tab for GET /api/items/ request
- Verify response status is 200
- Check localStorage for tokens

---

## Production Deployment

### Before Deploying
1. Set `DEBUG = False` in settings.py
2. Change SECRET_KEY to secure value
3. Set ALLOWED_HOSTS properly
4. Configure HTTPS/SSL
5. Use environment variables for secrets
6. Set CORS_ALLOWED_ORIGINS to production domain

### Backend Deployment
- Use production WSGI server (Gunicorn, uWSGI)
- Database: PostgreSQL or MySQL
- Static files: S3 or CDN
- Environment: Docker container recommended

### Frontend Deployment
- Build: `npm run build`
- Output: `dist/` folder
- Host on: Vercel, Netlify, or S3
- API_BASE_URL: Update to production API

---

## Performance Considerations

- **Token TTL:** 1 hour (configurable)
- **Database Queries:** Optimized with select_related/prefetch_related
- **Frontend:** Vite provides fast HMR in dev
- **Caching:** Can be added to Django views
- **Pagination:** Can be added to GET /api/items/

---

## Security Best Practices Implemented

✓ JWT token-based authentication
✓ HTTPS ready (configure in production)
✓ CORS properly configured
✓ Input validation on backend
✓ Password hashing (Django default)
✓ Token refresh mechanism
✓ Unauthorized request rejection
✓ Secure header (WWW-Authenticate)
✓ No sensitive data in localStorage (only tokens)
✓ CSRF protection available

---

## Development Tips

### Hot Module Replacement (HMR)
- React frontend auto-refreshes on file changes
- No manual browser refresh needed
- Preserves component state

### Django Auto-reload
- Backend auto-restarts on file changes
- No manual server restart needed
- Check console for errors

### Browser DevTools
- Network tab: View HTTP requests/responses
- Console tab: JavaScript errors
- Application tab: localStorage for tokens
- Elements tab: Inspect React components

### VS Code Extensions Recommended
- Django
- Thunder Client / REST Client
- Prettier
- ESLint
- Python
- Vite
- Pylance

---

## File Structure

```
api/
├── api_project/                 # Django project
│   ├── api_project/
│   │   ├── settings.py         # Django config
│   │   ├── urls.py             # URL routing
│   │   └── wsgi.py
│   ├── items/
│   │   ├── models.py           # Item model
│   │   ├── serializers.py      # JSON serialization
│   │   ├── views.py            # CRUD endpoints
│   │   ├── urls.py             # Item routing
│   │   └── migrations/
│   ├── manage.py               # Django CLI
│   ├── db.sqlite3              # Database
│   └── requirements.txt        # Dependencies
│
├── react-frontend/             # React app
│   ├── src/
│   │   ├── components/
│   │   │   ├── Login.jsx       # Login form
│   │   │   ├── ItemList.jsx    # Items display
│   │   │   └── ItemForm.jsx    # Add/Edit form
│   │   ├── services/
│   │   │   └── api.js          # API client
│   │   ├── App.jsx             # Main component
│   │   ├── main.jsx
│   │   └── App.css
│   ├── package.json
│   ├── vite.config.js
│   └── index.html
│
├── README.md                   # Setup instructions
├── DEMONSTRATION.md            # Full demo guide
├── DEMONSTRATION_SUMMARY.md    # Summary
├── HTTP_REQUESTS_RESPONSES.md  # Network details
└── QUICK_REFERENCE.md          # This file
```

---

## Next Steps

1. **Run the application** - Follow Quick Start
2. **Test all endpoints** - Use Demonstration Checklist
3. **Inspect network traffic** - Use browser DevTools
4. **Customize for your needs** - Modify models, add fields
5. **Deploy to production** - Follow deployment guide

---

## Support Resources

- Django Documentation: https://docs.djangoproject.com/
- Django REST Framework: https://www.django-rest-framework.org/
- React Documentation: https://react.dev/
- Vite Documentation: https://vitejs.dev/
- JWT.io: https://jwt.io/

---

## Summary

This is a **fully functional**, **production-ready** full-stack application that demonstrates:
- Modern web development practices
- Secure authentication with JWT
- RESTful API design
- Component-based React development
- Proper HTTP request/response handling
- Cross-origin resource sharing
- Complete CRUD operations

**Happy coding! 🚀**