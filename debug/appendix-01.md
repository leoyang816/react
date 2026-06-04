# 🚀 Express & Nodemon Beginner's Guide

## 🎯 Learning Goal

After completing this guide, you will understand:

✅ What Node.js is

✅ What Express is

✅ What Nodemon is

✅ Why developers use them

✅ How Express and Nodemon work together

✅ How they fit into a React + Backend application

---

# 🌟 Big Picture

When building a full-stack application:

```text
┌─────────────────────┐
│     React UI        │
│   (Frontend)        │
└──────────┬──────────┘
           │ HTTP Request
           ▼
┌─────────────────────┐
│      Express        │
│     (Backend)       │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│     Database        │
│ PostgreSQL/MySQL    │
└─────────────────────┘
```

Example:

```text
User clicks button
        │
        ▼
React sends request
        │
        ▼
Express receives request
        │
        ▼
Database query
        │
        ▼
Express sends response
        │
        ▼
React updates screen
```

---

# 🟢 What is Node.js?

Node.js allows JavaScript to run outside a browser.

Normally:

```text
JavaScript
    │
    ▼
Browser Only
```

With Node.js:

```text
JavaScript
    │
    ├── Browser
    │
    └── Server
```

Example:

```js
console.log("Hello Node.js");
```

Run:

```bash
node app.js
```

Output:

```text
Hello Node.js
```

---

# 🔵 What is Express?

Express is a web framework built on top of Node.js.

Think of it like this:

```text
Node.js = Engine

Express = Car
```

Node.js provides the engine.

Express provides:

* Routing
* Request handling
* Response handling
* Middleware
* REST API support

---

# 🔍 Without Express

Using pure Node.js:

```js
const http = require("http");

const server = http.createServer((req, res) => {

  if (req.url === "/hello") {

    res.writeHead(200);

    res.end("Hello World");
  }

});

server.listen(3000);
```

Works...

But gets messy quickly.

---

# 🔍 With Express

Same functionality:

```js
const express = require("express");

const app = express();

app.get("/hello", (req, res) => {
  res.send("Hello World");
});

app.listen(3000);
```

Much cleaner.

Much easier.

Much more maintainable.

---

# 🎯 Express Routing

A route tells Express:

```text
When URL X arrives
Run function Y
```

Example:

```js
app.get("/users", (req, res) => {
  res.send("List of users");
});
```

Request:

```text
GET /users
```

Response:

```text
List of users
```

---

# 🎯 Route Parameters

Example:

```js
app.get("/users/:id", (req, res) => {

  const id = req.params.id;

  res.send(`User ID = ${id}`);
});
```

Request:

```text
GET /users/5
```

Result:

```text
User ID = 5
```

---

# 🎯 Query Parameters

Example:

```js
app.get("/search", (req, res) => {

  const keyword = req.query.keyword;

  res.send(keyword);
});
```

Request:

```text
/search?keyword=react
```

Result:

```text
react
```

---

# 🎯 JSON Request Body

Without Express:

```text
😫 Difficult
```

With Express:

```js
app.use(express.json());
```

Then:

```js
app.post("/users", (req, res) => {

  console.log(req.body);

  res.send("User created");
});
```

Request:

```json
{
  "name": "Tao"
}
```

Result:

```js
req.body.name
```

contains:

```text
Tao
```

---

# 🟣 What is Middleware?

Middleware runs before your route handler.

```text
Request
   │
   ▼
Middleware
   │
   ▼
Route Handler
   │
   ▼
Response
```

Example:

```js
app.use(express.json());
```

Purpose:

```text
Convert JSON
into req.body
```

---

# 🟡 Why Express is Popular

Express makes API development easy.

Example:

```js
app.get("/users");
app.post("/users");
app.put("/users/:id");
app.delete("/users/:id");
```

These four routes are enough to build a basic CRUD application.

---

# 🔥 What is Nodemon?

Nodemon is a development tool.

Its job:

```text
Automatically restart Node.js
when code changes
```

---

# ❌ Without Nodemon

Start server:

```bash
node server.js
```

Output:

```text
Server started
```

Modify code:

```js
console.log("Version 2");
```

Nothing happens.

Must restart manually:

```bash
Ctrl + C

node server.js
```

Every.

Single.

Time.

😫

---

# ✅ With Nodemon

Start:

```bash
nodemon server.js
```

Output:

```text
[nodemon] starting node server.js
```

Modify file.

Save.

Nodemon automatically detects:

```text
File changed
```

and restarts:

```text
[nodemon] restarting due to changes...
```

No manual restart required.

🎉

---

# 🚀 Installing Express

Create project:

```bash
mkdir backend-demo

cd backend-demo

npm init -y
```

Install Express:

```bash
npm install express
```

---

# 🚀 Installing Nodemon

Install as development dependency:

```bash
npm install --save-dev nodemon
```

---

# 📦 Package.json Example

```json
{
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js"
  }
}
```

---

# 🚀 Running the Application

Production style:

```bash
npm start
```

Equivalent to:

```bash
node server.js
```

---

Development style:

```bash
npm run dev
```

Equivalent to:

```bash
nodemon server.js
```

---

# 📂 Typical Project Structure

```text
backend-demo/

├── node_modules/
├── package.json
├── package-lock.json
├── server.js
└── .vscode/
```

---

# 🧪 Complete Example

## server.js

```js
const express = require("express");

const app = express();

const port = 3001;

app.use(express.json());

app.get("/", (req, res) => {

  res.send("Backend Running");

});

app.get("/users", (req, res) => {

  res.json([
    {
      id: 1,
      name: "Alice"
    },
    {
      id: 2,
      name: "Bob"
    }
  ]);

});

app.listen(port, () => {

  console.log(
    `Server running on port ${port}`
  );

});
```

Run:

```bash
npm run dev
```

Open:

```text
http://localhost:3001
```

Result:

```text
Backend Running
```

Open:

```text
http://localhost:3001/users
```

Result:

```json
[
  {
    "id": 1,
    "name": "Alice"
  },
  {
    "id": 2,
    "name": "Bob"
  }
]
```

---

# 🔗 How React and Express Work Together

React:

```text
Port 5173
```

Express:

```text
Port 3001
```

Flow:

```text
┌───────────────┐
│ React UI      │
└───────┬───────┘
        │
        ▼
fetch("/api/users")
        │
        ▼
┌───────────────┐
│ Express API   │
└───────┬───────┘
        │
        ▼
┌───────────────┐
│ Database      │
└───────────────┘
```

Example React code:

```js
const response = await fetch(
  "http://localhost:3001/users"
);

const users = await response.json();
```

---

# 💡 Express vs Nodemon

| Feature                 | Express    | Nodemon             |
| ----------------------- | ---------- | ------------------- |
| Type                    | Framework  | Development Tool    |
| Purpose                 | Build APIs | Auto restart server |
| Required in Production  | Yes        | No                  |
| Required in Development | Yes        | Highly Recommended  |
| Handles HTTP Requests   | ✅          | ❌                   |
| Auto Restart            | ❌          | ✅                   |

---

# 🎯 Quick Summary

## Express

```text
🟢 Build APIs
🟢 Handle requests
🟢 Handle responses
🟢 Manage routes
🟢 Process JSON
```

## Nodemon

```text
🟣 Watch files
🟣 Detect changes
🟣 Restart server automatically
🟣 Improve developer productivity
```

## Most Common Beginner Stack

```text
Frontend
─────────
React
Vite

Backend
─────────
Node.js
Express
Nodemon

Database
─────────
PostgreSQL
```

This stack is one of the most common ways to learn modern full-stack web development and is an excellent next step after your React training project.

