# 📱 Phase 3 - SMS Reminder Notification System

## React + FastAPI + PostgreSQL + Twilio SMS

---

# 🎯 Goal

Extend the Task/Event Manager so users can receive reminders by text message.

This phase adds:

```text
✅ SMS reminder support
✅ Twilio integration
✅ Text message notification service
✅ Reminder method selection: email or text
✅ Backend scheduler support for SMS reminders
✅ Safer environment variable configuration
```

---

# 🧩 Current System

You already have:

```text
React Frontend
FastAPI Backend
PostgreSQL Database
Task CRUD
Email Reminder Scheduler
```

Now we add:

```text
SMS Reminder
```

---

# 🏗️ Updated Architecture

```text
React UI
   │
   ▼
FastAPI Backend
   │
   ├── REST APIs
   ├── Reminder Scheduler
   ├── Email Notification Service
   └── SMS Notification Service
   │
   ▼
PostgreSQL
   │
   ▼
Twilio SMS API
```

Twilio’s Python helper library supports connecting to Twilio APIs from Python apps, and Twilio recommends using environment variables for credentials instead of hardcoding secrets.

---

# 📁 Updated Backend Structure

```text
backend/

├── app/
│   ├── main.py
│   ├── database.py
│   ├── models.py
│   ├── schemas.py
│   ├── crud.py
│   ├── notification.py
│   ├── reminder_service.py
│   ├── scheduler.py
│   └── sms_notification.py
│
├── requirements.txt
└── .env
```

---

# 1️⃣ Create Twilio Account

Go to:

```text
https://www.twilio.com
```

Create an account.

You will need:

```text
Account SID
Auth Token
Twilio Phone Number
```

Twilio also documents that recipient phone numbers should use **E.164 format**, such as `+19195551234`.

---

# 2️⃣ Install Twilio Python Package

Go to backend folder:

```bash
cd task-event-manager/backend
```

Activate virtual environment:

```bash
source .venv/bin/activate
```

Install Twilio package:

```bash
pip install twilio
```

Update `requirements.txt`:

```bash
pip freeze > requirements.txt
```

Your `requirements.txt` should include packages like:

```txt
fastapi
uvicorn
sqlalchemy
psycopg2-binary
python-dotenv
apscheduler
twilio
```

---

# 3️⃣ Update `.env`

Open:

```text
backend/.env
```

Add Twilio settings:

```text
DATABASE_URL=postgresql://taskuser:taskpass@localhost:5432/taskdb

SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your_email@gmail.com
SMTP_PASSWORD=your_email_app_password
SMTP_FROM=your_email@gmail.com

TWILIO_ACCOUNT_SID=your_twilio_account_sid
TWILIO_AUTH_TOKEN=your_twilio_auth_token
TWILIO_PHONE_NUMBER=+15551234567
```

Important:

```text
Do not commit .env to Git.
```

Make sure `.gitignore` contains:

```text
.env
.venv/
__pycache__/
```

---

# 4️⃣ Confirm Phone Number Format

Phone numbers should be stored in E.164 format.

Good examples:

```text
+19195551234
+12025550123
```

Bad examples:

```text
9195551234
(919) 555-1234
919-555-1234
```

For this project, enter phone numbers in the UI as:

```text
+1XXXXXXXXXX
```

---

# 5️⃣ Create SMS Notification Service

Create:

```text
backend/app/sms_notification.py
```

Add:

```python
import os

from dotenv import load_dotenv
from twilio.rest import Client


load_dotenv()


TWILIO_ACCOUNT_SID = os.getenv("TWILIO_ACCOUNT_SID")
TWILIO_AUTH_TOKEN = os.getenv("TWILIO_AUTH_TOKEN")
TWILIO_PHONE_NUMBER = os.getenv("TWILIO_PHONE_NUMBER")


def send_sms_reminder(task):
    if not TWILIO_ACCOUNT_SID:
        raise RuntimeError("TWILIO_ACCOUNT_SID is not configured")

    if not TWILIO_AUTH_TOKEN:
        raise RuntimeError("TWILIO_AUTH_TOKEN is not configured")

    if not TWILIO_PHONE_NUMBER:
        raise RuntimeError("TWILIO_PHONE_NUMBER is not configured")

    if not task.phone_number:
        raise RuntimeError("Task phone_number is missing")

    client = Client(
        TWILIO_ACCOUNT_SID,
        TWILIO_AUTH_TOKEN
    )

    body = (
        f"Reminder: {task.title}\n"
        f"Time: {task.event_time}\n"
        f"With: {task.with_whom or 'N/A'}\n"
        f"Location: {task.location or 'N/A'}"
    )

    message = client.messages.create(
        body=body,
        from_=TWILIO_PHONE_NUMBER,
        to=task.phone_number
    )

    print(
        f"SMS reminder sent for task {task.id}. "
        f"Twilio SID: {message.sid}"
    )

    return message.sid
```

---

# 6️⃣ Update Reminder Service

Open:

```text
backend/app/reminder_service.py
```

Update imports:

```python
from datetime import datetime
from datetime import timedelta

from sqlalchemy.orm import Session

from .models import Task
from .notification import send_email_reminder
from .sms_notification import send_sms_reminder
```

Replace the full file with:

```python
from datetime import datetime
from datetime import timedelta

from sqlalchemy.orm import Session

from .models import Task
from .notification import send_email_reminder
from .sms_notification import send_sms_reminder


def process_reminders(db: Session):
    now = datetime.now()

    tasks = (
        db.query(Task)
        .filter(
            Task.reminder_enabled == True,
            Task.reminder_sent == False
        )
        .all()
    )

    for task in tasks:
        reminder_time = (
            task.event_time
            - timedelta(
                minutes=task.reminder_minutes_before
            )
        )

        if now >= reminder_time:
            try:
                if task.reminder_method == "email":
                    send_email_reminder(task)

                elif task.reminder_method == "text":
                    send_sms_reminder(task)

                else:
                    print(
                        f"Unsupported reminder method "
                        f"for task {task.id}: "
                        f"{task.reminder_method}"
                    )

                    continue

                task.reminder_sent = True

                db.commit()

                print(
                    f"Reminder completed for task {task.id}"
                )

            except Exception as ex:
                db.rollback()

                print(
                    f"Reminder failed for task {task.id}"
                )

                print(ex)
```

---

# 7️⃣ Reset Reminder When Task Is Updated

When a task changes, especially event time or reminder method, reset:

```text
reminder_sent = false
```

Open:

```text
backend/app/crud.py
```

Find:

```python
def update_task(db: Session, task_id: int, task: schemas.TaskUpdate):
```

Update it like this:

```python
def update_task(db: Session, task_id: int, task: schemas.TaskUpdate):
    db_task = get_task(db, task_id)

    if not db_task:
        return None

    for key, value in task.model_dump().items():
        setattr(db_task, key, value)

    db_task.reminder_sent = False

    db.commit()
    db.refresh(db_task)

    return db_task
```

---

# 8️⃣ Confirm Scheduler Still Runs

Open:

```text
backend/app/scheduler.py
```

It should still look like this:

```python
from apscheduler.schedulers.background import BackgroundScheduler

from .database import SessionLocal
from .reminder_service import process_reminders


scheduler = BackgroundScheduler()


def run_reminders():
    db = SessionLocal()

    try:
        process_reminders(db)

    finally:
        db.close()


def start_scheduler():
    scheduler.add_job(
        run_reminders,
        trigger="interval",
        minutes=1
    )

    scheduler.start()

    print("Reminder scheduler started.")
```

---

# 9️⃣ Confirm FastAPI Starts Scheduler

Open:

```text
backend/app/main.py
```

Make sure it includes:

```python
from .scheduler import start_scheduler
```

and:

```python
@app.on_event("startup")
def startup_event():
    start_scheduler()
```

---

# 🔟 Restart Backend

Stop backend:

```text
Ctrl + C
```

Start again:

```bash
cd task-event-manager/backend
source .venv/bin/activate
uvicorn app.main:app --reload
```

Expected output:

```text
Reminder scheduler started.
```

---

# 1️⃣1️⃣ Test SMS Directly First

Before testing the full scheduler, create a simple test file.

Create:

```text
backend/test_sms.py
```

Add:

```python
from types import SimpleNamespace

from app.sms_notification import send_sms_reminder


task = SimpleNamespace(
    id=999,
    title="Test SMS Reminder",
    event_time="2026-06-20 10:00 AM",
    with_whom="Self",
    location="Home",
    phone_number="+19195551234"
)


send_sms_reminder(task)
```

Update the phone number to your real verified phone number.

Run:

```bash
python test_sms.py
```

Expected:

```text
SMS reminder sent for task 999. Twilio SID: ...
```

If this fails, fix SMS setup before testing the scheduler.

---

# 1️⃣2️⃣ Create SMS Reminder Task from UI

Open React UI:

```text
http://localhost:5173
```

Create a task:

```text
Title:
SMS Reminder Test

Description:
Testing text reminder

Event Time:
5 minutes from now

With Whom:
Self

Location:
Home

Reminder Enabled:
Checked

Reminder Minutes Before:
1

Reminder Method:
text

Phone Number:
+19195551234
```

Save task.

---

# 1️⃣3️⃣ Wait for Scheduler

The scheduler runs every minute.

Expected backend logs:

```text
SMS reminder sent for task 12. Twilio SID: SMxxxxxxxx

Reminder completed for task 12
```

The database should update:

```text
reminder_sent = true
```

---

# 1️⃣4️⃣ Verify Database

Connect:

```bash
psql -h localhost -U taskuser -d taskdb
```

Query:

```sql
SELECT
    id,
    title,
    reminder_method,
    phone_number,
    reminder_sent
FROM tasks
ORDER BY event_time ASC;
```

Expected:

```text
id | title             | reminder_method | phone_number  | reminder_sent
12 | SMS Reminder Test | text            | +19195551234  | true
```

---

# 1️⃣5️⃣ Optional: Improve Frontend Reminder Method Label

In `frontend/src/App.jsx`, the select already supports:

```jsx
<select
  name="reminder_method"
  value={form.reminder_method}
  onChange={handleChange}
>
  <option value="email">Email</option>
  <option value="text">Text</option>
</select>
```

You may improve labels:

```jsx
<option value="email">Email Reminder</option>
<option value="text">Text Message Reminder</option>
```

---

# 1️⃣6️⃣ Optional: Validate Phone Number in React

Add simple validation before submit:

```jsx
if (
  payload.reminder_enabled &&
  payload.reminder_method === "text" &&
  !payload.phone_number.startsWith("+")
) {
  setError("Phone number must use E.164 format, for example +19195551234");
  return;
}
```

Put this inside `handleSubmit` before calling `createTask` or `updateTask`.

---

# 1️⃣7️⃣ Optional: Validate Phone Number in FastAPI

Add simple backend validation in `schemas.py`.

```python
from pydantic import field_validator
```

Inside `TaskBase`:

```python
@field_validator("phone_number")
@classmethod
def validate_phone_number(cls, value):
    if value and not value.startswith("+"):
        raise ValueError(
            "Phone number must use E.164 format, for example +19195551234"
        )

    return value
```

---

# 1️⃣8️⃣ Troubleshooting

## SMS Not Sent

Check:

```text
TWILIO_ACCOUNT_SID
TWILIO_AUTH_TOKEN
TWILIO_PHONE_NUMBER
Recipient phone number format
Twilio account status
```

---

## Trial Account Limitation

If using a Twilio trial account, you may need to verify recipient phone numbers before sending SMS.

---

## Invalid Phone Number

Use E.164 format:

```text
+19195551234
```

---

## Scheduler Does Not Fire

Check backend log:

```text
Reminder scheduler started.
```

Check task values:

```sql
SELECT
    id,
    title,
    event_time,
    reminder_enabled,
    reminder_minutes_before,
    reminder_method,
    reminder_sent
FROM tasks;
```

---

## Reminder Already Sent

If:

```text
reminder_sent = true
```

the scheduler will not send again.

For testing, reset:

```sql
UPDATE tasks
SET reminder_sent = false
WHERE id = 12;
```

---

## Reminder Time Already Passed

If current time is already greater than:

```text
event_time - reminder_minutes_before
```

the reminder may fire on the next scheduler run.

---

# 1️⃣9️⃣ Production Notes

For a real production system, improve this design by adding:

```text
Dedicated background worker
Retry logic
Notification audit table
Failure status
SMS provider abstraction
User-specific phone validation
Timezone handling
```

Recommended future table:

```sql
CREATE TABLE notification_logs (
    id SERIAL PRIMARY KEY,
    task_id INTEGER REFERENCES tasks(id),
    notification_method VARCHAR(20),
    recipient VARCHAR(255),
    status VARCHAR(50),
    provider_message_id VARCHAR(255),
    error_message TEXT,
    sent_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

# 2️⃣0️⃣ Final Flow

```text
User Creates Task
       │
       ▼
PostgreSQL stores task
       │
       ▼
Scheduler runs every minute
       │
       ▼
Reminder Service checks due reminders
       │
       ├── Email reminder
       │
       └── SMS reminder
              │
              ▼
          Twilio API
              │
              ▼
          User receives text
```

---

# 🏆 What You Learned

```text
✅ SMS API integration
✅ Twilio Python SDK
✅ Environment-based secrets
✅ Scheduler-based reminders
✅ Reminder method routing
✅ Email + SMS notification design
✅ Backend service separation
```

At this point your application supports:

```text
✅ CRUD task management
✅ PostgreSQL persistence
✅ Email reminders
✅ SMS reminders
✅ React frontend
✅ FastAPI backend
```

