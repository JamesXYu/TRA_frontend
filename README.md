# Simple Full-Stack Framework

A minimal full-stack framework demonstrating the architecture of modern web applications with a **C++ backend** and **HTML/JavaScript frontend** communicating via REST API with JSON.

## 📋 Table of Contents
- [Architecture Overview](#architecture-overview)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Setup & Installation](#setup--installation)
- [Running the Application](#running-the-application)
- [How It Works](#how-it-works)
- [API Endpoints](#api-endpoints)
- [Understanding the Code](#understanding-the-code)

---

## 🏗️ Architecture Overview

```
┌─────────────────────┐         HTTP/JSON          ┌─────────────────────┐
│   Frontend (Web)    │  ◄────────────────────►   │  Backend (C++)      │
│                     │                             │                     │
│  - HTML             │   GET /api/hello           │  - HTTP Server      │
│  - CSS              │   GET /api/data            │  - REST API         │
│  - JavaScript       │   POST /api/add            │  - Data Storage     │
│                     │                             │                     │
│  (Browser)          │         Port 8080          │  (Your Laptop)      │
└─────────────────────┘                             └─────────────────────┘
```

### Key Concepts:

1. **Frontend (Client-Side)**
   - Runs in your web browser
   - User interface built with HTML/CSS
   - JavaScript handles user interactions and API calls
   - Uses `fetch()` API to communicate with backend

2. **Backend (Server-Side)**
   - C++ server running on your laptop
   - Listens on port 8080 for HTTP requests
   - Processes requests and returns JSON responses
   - Stores data in memory

3. **Communication**
   - REST API: Standard way for frontend and backend to communicate
   - JSON: Lightweight data format that both sides understand
   - HTTP: Protocol used for sending requests/responses

---

## 📁 Project Structure

```
framework test/
│
├── backend/
│   ├── server.cpp          # C++ HTTP server with API endpoints
│   └── CMakeLists.txt      # Build configuration file
│
├── frontend/
│   └── index.html          # Web interface (HTML + CSS + JavaScript)
│
└── README.md               # This file
```

---

## ✅ Prerequisites

Before you start, make sure you have:

1. **C++ Compiler** (you already have this on macOS)
   - Xcode Command Line Tools
   - Check: `clang++ --version`

2. **CMake** (build system)
   - Install: `brew install cmake`
   - Check: `cmake --version`

3. **Web Browser** (any modern browser)
   - Chrome, Firefox, Safari, etc.

---

## 🚀 Setup & Installation

### Step 1: Install CMake (if not already installed)

```bash
# Install Homebrew if you don't have it
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install CMake
brew install cmake
```

### Step 2: Build the C++ Backend

```bash
# Navigate to the backend directory
cd "/Users/jamesyu/Documents/Explorer/framework test/backend"

# Create a build directory
mkdir build
cd build

# Generate build files
cmake ..

# Compile the server
make

# The executable 'server' is now ready!
```

---

## 🎮 Running the Application

### Step 1: Start the Backend Server

```bash
# From the backend/build directory
./server
```

You should see:
```
Server started on http://localhost:8080
Available endpoints:
  GET  /api/hello - Get a hello message
  GET  /api/data  - Get all stored data
  POST /api/add   - Add new data item

Press Ctrl+C to stop the server
```

**Keep this terminal window open!** The server needs to keep running.

### Step 2: Open the Frontend

Open a **new terminal window** and run:

```bash
# Navigate to the frontend directory
cd "/Users/jamesyu/Documents/Explorer/framework test/frontend"

# Open the HTML file in your default browser
open index.html
```

Alternatively, you can:
- Double-click `index.html` in Finder
- Drag `index.html` onto your browser icon

### Step 3: Test the Application

In your browser, you should see a purple-gradient page with three sections:

1. **Test API Connection** - Click "Get Hello Message" to test the connection
2. **Add Data** - Enter a name and value, then click "Add Item"
3. **View All Data** - Click "Refresh Data" to see all stored items

---

## 🔍 How It Works

### Request Flow Example

Let's trace what happens when you click "Get Hello Message":

```
1. User clicks button
   │
   ├─► JavaScript: testConnection() function executes
   │
   ├─► fetch('http://localhost:8080/api/hello')
   │   - Browser sends HTTP GET request
   │
   ├─► Network: Request travels to backend
   │
   ├─► C++ Server: Receives request
   │   - Parses HTTP headers
   │   - Identifies path: /api/hello
   │   - Generates JSON response
   │   - Sends HTTP response
   │
   ├─► Network: Response travels back
   │
   ├─► JavaScript: Receives response
   │   - Parses JSON
   │   - Updates DOM (displays result)
   │
   └─► User sees result on screen
```

### Data Flow (Adding an Item)

```
Frontend                          Backend
   │                                 │
   │  POST /api/add                  │
   │  { "name": "apples",            │
   │    "value": 5 }                 │
   ├────────────────────────────────►│
   │                                 │ Stores in memory:
   │                                 │ data_store["apples"] = 5
   │                                 │
   │  { "success": true }            │
   │◄────────────────────────────────┤
   │                                 │
   │  GET /api/data                  │
   ├────────────────────────────────►│
   │                                 │ Retrieves all data
   │  { "items": [                   │
   │    {"name": "apples",           │
   │     "value": 5}                 │
   │  ]}                             │
   │◄────────────────────────────────┤
   │                                 │
```

---

## 🌐 API Endpoints

### 1. GET `/api/hello`

**Description:** Simple endpoint to test connectivity

**Request:**
```http
GET http://localhost:8080/api/hello
```

**Response:**
```json
{
  "message": "Hello from C++ Backend!",
  "timestamp": 1728403200
}
```

---

### 2. GET `/api/data`

**Description:** Retrieve all stored data

**Request:**
```http
GET http://localhost:8080/api/data
```

**Response:**
```json
{
  "items": [
    {"name": "apples", "value": 5},
    {"name": "oranges", "value": 3}
  ]
}
```

---

### 3. POST `/api/add`

**Description:** Add a new data item

**Request:**
```http
POST http://localhost:8080/api/add
Content-Type: application/json

{
  "name": "apples",
  "value": 5
}
```

**Response:**
```json
{
  "success": true,
  "message": "Item added successfully"
}
```

---

## 📚 Understanding the Code

### Backend (`server.cpp`)

The C++ server is built from scratch using POSIX sockets. Here's what each part does:

#### 1. **Socket Creation**
```cpp
server_fd = socket(AF_INET, SOCK_STREAM, 0);
```
Creates a TCP socket that can accept incoming connections.

#### 2. **Binding to Port**
```cpp
bind(server_fd, (struct sockaddr*)&address, sizeof(address));
```
Tells the OS: "I want to listen on port 8080".

#### 3. **Request Handling**
```cpp
std::string handle_request(const std::string& request)
```
Parses incoming HTTP requests and routes them to the correct handler.

#### 4. **CORS Headers**
```cpp
"Access-Control-Allow-Origin: *\r\n"
```
Allows the browser to make requests from a different origin (necessary for local development).

#### 5. **Data Storage**
```cpp
std::map<std::string, int> data_store;
```
In-memory storage using C++ STL map. Data is lost when server stops.

---

### Frontend (`index.html`)

The frontend is a single HTML file containing structure, styles, and logic.

#### 1. **HTML Structure**
Defines the layout: buttons, input fields, display areas.

#### 2. **CSS Styling**
Makes it look modern and professional with gradients, animations, and responsive design.

#### 3. **JavaScript Functions**

**testConnection()** - Tests API availability
```javascript
const response = await fetch(`${API_URL}/api/hello`);
const data = await response.json();
```

**addData()** - Sends POST request with JSON body
```javascript
fetch(`${API_URL}/api/add`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ name, value })
});
```

**getData()** - Retrieves and displays all data
```javascript
const response = await fetch(`${API_URL}/api/data`);
const data = await response.json();
// Update DOM with data
```

---

## 🎓 Learning Exercises

Now that you understand the basics, try these challenges:

### Easy
1. Add a new endpoint `/api/multiply?a=5&b=3` that multiplies two numbers
2. Change the frontend color scheme
3. Add a button to clear all data

### Medium
4. Add a DELETE endpoint to remove specific items
5. Implement persistent storage (save to file)
6. Add input validation on both frontend and backend

### Hard
7. Add authentication (simple username/password)
8. Implement WebSocket for real-time updates
9. Add a database (SQLite) instead of in-memory storage

---

## 🛠️ Troubleshooting

### "Server failed to bind to port 8080"
- Another application is using port 8080
- Solution: Change port in both `server.cpp` and `index.html`

### "Backend Not Running" in browser
- The C++ server isn't running
- Solution: Start the server with `./server`

### "Connection refused"
- Firewall blocking the connection
- Wrong port number
- Solution: Check firewall settings and verify port in code

### Changes not reflecting
- Browser cache
- Solution: Hard refresh (Cmd+Shift+R on Mac)

---

## 🎉 Success!

You now have a working full-stack framework! You've learned:

✅ How HTTP servers work  
✅ REST API architecture  
✅ Client-server communication  
✅ JSON data exchange  
✅ Building backends in C++  
✅ Creating interactive web frontends  

This is the foundation of how most web applications work, from simple websites to complex platforms like Facebook, Twitter, or your favorite apps!

---

## 📝 Next Steps

- Explore other backend frameworks (Node.js, Python Flask, Go)
- Learn about databases (PostgreSQL, MongoDB)
- Study frontend frameworks (React, Vue, Svelte)
- Understand security concepts (HTTPS, CORS, authentication)
- Deploy to cloud platforms (AWS, Heroku, DigitalOcean)

Happy coding! 🚀
