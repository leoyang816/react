# ✅ Full-Stack Task/Event Manager Tutorial

## React + FastAPI + PostgreSQL

---

# 1. What We Are Building

A full-stack task/event manager where users can:

```text
✅ View upcoming tasks/events
✅ Add a new task
✅ Edit a task
✅ Delete a task
✅ Click a task to view details
✅ Store all data in PostgreSQL
✅ Display tasks ordered by event time
✅ Hide past/obsolete tasks
```

Technology stack:

```text
Frontend: React + Vite
Backend: FastAPI
Database: PostgreSQL
ORM: SQLAlchemy
```

---

# 2. Final Project Structure

```text
task-event-manager/

├── backend/
│   ├── app/
│   │   ├── main.py
│   │   ├── database.py
│   │   ├── models.py
│   │   ├── schemas.py
│   │   └── crud.py
│   ├── requirements.txt
│   └── .env
│
└── frontend/
    ├── package.json
    └── src/
        ├── App.jsx
        ├── App.css
        └── api.js
```

---

# 3. Create Project Folder

```bash
mkdir task-event-manager
cd task-event-manager

mkdir backend
mkdir frontend
```

---

# 4. Install PostgreSQL

On Ubuntu / WSL:

```bash
sudo apt update
sudo apt install postgresql postgresql-contrib
```

Start PostgreSQL:

```bash
sudo service postgresql start
```

Check status:

```bash
sudo service postgresql status
```

---

# 5. Create Database

Open PostgreSQL shell:

```bash
sudo -u postgres psql
```

Create database:

```sql
CREATE DATABASE taskdb;
```

Create user:

```sql
CREATE USER taskuser WITH PASSWORD 'taskpass';
```

Grant permission:

```sql
GRANT ALL PRIVILEGES ON DATABASE taskdb TO taskuser;
```

Exit:

```sql
\q
```

Test connection:

```bash
psql -h localhost -U taskuser -d taskdb
```

Password:

```text
taskpass
```

---

# 6. Backend Setup

Go to backend folder:

```bash
cd backend
```

Create virtual environment:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Create `requirements.txt`:

```txt
fastapi
uvicorn
sqlalchemy
psycopg2-binary
python-dotenv
```

Install packages:

```bash
pip install -r requirements.txt
```

Create app folder:

```bash
mkdir app
touch app/main.py app/database.py app/models.py app/schemas.py app/crud.py
touch .env
```

---

# 7. Backend Environment File

Create `backend/.env`:

```text
DATABASE_URL=postgresql://taskuser:taskpass@localhost:5432/taskdb
```

---

# 8. Database Connection

Create `backend/app/database.py`:

```python
import os

from dotenv import load_dotenv
from sqlalchemy import create_engine
from sqlalchemy.orm import declarative_base
from sqlalchemy.orm import sessionmaker


load_dotenv()

DATABASE_URL = os.getenv("DATABASE_URL")

if not DATABASE_URL:
    raise RuntimeError("DATABASE_URL is not configured")

engine = create_engine(DATABASE_URL)

SessionLocal = sessionmaker(
    autocommit=False,
    autoflush=False,
    bind=engine
)

Base = declarative_base()


def get_db():
    db = SessionLocal()

    try:
        yield db
    finally:
        db.close()
```

---

# 9. Database Model

Create `backend/app/models.py`:

```python
from sqlalchemy import Boolean
from sqlalchemy import Column
from sqlalchemy import DateTime
from sqlalchemy import Integer
from sqlalchemy import String
from sqlalchemy import Text
from sqlalchemy.sql import func

from .database import Base


class Task(Base):
    __tablename__ = "tasks"

    id = Column(Integer, primary_key=True, index=True)

    title = Column(String(255), nullable=False)
    description = Column(Text, nullable=True)

    event_time = Column(DateTime, nullable=False)

    with_whom = Column(String(255), nullable=True)
    location = Column(String(255), nullable=True)

    reminder_enabled = Column(Boolean, default=False)
    reminder_minutes_before = Column(Integer, default=30)
    reminder_method = Column(String(20), nullable=True)

    email = Column(String(255), nullable=True)
    phone_number = Column(String(50), nullable=True)

    reminder_sent = Column(Boolean, default=False)

    created_at = Column(DateTime(timezone=True), server_default=func.now())
    updated_at = Column(
        DateTime(timezone=True),
        server_default=func.now(),
        onupdate=func.now()
    )
```

---

# 10. Pydantic Schemas

Create `backend/app/schemas.py`:

```python
from datetime import datetime
from typing import Optional

from pydantic import BaseModel


class TaskBase(BaseModel):
    title: str
    description: Optional[str] = None
    event_time: datetime

    with_whom: Optional[str] = None
    location: Optional[str] = None

    reminder_enabled: bool = False
    reminder_minutes_before: int = 30
    reminder_method: Optional[str] = None

    email: Optional[str] = None
    phone_number: Optional[str] = None


class TaskCreate(TaskBase):
    pass


class TaskUpdate(TaskBase):
    pass


class TaskResponse(TaskBase):
    id: int
    reminder_sent: bool
    created_at: datetime
    updated_at: datetime

    class Config:
        from_attributes = True
```

---

# 11. CRUD Logic

Create `backend/app/crud.py`:

```python
from datetime import datetime

from sqlalchemy.orm import Session

from . import models
from . import schemas


def get_upcoming_tasks(db: Session):
    return (
        db.query(models.Task)
        .filter(models.Task.event_time >= datetime.now())
        .order_by(models.Task.event_time.asc())
        .all()
    )


def get_task(db: Session, task_id: int):
    return (
        db.query(models.Task)
        .filter(models.Task.id == task_id)
        .first()
    )


def create_task(db: Session, task: schemas.TaskCreate):
    db_task = models.Task(**task.model_dump())

    db.add(db_task)
    db.commit()
    db.refresh(db_task)

    return db_task


def update_task(db: Session, task_id: int, task: schemas.TaskUpdate):
    db_task = get_task(db, task_id)

    if not db_task:
        return None

    for key, value in task.model_dump().items():
        setattr(db_task, key, value)

    db.commit()
    db.refresh(db_task)

    return db_task


def delete_task(db: Session, task_id: int):
    db_task = get_task(db, task_id)

    if not db_task:
        return None

    db.delete(db_task)
    db.commit()

    return db_task
```

---

# 12. FastAPI Main App

Create `backend/app/main.py`:

```python
from fastapi import Depends
from fastapi import FastAPI
from fastapi import HTTPException
from fastapi.middleware.cors import CORSMiddleware
from sqlalchemy.orm import Session

from . import crud
from . import models
from . import schemas
from .database import Base
from .database import engine
from .database import get_db


Base.metadata.create_all(bind=engine)

app = FastAPI(
    title="Task Event Manager API"
)

app.add_middleware(
    CORSMiddleware,
    allow_origins=[
        "http://localhost:5173",
        "http://127.0.0.1:5173"
    ],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)


@app.get("/")
def root():
    return {
        "message": "Task Event Manager API is running"
    }


@app.get("/tasks", response_model=list[schemas.TaskResponse])
def list_tasks(db: Session = Depends(get_db)):
    return crud.get_upcoming_tasks(db)


@app.get("/tasks/{task_id}", response_model=schemas.TaskResponse)
def get_task(task_id: int, db: Session = Depends(get_db)):
    task = crud.get_task(db, task_id)

    if not task:
        raise HTTPException(status_code=404, detail="Task not found")

    return task


@app.post("/tasks", response_model=schemas.TaskResponse)
def create_task(
    task: schemas.TaskCreate,
    db: Session = Depends(get_db)
):
    return crud.create_task(db, task)


@app.put("/tasks/{task_id}", response_model=schemas.TaskResponse)
def update_task(
    task_id: int,
    task: schemas.TaskUpdate,
    db: Session = Depends(get_db)
):
    updated_task = crud.update_task(db, task_id, task)

    if not updated_task:
        raise HTTPException(status_code=404, detail="Task not found")

    return updated_task


@app.delete("/tasks/{task_id}")
def delete_task(task_id: int, db: Session = Depends(get_db)):
    deleted_task = crud.delete_task(db, task_id)

    if not deleted_task:
        raise HTTPException(status_code=404, detail="Task not found")

    return {
        "message": "Task deleted successfully"
    }
```

---

# 13. Run Backend

From `backend/`:

```bash
source .venv/bin/activate
uvicorn app.main:app --reload
```

Open:

```text
http://localhost:8000
```

API docs:

```text
http://localhost:8000/docs
```

---

# 14. Test Backend

Use FastAPI docs.

Create task with:

```json
{
  "title": "Team Meeting",
  "description": "Discuss project status and release plan.",
  "event_time": "2026-06-20T09:00:00",
  "with_whom": "John Smith",
  "location": "Conference Room",
  "reminder_enabled": true,
  "reminder_minutes_before": 30,
  "reminder_method": "email",
  "email": "user@example.com",
  "phone_number": ""
}
```

Then test:

```text
GET /tasks
GET /tasks/{task_id}
PUT /tasks/{task_id}
DELETE /tasks/{task_id}
```

---

# 15. React Frontend Setup

Go back to project root:

```bash
cd ..
```

Create React app:

```bash
npm create vite@latest frontend -- --template react
```

Go to frontend:

```bash
cd frontend
npm install
```

---

# 16. Create API Helper

Create `frontend/src/api.js`:

```javascript
const API_BASE_URL = "http://localhost:8000";

export async function fetchTasks() {
  const response = await fetch(`${API_BASE_URL}/tasks`);

  if (!response.ok) {
    throw new Error("Failed to fetch tasks");
  }

  return response.json();
}

export async function createTask(task) {
  const response = await fetch(`${API_BASE_URL}/tasks`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify(task)
  });

  if (!response.ok) {
    throw new Error("Failed to create task");
  }

  return response.json();
}

export async function updateTask(taskId, task) {
  const response = await fetch(`${API_BASE_URL}/tasks/${taskId}`, {
    method: "PUT",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify(task)
  });

  if (!response.ok) {
    throw new Error("Failed to update task");
  }

  return response.json();
}

export async function deleteTask(taskId) {
  const response = await fetch(`${API_BASE_URL}/tasks/${taskId}`, {
    method: "DELETE"
  });

  if (!response.ok) {
    throw new Error("Failed to delete task");
  }

  return response.json();
}
```

---

# 17. Replace `App.jsx`

Create `frontend/src/App.jsx`:

```jsx
import { useEffect, useState } from "react";
import "./App.css";

import {
  createTask,
  deleteTask,
  fetchTasks,
  updateTask
} from "./api";

const emptyForm = {
  title: "",
  description: "",
  event_time: "",
  with_whom: "",
  location: "",
  reminder_enabled: false,
  reminder_minutes_before: 30,
  reminder_method: "email",
  email: "",
  phone_number: ""
};

function App() {
  const [tasks, setTasks] = useState([]);
  const [form, setForm] = useState(emptyForm);
  const [selectedTask, setSelectedTask] = useState(null);
  const [editingTaskId, setEditingTaskId] = useState(null);
  const [error, setError] = useState("");

  async function loadTasks() {
    try {
      const data = await fetchTasks();
      setTasks(data);
    } catch (err) {
      setError(err.message);
    }
  }

  useEffect(() => {
    loadTasks();
  }, []);

  function handleChange(event) {
    const { name, value, type, checked } = event.target;

    setForm({
      ...form,
      [name]: type === "checkbox" ? checked : value
    });
  }

  async function handleSubmit(event) {
    event.preventDefault();
    setError("");

    try {
      const payload = {
        ...form,
        reminder_minutes_before: Number(form.reminder_minutes_before)
      };

      if (editingTaskId) {
        await updateTask(editingTaskId, payload);
      } else {
        await createTask(payload);
      }

      setForm(emptyForm);
      setEditingTaskId(null);
      await loadTasks();
    } catch (err) {
      setError(err.message);
    }
  }

  function handleEdit(task) {
    setEditingTaskId(task.id);

    setForm({
      title: task.title || "",
      description: task.description || "",
      event_time: task.event_time.slice(0, 16),
      with_whom: task.with_whom || "",
      location: task.location || "",
      reminder_enabled: task.reminder_enabled || false,
      reminder_minutes_before: task.reminder_minutes_before || 30,
      reminder_method: task.reminder_method || "email",
      email: task.email || "",
      phone_number: task.phone_number || ""
    });
  }

  async function handleDelete(taskId) {
    if (!confirm("Delete this task?")) {
      return;
    }

    await deleteTask(taskId);
    await loadTasks();

    if (selectedTask?.id === taskId) {
      setSelectedTask(null);
    }
  }

  return (
    <div className="page">
      <h1>✅ Event / Task Manager</h1>

      {error && <div className="error">{error}</div>}

      <div className="layout">
        <section className="card">
          <h2>{editingTaskId ? "Edit Task" : "Add New Task"}</h2>

          <form onSubmit={handleSubmit}>
            <label>Title</label>
            <input
              name="title"
              value={form.title}
              onChange={handleChange}
              required
            />

            <label>Event Time</label>
            <input
              type="datetime-local"
              name="event_time"
              value={form.event_time}
              onChange={handleChange}
              required
            />

            <label>With Whom</label>
            <input
              name="with_whom"
              value={form.with_whom}
              onChange={handleChange}
            />

            <label>Location</label>
            <input
              name="location"
              value={form.location}
              onChange={handleChange}
            />

            <label>Description</label>
            <textarea
              name="description"
              value={form.description}
              onChange={handleChange}
              rows="4"
            />

            <label className="checkbox-row">
              <input
                type="checkbox"
                name="reminder_enabled"
                checked={form.reminder_enabled}
                onChange={handleChange}
              />
              Reminder Enabled
            </label>

            <label>Reminder Minutes Before</label>
            <input
              type="number"
              name="reminder_minutes_before"
              value={form.reminder_minutes_before}
              onChange={handleChange}
            />

            <label>Reminder Method</label>
            <select
              name="reminder_method"
              value={form.reminder_method}
              onChange={handleChange}
            >
              <option value="email">Email</option>
              <option value="text">Text</option>
            </select>

            <label>Email</label>
            <input
              name="email"
              value={form.email}
              onChange={handleChange}
            />

            <label>Phone Number</label>
            <input
              name="phone_number"
              value={form.phone_number}
              onChange={handleChange}
            />

            <button type="submit">
              {editingTaskId ? "Update Task" : "Add Task"}
            </button>

            {editingTaskId && (
              <button
                type="button"
                className="secondary"
                onClick={() => {
                  setEditingTaskId(null);
                  setForm(emptyForm);
                }}
              >
                Cancel Edit
              </button>
            )}
          </form>
        </section>

        <section className="card">
          <h2>Upcoming Tasks</h2>

          {tasks.length === 0 && <p>No upcoming tasks.</p>}

          <table>
            <thead>
              <tr>
                <th>Time</th>
                <th>Title</th>
                <th>With Whom</th>
                <th>Actions</th>
              </tr>
            </thead>

            <tbody>
              {tasks.map((task) => (
                <tr key={task.id}>
                  <td>
                    {new Date(task.event_time).toLocaleString()}
                  </td>
                  <td
                    className="clickable"
                    onClick={() => setSelectedTask(task)}
                  >
                    {task.title}
                  </td>
                  <td>{task.with_whom}</td>
                  <td>
                    <button onClick={() => handleEdit(task)}>
                      Edit
                    </button>
                    <button
                      className="danger"
                      onClick={() => handleDelete(task.id)}
                    >
                      Delete
                    </button>
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </section>
      </div>

      {selectedTask && (
        <section className="card detail-card">
          <h2>Task Detail</h2>

          <p><strong>Title:</strong> {selectedTask.title}</p>
          <p>
            <strong>Time:</strong>{" "}
            {new Date(selectedTask.event_time).toLocaleString()}
          </p>
          <p><strong>With:</strong> {selectedTask.with_whom}</p>
          <p><strong>Location:</strong> {selectedTask.location}</p>
          <p><strong>Description:</strong> {selectedTask.description}</p>
          <p>
            <strong>Reminder:</strong>{" "}
            {selectedTask.reminder_enabled ? "Enabled" : "Disabled"}
          </p>
          <p>
            <strong>Reminder Method:</strong>{" "}
            {selectedTask.reminder_method}
          </p>
          <p>
            <strong>Reminder Time:</strong>{" "}
            {selectedTask.reminder_minutes_before} minutes before
          </p>

          <button onClick={() => setSelectedTask(null)}>
            Close Detail
          </button>
        </section>
      )}
    </div>
  );
}

export default App;
```

---

# 18. Replace `App.css`

Create `frontend/src/App.css`:

```css
body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: #f4f6f8;
}

.page {
  padding: 32px;
}

h1 {
  margin-bottom: 24px;
}

.layout {
  display: grid;
  grid-template-columns: 420px 1fr;
  gap: 24px;
}

.card {
  background: white;
  padding: 24px;
  border-radius: 10px;
  box-shadow: 0 4px 14px rgba(0, 0, 0, 0.08);
}

form {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

input,
textarea,
select {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 6px;
}

button {
  margin-right: 8px;
  margin-top: 8px;
  padding: 8px 12px;
  border: none;
  border-radius: 6px;
  background: #2563eb;
  color: white;
  cursor: pointer;
}

button.secondary {
  background: #6b7280;
}

button.danger {
  background: #dc2626;
}

table {
  width: 100%;
  border-collapse: collapse;
}

th,
td {
  padding: 10px;
  border-bottom: 1px solid #ddd;
  text-align: left;
}

.clickable {
  color: #2563eb;
  cursor: pointer;
  font-weight: bold;
}

.checkbox-row {
  display: flex;
  align-items: center;
  gap: 8px;
}

.error {
  background: #fee2e2;
  color: #991b1b;
  padding: 12px;
  border-radius: 8px;
  margin-bottom: 16px;
}

.detail-card {
  margin-top: 24px;
}
```

---

# 19. Run Frontend

From `frontend/`:

```bash
npm run dev
```

Open:

```text
http://localhost:5173
```

---

# 20. Run Full App

Use two terminals.

Backend:

```bash
cd task-event-manager/backend
source .venv/bin/activate
uvicorn app.main:app --reload
```

Frontend:

```bash
cd task-event-manager/frontend
npm run dev
```

Open:

```text
http://localhost:5173
```

---

# 21. Test Flow

Test these actions:

```text
1. Add a new task
2. Verify it appears in task list
3. Click title to view full detail
4. Edit task
5. Delete task
6. Add a task in the past
7. Verify past task does not show in list
```

---

# 22. Next Phase: Reminder Notification

After CRUD works, add:

```text
Email reminder
SMS reminder
Background scheduler
reminder_sent flag update
```

Suggested library:

```bash
pip install apscheduler
```

Possible services:

```text
Email: SMTP, Gmail SMTP, AWS SES
Text: Twilio, AWS SNS
```

---

# 23. Summary

You built:

```text
React frontend
FastAPI backend
PostgreSQL database
Task CRUD API
Event time ordering
Past event filtering
Task detail view
```

This is a clean foundation for adding reminders and notification support later.

