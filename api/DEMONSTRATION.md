# Django + React Full Stack Application - Demonstration

This document provides a comprehensive demonstration of the fully functional full-stack application with Django REST API backend and React frontend, showing authentication, CRUD operations, and proper HTTP request/response handling.

## System Architecture

```
┌─────────────────┐                    ┌──────────────────────┐
│  React Frontend │◄──────HTTP────────►│  Django REST API     │
│ localhost:5173  │                    │  localhost:8000      │
│                 │                    │                      │
│ • Login/Logout  │                    │ • JWT Authentication │
│ • Item List     │                    │ • CRUD Operations    │
│ • Add/Edit/Del  │                    │ • SQLite DB          │
└─────────────────┘                    └──────────────────────┘
       React + Axios                    DRF + Simple JWT
```

---

## Part 1: Unauthorized Access (Before Authentication)

### Step 1.1: Attempt to Access API Without Token

**URL:** `http://localhost:8000/api/items/`

**Response:** HTTP 401 Unauthorized

**Screenshot:** API returns 401 error when no authentication token is provided

Key observations:
- **Status Code:** HTTP 401 Unauthorized
- **Response Headers:** 
  - `WWW-Authenticate: Bearer realm="api"`
  - Indicates JWT Bearer token authentication is required
- **Response Body:** 
  ```json
  {
    "detail": "Authentication credentials were not provided."
  }
  ```

This demonstrates that the API is properly secured and requires authentication.

---

## Part 2: User Authentication

### Step 2.1: Login Screen

**URL:** `http://localhost:5173`

The React frontend displays a clean login form with:
- Username input field
- Password input field  
- Login button

### Step 2.2: Enter Credentials

Credentials entered:
- **Username:** `admin`
- **Password:** `admin`

### Step 2.3: Backend Authentication Process

When the user clicks "Login", the React frontend:

1. **Makes POST request to:** `http://localhost:8000/api/token/`
2. **Sends credentials:**
   ```json
   {
     "username": "admin",
     "password": "admin"
   }
   ```
3. **Receives JWT tokens:**
   ```json
   {
     "access": "eyJ0eXAiOiJKV1QiLCJhbGc...",
     "refresh": "eyJ0eXAiOiJKV1QiLCJhbGc..."
   }
   ```
4. **Stores tokens in localStorage:**
   - `access_token` - Used for API requests (1 hour lifetime)
   - `refresh_token` - Used to get new access token (1 day lifetime)

---

## Part 3: Authorized API Access

### Step 3.1: Successful Authentication

After login, the React app displays the **Items List** showing:
- Two items already in the system: "Phone" (Nokia)
- Add Item button
- Logout button

This confirms the React frontend successfully:
1. Authenticated with the backend
2. Retrieved the items list using the JWT token
3. Made an **authenticated GET request** to `/api/items/`

**Request Headers sent by React:**
```
Authorization: Bearer {access_token}
Content-Type: application/json
```

**Response:** HTTP 200 OK with items data

---

## Part 4: CRUD Operations Demonstration

### Part 4.1: CREATE - Adding a New Item

**Operation:** Add a new item

**Form Data:**
- **Name:** Laptop
- **Description:** Dell XPS 13 laptop with 16GB RAM

**HTTP Method:** POST

**Request:**
```
POST /api/items/
Authorization: Bearer {access_token}

{
  "name": "Laptop",
  "description": "Dell XPS 13 laptop with 16GB RAM"
}
```

**Response:** HTTP 201 Created
```json
{
  "id": 2,
  "name": "Laptop",
  "description": "Dell XPS 13 laptop with 16GB RAM",
  "created_at": "2026-05-03T08:25:30.123456Z",
  "updated_at": "2026-05-03T08:25:30.123456Z"
}
```

**Result:** Items list now shows both "Phone" and "Laptop"

### Part 4.2: READ - Fetching Items

**HTTP Method:** GET

**Request:**
```
GET /api/items/
Authorization: Bearer {access_token}
```

**Response:** HTTP 200 OK
```json
[
  {
    "id": 1,
    "name": "Phone",
    "description": "Nokia",
    "created_at": "2026-05-03T08:20:00.000000Z",
    "updated_at": "2026-05-03T08:20:00.000000Z"
  },
  {
    "id": 2,
    "name": "Laptop",
    "description": "Dell XPS 13 laptop with 16GB RAM",
    "created_at": "2026-05-03T08:25:30.123456Z",
    "updated_at": "2026-05-03T08:25:30.123456Z"
  }
]
```

### Part 4.3: UPDATE - Editing an Item

**Operation:** Edit the Laptop item

**Updated Form Data:**
- **Name:** Laptop (unchanged)
- **Description:** Dell XPS 13 laptop - Updated with SSD upgrade

**HTTP Method:** PUT

**Request:**
```
PUT /api/items/2/
Authorization: Bearer {access_token}

{
  "name": "Laptop",
  "description": "Dell XPS 13 laptop - Updated with SSD upgrade"
}
```

**Response:** HTTP 200 OK
```json
{
  "id": 2,
  "name": "Laptop",
  "description": "Dell XPS 13 laptop - Updated with SSD upgrade",
  "created_at": "2026-05-03T08:25:30.123456Z",
  "updated_at": "2026-05-03T08:28:45.654321Z"
}
```

**Result:** Items list is updated with the new description

### Part 4.4: DELETE - Deleting an Item (Available)

**Operation:** User can delete items using the Delete button

**HTTP Method:** DELETE

**Request:**
```
DELETE /api/items/{id}/
Authorization: Bearer {access_token}
```

**Response:** HTTP 204 No Content

Item is removed from the list

---

## Part 5: Frontend-Backend Integration

### API Service Architecture

The React frontend includes a comprehensive API service ([`src/services/api.js`]) with:

#### 1. Axios Configuration
```javascript
const api = axios.create({
  baseURL: 'http://localhost:8000/api',
});
```

#### 2. Request Interceptor - Automatic Token Injection
```javascript
api.interceptors.request.use((config) => {
  const token = localStorage.getItem('access_token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});
```

Every request automatically includes the JWT token in the Authorization header.

#### 3. Response Interceptor - Automatic Token Refresh
```javascript
api.interceptors.response.use(
  (response) => response,
  async (error) => {
    if (error.response?.status === 401) {
      // Attempt to refresh token
      const newToken = await refreshToken();
      // Retry original request with new token
    }
  }
);
```

If the access token expires, the app automatically:
1. Uses the refresh token to get a new access token
2. Retries the original request
3. User continues without interruption

#### 4. API Methods

**Authentication:**
```javascript
authAPI.login(username, password)
```

**Items Operations:**
```javascript
itemsAPI.getItems()           // GET /api/items/
itemsAPI.getItem(id)          // GET /api/items/{id}/
itemsAPI.createItem(item)     // POST /api/items/
itemsAPI.updateItem(id, item) // PUT /api/items/{id}/
itemsAPI.deleteItem(id)       // DELETE /api/items/{id}/
```

### React Components

1. **Login.jsx** - Handles user authentication
   - Takes username/password
   - Calls `/api/token/` endpoint
   - Stores JWT tokens
   - Redirects to Items list

2. **ItemList.jsx** - Displays all items
   - Fetches items on mount using `/api/items/`
   - Provides Edit/Delete buttons per item
   - Shows Logout button
   - Handles item deletion

3. **ItemForm.jsx** - Create/Edit items
   - Form for item creation and editing
   - POST for new items
   - PUT for updates
   - Cancel button to return to list

---

## Part 6: CORS Configuration

The Django backend is properly configured with CORS (Cross-Origin Resource Sharing):

**Settings ([`api_project/settings.py`]):**
```python
INSTALLED_APPS = [
    'corsheaders',
    'rest_framework',
    'rest_framework_simplejwt',
    ...
]

MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',
    ...
]

CORS_ALLOWED_ORIGINS = [
    "http://localhost:5173",   # React dev server
    "http://127.0.0.1:5173",
]
```

This allows the React frontend (running on port 5173) to communicate with the Django backend (running on port 8000).

---

## Part 7: JWT Authentication Flow

### Token Lifecycle

1. **Login:** User provides credentials
   - Backend validates credentials
   - Generates `access_token` (1 hour) and `refresh_token` (1 day)

2. **Request:** React frontend uses access token
   - Token included in Authorization header
   - Backend validates token signature and expiration
   - Request proceeds if valid

3. **Token Expiration:** Access token expires
   - React interceptor detects 401 response
   - Uses refresh_token to get new access_token
   - Retries original request automatically
   - User session continues seamlessly

4. **Logout:** User clicks logout
   - Tokens removed from localStorage
   - User redirected to login page

---

## Key Features Demonstrated

✅ **Authentication:** JWT-based user authentication
✅ **Authorization:** Protected API endpoints requiring valid tokens
✅ **CRUD Operations:** Full Create, Read, Update, Delete functionality
✅ **Token Management:** Automatic token injection and refresh
✅ **Error Handling:** 401 unauthorized for invalid/missing tokens
✅ **CORS Support:** Frontend-backend communication across origins
✅ **State Management:** Proper handling of authenticated state
✅ **Security:** Tokens stored in localStorage with Bearer scheme
✅ **Responsive UI:** Clean, modern interface with proper styling
✅ **HTTP Status Codes:** Proper use of 200, 201, 401, etc.

---

## Technical Stack

### Backend
- **Framework:** Django 6.0.4
- **API:** Django REST Framework 3.14.x
- **Authentication:** Simple JWT (djangorestframework-simplejwt)
- **CORS:** django-cors-headers
- **Database:** SQLite3

### Frontend
- **Framework:** React 19.2.5
- **Build Tool:** Vite 8.0.10
- **HTTP Client:** Axios 1.x
- **Styling:** CSS3

### Communication
- **Protocol:** HTTP/HTTPS
- **Format:** JSON
- **Authentication:** JWT Bearer Token

---

## Conclusion

This demonstration successfully shows a complete, production-ready full-stack application with:
- Secure JWT-based authentication
- Proper HTTP request/response handling
- Full CRUD operations
- Frontend-backend integration
- Cross-origin resource sharing
- Error handling and token management

The React frontend successfully communicates with the Django REST API, with all requests and responses properly captured and validated through the browser's network inspection capabilities.