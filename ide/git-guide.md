# 🚀 Git Learning Journey

## From First Commit to Team Collaboration

A complete hands-on tutorial using a React project.

---

# 🎯 Goal

In this tutorial, you will learn how to use Git by building a small React counter app step by step.

You will learn:

```text
✅ Create a React project
✅ Initialize a Git repository
✅ Save versions with commits
✅ View Git history
✅ Push code to GitHub
✅ Create branches
✅ Merge branches
✅ Create Pull Requests
✅ Work like a real software team
```

---

# 🧠 What is Git?

Git is a version control system.

Think of Git as:

```text
📦 Project Box
💾 Save Point System
⏪ Time Machine
🌳 Experiment Tool
👥 Team Collaboration Tool
```

Git lets you:

```text
Save your work
Track every change
Go back to old versions
Try new ideas safely
Work with others
```

---

# 🛠️ Prerequisites

Install these first:

```text
Git
Node.js
npm
VS Code
GitHub Account
```

Check versions:

```bash
git --version
node --version
npm --version
```

Expected output should look similar to:

```text
git version 2.x.x
v22.x.x
11.x.x
```

---

# 📁 Project Name

We will create:

```text
react-learning-journey
```

---

# 🏗️ Final Project Flow

```text
Version 1:
Counter App

Version 2:
Add Reset Button

Version 3:
Add Color Button using Branch

Version 4:
Add Decrement Button using Pull Request
```

---

# 1️⃣ Create React Project

Run:

```bash
mkdir react-learning-journey
cd react-learning-journey
npm create vite@latest . -- --template react
npm install
npm run dev
```

Open browser:

```text
http://localhost:5173
```

Stop the app when needed:

```text
Ctrl + C
```

---

# 2️⃣ Create First Counter App

Open:

```text
src/App.jsx
```

Replace everything with:

```jsx
import { useState } from "react";
import "./App.css";

function App() {
  const [count, setCount] = useState(0);

  return (
    <div className="page">
      <div className="card">
        <h1>React Learning Journey</h1>

        <h2>Counter: {count}</h2>

        <button onClick={() => setCount(count + 1)}>
          Increment
        </button>
      </div>
    </div>
  );
}

export default App;
```

Open:

```text
src/App.css
```

Replace everything with:

```css
.page {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #f4f6f8;
  font-family: Arial, sans-serif;
}

.card {
  background: white;
  padding: 32px;
  border-radius: 12px;
  text-align: center;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.12);
}

button {
  padding: 10px 16px;
  margin: 8px;
  border: none;
  border-radius: 8px;
  background: #2563eb;
  color: white;
  font-weight: bold;
  cursor: pointer;
}

button:hover {
  background: #1d4ed8;
}
```

Run:

```bash
npm run dev
```

Test:

```text
Click Increment
Counter should increase
```

---

# 3️⃣ Initialize Git Repository

Run from project root:

```bash
git init
```

Check status:

```bash
git status
```

You should see files listed as untracked.

---

# 4️⃣ Create `.gitignore`

Create file:

```bash
touch .gitignore
```

Add:

```text
node_modules/
dist/
.env
.DS_Store
.vscode/
```

Check status:

```bash
git status
```

---

# 5️⃣ First Commit

Stage files:

```bash
git add .
```

Commit:

```bash
git commit -m "Create initial counter app"
```

View history:

```bash
git log --oneline
```

Expected:

```text
abc1234 Create initial counter app
```

This is your first save point.

---

# 6️⃣ Add Reset Button

Open:

```text
src/App.jsx
```

Replace with:

```jsx
import { useState } from "react";
import "./App.css";

function App() {
  const [count, setCount] = useState(0);

  return (
    <div className="page">
      <div className="card">
        <h1>React Learning Journey</h1>

        <h2>Counter: {count}</h2>

        <button onClick={() => setCount(count + 1)}>
          Increment
        </button>

        <button onClick={() => setCount(0)}>
          Reset
        </button>
      </div>
    </div>
  );
}

export default App;
```

Check changes:

```bash
git status
git diff
```

Stage and commit:

```bash
git add .
git commit -m "Add reset button"
```

View history:

```bash
git log --oneline
```

Expected:

```text
def5678 Add reset button
abc1234 Create initial counter app
```

---

# 7️⃣ Create GitHub Repository

Go to GitHub and create a new repository:

```text
react-learning-journey
```

Do not initialize it with README.

GitHub will show a command like:

```bash
git remote add origin git@github.com:YOUR_USERNAME/react-learning-journey.git
```

Run that command with your own GitHub username.

Example:

```bash
git remote add origin git@github.com:yourname/react-learning-journey.git
```

Rename branch to main:

```bash
git branch -M main
```

Push:

```bash
git push -u origin main
```

Verify remote:

```bash
git remote -v
```

---

# 8️⃣ Pull Latest Changes

If you make changes on GitHub later, download them with:

```bash
git pull
```

For now, this may say everything is already up to date.

---

# 9️⃣ Create a Branch for Color Feature

Create and switch to a new branch:

```bash
git switch -c feature/color-button
```

Check branch:

```bash
git branch
```

Expected:

```text
* feature/color-button
  main
```

---

# 🔟 Add Color Button Feature

Open:

```text
src/App.jsx
```

Replace with:

```jsx
import { useState } from "react";
import "./App.css";

function App() {
  const [count, setCount] = useState(0);
  const [color, setColor] = useState("#111827");

  return (
    <div className="page">
      <div className="card">
        <h1>React Learning Journey</h1>

        <h2 style={{ color }}>
          Counter: {count}
        </h2>

        <button onClick={() => setCount(count + 1)}>
          Increment
        </button>

        <button onClick={() => setCount(0)}>
          Reset
        </button>

        <button onClick={() => setColor("#dc2626")}>
          Make Red
        </button>
      </div>
    </div>
  );
}

export default App;
```

Test:

```bash
npm run dev
```

Click:

```text
Make Red
```

Counter text should turn red.

Commit:

```bash
git status
git add .
git commit -m "Add color button"
```

Push branch:

```bash
git push -u origin feature/color-button
```

---

# 1️⃣1️⃣ Merge Branch Locally

Switch back to main:

```bash
git switch main
```

Merge:

```bash
git merge feature/color-button
```

Push main:

```bash
git push
```

View history:

```bash
git log --oneline --graph --all
```

---

# 1️⃣2️⃣ Create Another Branch for Decrement Button

Create branch:

```bash
git switch -c feature/decrement-button
```

Open:

```text
src/App.jsx
```

Replace with:

```jsx
import { useState } from "react";
import "./App.css";

function App() {
  const [count, setCount] = useState(0);
  const [color, setColor] = useState("#111827");

  return (
    <div className="page">
      <div className="card">
        <h1>React Learning Journey</h1>

        <h2 style={{ color }}>
          Counter: {count}
        </h2>

        <button onClick={() => setCount(count + 1)}>
          Increment
        </button>

        <button onClick={() => setCount(count - 1)}>
          Decrement
        </button>

        <button onClick={() => setCount(0)}>
          Reset
        </button>

        <button onClick={() => setColor("#dc2626")}>
          Make Red
        </button>
      </div>
    </div>
  );
}

export default App;
```

Commit:

```bash
git add .
git commit -m "Add decrement button"
```

Push branch:

```bash
git push -u origin feature/decrement-button
```

---

# 1️⃣3️⃣ Create Pull Request

Go to GitHub repository.

You should see:

```text
Compare & pull request
```

Click it.

Title:

```text
Add decrement button
```

Description:

```text
This pull request adds a Decrement button to the counter app.
```

Click:

```text
Create pull request
```

Review:

```text
Files changed
Commits
Conversation
```

Click:

```text
Merge pull request
```

Then click:

```text
Confirm merge
```

---

# 1️⃣4️⃣ Update Local Main After Pull Request

On your computer:

```bash
git switch main
git pull
```

Now your local main has the merged pull request.

---

# 1️⃣5️⃣ Delete Old Branches

Delete local branch:

```bash
git branch -d feature/decrement-button
```

Delete remote branch:

```bash
git push origin --delete feature/decrement-button
```

Optional: delete color branch too:

```bash
git branch -d feature/color-button
git push origin --delete feature/color-button
```

---

# 1️⃣6️⃣ Add Tags for Learning Milestones

Tags are labels for important versions.

Create tags:

```bash
git tag v1.0-counter
git tag v1.1-reset-button
git tag v1.2-color-button
git tag v1.3-decrement-button
```

Push tags:

```bash
git push --tags
```

List tags:

```bash
git tag
```

---

# 1️⃣7️⃣ Useful Git Commands

## Check current status

```bash
git status
```

## See history

```bash
git log --oneline
```

## See all branches

```bash
git branch
```

## See remote branches

```bash
git branch -r
```

## See changes before commit

```bash
git diff
```

## Stage all changes

```bash
git add .
```

## Commit changes

```bash
git commit -m "Your message"
```

## Push changes

```bash
git push
```

## Pull changes

```bash
git pull
```

---

# 1️⃣8️⃣ Common Beginner Mistakes

## Forgot to save before commit

Check:

```bash
git status
```

If files are modified:

```bash
git add .
git commit -m "Save changes"
```

---

## Forgot which branch you are on

Run:

```bash
git branch
```

The active branch has:

```text
*
```

---

## Need to cancel local changes

Use carefully:

```bash
git restore src/App.jsx
```

This removes uncommitted changes in that file.

---

## Need to see what changed

```bash
git diff
```

---

# 1️⃣9️⃣ Final App Code

Final `src/App.jsx`:

```jsx
import { useState } from "react";
import "./App.css";

function App() {
  const [count, setCount] = useState(0);
  const [color, setColor] = useState("#111827");

  return (
    <div className="page">
      <div className="card">
        <h1>React Learning Journey</h1>

        <h2 style={{ color }}>
          Counter: {count}
        </h2>

        <button onClick={() => setCount(count + 1)}>
          Increment
        </button>

        <button onClick={() => setCount(count - 1)}>
          Decrement
        </button>

        <button onClick={() => setCount(0)}>
          Reset
        </button>

        <button onClick={() => setColor("#dc2626")}>
          Make Red
        </button>
      </div>
    </div>
  );
}

export default App;
```

Final `src/App.css`:

```css
.page {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #f4f6f8;
  font-family: Arial, sans-serif;
}

.card {
  background: white;
  padding: 32px;
  border-radius: 12px;
  text-align: center;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.12);
}

button {
  padding: 10px 16px;
  margin: 8px;
  border: none;
  border-radius: 8px;
  background: #2563eb;
  color: white;
  font-weight: bold;
  cursor: pointer;
}

button:hover {
  background: #1d4ed8;
}
```

---

# 2️⃣0️⃣ Final Workflow

Professional Git workflow:

```text
Create project
      ↓
Make change
      ↓
git status
      ↓
git add .
      ↓
git commit -m "message"
      ↓
git push
      ↓
Create branch
      ↓
Build feature
      ↓
Push branch
      ↓
Create Pull Request
      ↓
Review
      ↓
Merge
      ↓
Pull latest main
```

---

# 🏆 What You Learned

```text
📦 Repository
💾 Commit
🕵️ History
📤 Push
📥 Pull
🌳 Branch
🧩 Merge
📝 Pull Request
🏷️ Tag
👥 Team Workflow
```

You now understand the most common Git workflow used by real software teams.

