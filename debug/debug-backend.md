# Backend Code Debugging Guideline Using VS Code

## 1. Goal

This tutorial shows how to debug backend code step by step using:

```text
Node.js
Express.js
VS Code Debugger
Postman or browser
Breakpoints
Console logs
```

You will create a small backend API, set breakpoints, send requests, and inspect variables while the backend code is paused.

---

# 2. Create a Backend Project

Create a new folder:

```bash
mkdir backend-debug-demo
cd backend-debug-demo
```

Initialize a Node.js project:

```bash
npm init -y
```

Install Express:

```bash
npm install express
```

Install Nodemon for development:

```bash
npm install --save-dev nodemon
```

---

# 3. Create Backend Server File

Create a file:

```text
server.js
```

Add this code:

```js
const express = require("express");

const app = express();
const port = 3001;

app.use(express.json());

const users = [
  { id: 1, name: "Alice", role: "Admin" },
  { id: 2, name: "Bob", role: "Developer" },
  { id: 3, name: "Charlie", role: "Tester" },
];

app.get("/", (req, res) => {
  res.send("Backend Debug Demo API is running");
});

app.get("/api/users", (req, res) => {
  console.log("GET /api/users called");

  res.json(users);
});

app.get("/api/users/:id", (req, res) => {
  const id = Number(req.params.id);

  console.log("Requested user id:", id);

  const user = users.find((u) => u.id === id);

  if (!user) {
    return res.status(404).json({
      message: "User not found",
    });
  }

  res.json(user);
});

app.post("/api/users", (req, res) => {
  const newUser = req.body;

  console.log("New user request body:", newUser);

  const userToAdd = {
    id: users.length + 1,
    name: newUser.name,
    role: newUser.role,
  };

  users.push(userToAdd);

  res.status(201).json(userToAdd);
});

app.listen(port, () => {
  console.log(`Backend server running at http://localhost:${port}`);
});
```

---

# 4. Update `package.json`

Open `package.json` and update the `scripts` section:

```json
{
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js",
    "debug": "node --inspect server.js"
  }
}
```

Full example:

```json
{
  "name": "backend-debug-demo",
  "version": "1.0.0",
  "description": "Backend debugging demo",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js",
    "debug": "node --inspect server.js"
  },
  "dependencies": {
    "express": "^4.18.0"
  },
  "devDependencies": {
    "nodemon": "^3.0.0"
  }
}
```

---

# 5. Run the Backend Normally

Start the backend:

```bash
npm run dev
```

You should see:

```text
Backend server running at http://localhost:3001
```

Open browser:

```text
http://localhost:3001
```

You should see:

```text
Backend Debug Demo API is running
```

Test users API:

```text
http://localhost:3001/api/users
```

Expected response:

```json
[
  {
    "id": 1,
    "name": "Alice",
    "role": "Admin"
  },
  {
    "id": 2,
    "name": "Bob",
    "role": "Developer"
  },
  {
    "id": 3,
    "name": "Charlie",
    "role": "Tester"
  }
]
```

---

# 6. Add VS Code Debug Configuration

Open the project in VS Code:

```bash
code .
```

Create this folder:

```text
.vscode
```

Inside `.vscode`, create:

```text
launch.json
```

Add this content:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Debug Backend Server",
      "program": "${workspaceFolder}/server.js",
      "skipFiles": ["<node_internals>/**"]
    }
  ]
}
```

---

# 7. Start Backend Debugging

In VS Code:

```text
Run and Debug
```

Select:

```text
Debug Backend Server
```

Click the green play button.

You should see this in the VS Code Debug Console:

```text
Backend server running at http://localhost:3001
```

Now your backend is running through the VS Code debugger.

---

# 8. Set Your First Breakpoint

Open:

```text
server.js
```

Find this route:

```js
app.get("/api/users/:id", (req, res) => {
  const id = Number(req.params.id);

  console.log("Requested user id:", id);

  const user = users.find((u) => u.id === id);

  if (!user) {
    return res.status(404).json({
      message: "User not found",
    });
  }

  res.json(user);
});
```

Click on the left side of this line:

```js
const id = Number(req.params.id);
```

A red dot should appear. That is your breakpoint.

---

# 9. Trigger the Breakpoint

Open browser:

```text
http://localhost:3001/api/users/1
```

The browser will keep loading.

That is normal.

Your backend code has paused in VS Code.

In VS Code, you should see the breakpoint line highlighted:

```js
const id = Number(req.params.id);
```

---

# 10. Inspect Variables

In the VS Code left panel, check:

```text
Variables
```

You can inspect:

```text
req
req.params
req.params.id
res
users
```

You should see:

```text
req.params.id = "1"
```

Notice that route parameters are strings by default.

That is why we convert it:

```js
const id = Number(req.params.id);
```

---

# 11. Step Through Code

Use these controls in VS Code:

```text
Continue    F5
Step Over   F10
Step Into   F11
Step Out    Shift + F11
Stop        Shift + F5
```

## Step Over

Press:

```text
F10
```

This executes:

```js
const id = Number(req.params.id);
```

Now inspect:

```text
id = 1
```

Press `F10` again.

This executes:

```js
console.log("Requested user id:", id);
```

Press `F10` again.

This executes:

```js
const user = users.find((u) => u.id === id);
```

Now inspect:

```text
user = { id: 1, name: "Alice", role: "Admin" }
```

Press `F5` to continue.

The browser receives the response.

---

# 12. Debug a Not Found Case

Open browser:

```text
http://localhost:3001/api/users/99
```

The breakpoint is hit again.

Step through the code.

You should see:

```text
id = 99
user = undefined
```

Then this block runs:

```js
if (!user) {
  return res.status(404).json({
    message: "User not found",
  });
}
```

Browser response:

```json
{
  "message": "User not found"
}
```

This shows how debugging helps you understand why the API returned `404`.

---

# 13. Debug a POST Request

For POST requests, use Postman, Insomnia, curl, or VS Code REST Client.

Example using `curl`:

```bash
curl -X POST http://localhost:3001/api/users \
  -H "Content-Type: application/json" \
  -d '{"name":"David","role":"DevOps Engineer"}'
```

Set a breakpoint here:

```js
const newUser = req.body;
```

Full route:

```js
app.post("/api/users", (req, res) => {
  const newUser = req.body;

  console.log("New user request body:", newUser);

  const userToAdd = {
    id: users.length + 1,
    name: newUser.name,
    role: newUser.role,
  };

  users.push(userToAdd);

  res.status(201).json(userToAdd);
});
```

When you send the POST request, VS Code pauses.

Inspect:

```text
req.body
newUser
users
userToAdd
```

Expected:

```json
{
  "name": "David",
  "role": "DevOps Engineer"
}
```

---

# 14. Use the `debugger` Statement

Instead of clicking a breakpoint, you can add:

```js
debugger;
```

Example:

```js
app.get("/api/users/:id", (req, res) => {
  debugger;

  const id = Number(req.params.id);

  const user = users.find((u) => u.id === id);

  if (!user) {
    return res.status(404).json({
      message: "User not found",
    });
  }

  res.json(user);
});
```

When this route is called, VS Code pauses automatically.

---

# 15. Debug with Console Logs

Console logs are still useful.

Example:

```js
app.get("/api/users/:id", (req, res) => {
  const id = Number(req.params.id);

  console.log("Request params:", req.params);
  console.log("Converted id:", id);
  console.log("All users:", users);

  const user = users.find((u) => u.id === id);

  console.log("Matched user:", user);

  if (!user) {
    return res.status(404).json({
      message: "User not found",
    });
  }

  res.json(user);
});
```

Useful things to log:

```text
req.params
req.query
req.body
req.headers
response status
database result
error object
```

---

# 16. Debug Query Parameters

Add this route:

```js
app.get("/api/search", (req, res) => {
  const role = req.query.role;

  console.log("Search role:", role);

  const results = users.filter((user) => user.role === role);

  res.json(results);
});
```

Test in browser:

```text
http://localhost:3001/api/search?role=Developer
```

Set breakpoint here:

```js
const role = req.query.role;
```

Inspect:

```text
req.query
role
results
```

Expected:

```json
[
  {
    "id": 2,
    "name": "Bob",
    "role": "Developer"
  }
]
```

---

# 17. Debug an Error

Add this route:

```js
app.get("/api/error-demo", (req, res) => {
  const user = undefined;

  console.log(user.name);

  res.json({
    message: "This will not run",
  });
});
```

Open:

```text
http://localhost:3001/api/error-demo
```

You should see an error like:

```text
TypeError: Cannot read properties of undefined
```

Fix it:

```js
app.get("/api/error-demo", (req, res) => {
  const user = undefined;

  if (!user) {
    return res.status(400).json({
      message: "User object is missing",
    });
  }

  res.json(user);
});
```

---

# 18. Debug Async Code

Add this route:

```js
function fakeDatabaseCall(id) {
  return new Promise((resolve) => {
    setTimeout(() => {
      const user = users.find((u) => u.id === id);
      resolve(user);
    }, 1000);
  });
}

app.get("/api/async-users/:id", async (req, res) => {
  const id = Number(req.params.id);

  const user = await fakeDatabaseCall(id);

  if (!user) {
    return res.status(404).json({
      message: "User not found",
    });
  }

  res.json(user);
});
```

Set breakpoint here:

```js
const user = await fakeDatabaseCall(id);
```

Test:

```text
http://localhost:3001/api/async-users/1
```

When stepping through async code:

```text
F10 = run the await line and pause after it completes
F11 = step into the function if possible
F5 = continue execution
```

---

# 19. Debug Backend Called by React Frontend

If React calls this backend:

```js
fetch("http://localhost:3001/api/users/1")
```

Then debugging flow is:

```text
React button click
        |
        v
Frontend sends HTTP request
        |
        v
Express backend route receives request
        |
        v
VS Code backend breakpoint is hit
        |
        v
Backend sends response
        |
        v
React receives data and updates UI
```

Important:

```text
Frontend breakpoint pauses browser code.
Backend breakpoint pauses server code.
They are separate debugging sessions.
```

---

# 20. Recommended Backend Debugging Workflow

Use this order:

```text
1. Reproduce the issue
2. Identify the API route involved
3. Add a breakpoint at the start of the route
4. Send the request again
5. Inspect req.params, req.query, req.body
6. Step through business logic
7. Inspect database/API results
8. Check response status and response body
9. Fix the code
10. Retest the same request
```

---

# 21. Common Backend Debugging Checklist

Check request path:

```text
Is the URL correct?
Is the HTTP method correct? GET, POST, PUT, DELETE?
```

Check request input:

```text
Are path parameters correct?
Are query parameters correct?
Is request body valid JSON?
Is Content-Type set to application/json?
```

Check authentication:

```text
Is token missing?
Is token expired?
Is user authorized?
```

Check backend logic:

```text
Is data type correct?
Is string being compared with number?
Is null or undefined handled?
```

Check database:

```text
Is database connected?
Did the query return data?
Is the table or collection correct?
```

Check response:

```text
Is status code correct?
Is response body correct?
Is error message helpful?
```

---

# 22. Summary

For backend debugging, use:

```text
console.log              quick inspection
VS Code breakpoints      step-by-step debugging
debugger statement       manual pause in code
Postman/curl/browser     trigger API requests
Network tab              confirm frontend request
Error stack trace        find crash location
```

Best learning order:

```text
1. console.log
2. Add breakpoint in API route
3. Send request from browser or curl
4. Inspect req.params, req.query, req.body
5. Step through logic
6. Inspect response
7. Fix and retest
```

Remember:

```text
React frontend code runs in the browser.
Backend Express code runs in Node.js.
VS Code can debug both, but they are separate processes.
```

