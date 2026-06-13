# 🔔 Phase 2 - Reminder Notification System

## React + FastAPI + PostgreSQL

---

# 🎯 Goal

Extend the Task/Event Manager to automatically send reminders before an event occurs.

Users can:

```text
✅ Enable reminders
✅ Select reminder time
✅ Choose notification method
✅ Receive email reminder automatically
```

Future enhancement:

```text
Phase 3
✅ SMS Reminder
```

---

# 🏗️ Architecture

Current system:

```text
React
   │
   ▼
FastAPI
   │
   ▼
PostgreSQL
```

New architecture:

```text
React
   │
   ▼
FastAPI
   │
   ├── REST APIs
   │
   ├── Background Scheduler
   │
   └── Notification Service
   │
   ▼
PostgreSQL
   │
   ▼
Email Server
```

---

# 📁 Updated Project Structure

```text
backend/

├── app/
│   ├── main.py
│   ├── database.py
│   ├── models.py
│   ├── schemas.py
│   ├── crud.py
│   │
│   ├── notification.py
│   ├── scheduler.py
│   └── reminder_service.py
│
├── requirements.txt
└── .env
```

---

# 🧠 Reminder Processing Logic

Example task:

```text
Team Meeting

Time:
2026-06-20 09:00 AM

Reminder:
Enabled

Reminder Time:
30 Minutes Before

Reminder Method:
Email
```

System behavior:

```text
08:30 AM
   ↓
Scheduler Runs
   ↓
Find Matching Tasks
   ↓
Send Email
   ↓
Mark Reminder Sent
```

---

# 🗃️ Database Design Review

The table already contains:

```text
reminder_enabled
reminder_minutes_before
reminder_method
email
phone_number
reminder_sent
```

No schema changes required.

---

# 1️⃣ Install Additional Packages

Activate backend environment:

```bash
source .venv/bin/activate
```

Install scheduler package:

```bash
pip install apscheduler
```

Update requirements:

```bash
pip freeze > requirements.txt
```

---

# 2️⃣ Configure Email Settings

Update `.env`

```text
DATABASE_URL=postgresql://taskuser:taskpass@localhost:5432/taskdb

SMTP_HOST=smtp.gmail.com
SMTP_PORT=587

SMTP_USER=your_email@gmail.com
SMTP_PASSWORD=your_app_password

SMTP_FROM=your_email@gmail.com
```

---

# Gmail App Password

If using Gmail:

```text
Google Account
  ↓
Security
  ↓
2-Factor Authentication
  ↓
App Password
```

Generate password and place it in:

```text
SMTP_PASSWORD
```

Do NOT use your normal Gmail password.

---

# 3️⃣ Create Email Notification Service

Create:

```text
app/notification.py
```

---

Add:

```python
import os
import smtplib

from email.message import EmailMessage

from dotenv import load_dotenv

load_dotenv()


SMTP_HOST = os.getenv("SMTP_HOST")
SMTP_PORT = int(os.getenv("SMTP_PORT"))

SMTP_USER = os.getenv("SMTP_USER")
SMTP_PASSWORD = os.getenv("SMTP_PASSWORD")

SMTP_FROM = os.getenv("SMTP_FROM")


def send_email_reminder(task):
    message = EmailMessage()

    message["Subject"] = (
        f"Reminder: {task.title}"
    )

    message["From"] = SMTP_FROM

    message["To"] = task.email

    message.set_content(
        f"""
Upcoming Event Reminder

Title:
{task.title}

Time:
{task.event_time}

With:
{task.with_whom}

Location:
{task.location}

Description:
{task.description}
"""
    )

    with smtplib.SMTP(
        SMTP_HOST,
        SMTP_PORT
    ) as server:

        server.starttls()

        server.login(
            SMTP_USER,
            SMTP_PASSWORD
        )

        server.send_message(message)

    print(
        f"Reminder email sent for task {task.id}"
    )
```

---

# 4️⃣ Create Reminder Service

Create:

```text
app/reminder_service.py
```

Add:

```python
from datetime import datetime
from datetime import timedelta

from sqlalchemy.orm import Session

from .models import Task
from .notification import send_email_reminder
```

---

Add:

```python
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

                if (
                    task.reminder_method
                    == "email"
                ):
                    send_email_reminder(task)

                task.reminder_sent = True

                db.commit()

                print(
                    f"Reminder completed "
                    f"for task {task.id}"
                )

            except Exception as ex:

                print(
                    f"Reminder failed "
                    f"for task {task.id}"
                )

                print(ex)
```

---

# 5️⃣ Create Scheduler

Create:

```text
app/scheduler.py
```

Add:

```python
from apscheduler.schedulers.background import (
    BackgroundScheduler
)

from .database import SessionLocal
from .reminder_service import (
    process_reminders
)
```

---

Add:

```python
scheduler = (
    BackgroundScheduler()
)


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

    print(
        "Reminder scheduler started."
    )
```

---

# 6️⃣ Start Scheduler in FastAPI

Open:

```text
app/main.py
```

Add import:

```python
from .scheduler import (
    start_scheduler
)
```

---

Add startup event:

```python
@app.on_event("startup")
def startup_event():

    start_scheduler()
```

---

Now when FastAPI starts:

```text
FastAPI
   ↓
Start Scheduler
   ↓
Run Reminder Check Every Minute
```

---

# 7️⃣ Restart Backend

```bash
uvicorn app.main:app --reload
```

Expected:

```text
Reminder scheduler started.
```

---

# 8️⃣ Create Test Reminder

Use Swagger:

```text
http://localhost:8000/docs
```

Create task:

```json
{
  "title": "Reminder Test",
  "description": "Testing reminder service",
  "event_time": "2026-06-20T10:00:00",
  "with_whom": "Self",
  "location": "Home",

  "reminder_enabled": true,
  "reminder_minutes_before": 1,

  "reminder_method": "email",

  "email": "your_email@gmail.com",

  "phone_number": ""
}
```

---

# 9️⃣ Observe Scheduler

Scheduler runs every minute:

```text
Current Time
      ↓
Check Database
      ↓
Find Pending Reminders
      ↓
Send Email
      ↓
Update reminder_sent = true
```

Expected backend log:

```text
Reminder email sent for task 12

Reminder completed for task 12
```

---

# 🔟 Verify Database Update

Connect:

```bash
psql -h localhost \
-U taskuser \
-d taskdb
```

Query:

```sql
SELECT
    id,
    title,
    reminder_sent
FROM tasks;
```

Expected:

```text
id | title          | reminder_sent

12 | Reminder Test  | true
```

---

# 1️⃣1️⃣ Frontend Improvements

Add reminder status indicator.

Current:

```text
Upcoming Tasks
```

Future:

```text
Upcoming Tasks

✓ Reminder Sent
✉ Email Pending
```

Possible display:

```text
Title              Reminder

Meeting            Sent
Homework           Pending
```

---

# 1️⃣2️⃣ Handling Reminder Updates

When user edits task:

```text
Change Time
Change Reminder Time
Change Reminder Method
```

Reset:

```python
reminder_sent = False
```

Otherwise reminder will never fire again.

Update `crud.py`:

```python
db_task.reminder_sent = False
```

when updating a task.

---

# 1️⃣3️⃣ Optional Email Template

Current email:

```text
Reminder: Team Meeting

Time:
2026-06-20 09:00

With:
John Smith
```

Future improvement:

```html
Professional HTML Email
Company Branding
Clickable Links
Calendar Attachments
```

---

# 1️⃣4️⃣ Future Phase 3

Add SMS.

Architecture:

```text
Reminder Service
      │
      ├── Email
      │
      └── SMS
```

Example:

```python
if task.reminder_method == "email":
    send_email()

elif task.reminder_method == "text":
    send_sms()
```

Possible providers:

```text
Twilio
AWS SNS
Vonage
```

---

# 🎯 Final Reminder Flow

```text
User Creates Task
       │
       ▼
PostgreSQL
       │
       ▼
Scheduler Runs Every Minute
       │
       ▼
Reminder Service
       │
       ▼
Email Notification
       │
       ▼
reminder_sent = true
```

---

# 🏆 What You Learned

```text
✅ Background Scheduling
✅ Email Integration
✅ SMTP Configuration
✅ Reminder Processing
✅ Time-Based Events
✅ Backend Services
✅ Production-Style Architecture
```

At this point your application becomes more than a CRUD application—it becomes an event management system with automated notifications.

