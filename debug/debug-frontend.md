# React Frontend Debugging Guideline Using VS Code

## 1. Create a New React App

Open a terminal and run:

```bash
npm create vite@latest react-debug-demo
```

When prompted, choose:

```text
Framework: React
Variant: JavaScript
```

Go into the project folder:

```bash
cd react-debug-demo
```

Install dependencies:

```bash
npm install
```

Start the app:

```bash
npm run dev
```

You should see something like:

```text
Local: http://localhost:5173/
```

Open that URL in your browser.

---

## 2. Create a Simple Counter Component

Open the project in VS Code:

```bash
code .
```

Edit `src/App.jsx` and replace the content with:

```jsx
import { useState } from "react";
import "./App.css";

function App() {
  const [count, setCount] = useState(0);

  function handleClick() {
    console.log("Before update:", count);

    setCount(count + 1);

    console.log("After update:", count);
  }

  return (
    <div>
      <h1>React Debug Demo</h1>

      <p>Current count: {count}</p>

      <button onClick={handleClick}>
        Increase Count
      </button>
    </div>
  );
}

export default App;
```

Save the file.

---

## 3. Test the App First

In the browser, click:

```text
Increase Count
```

The count should increase:

```text
Current count: 0
Current count: 1
Current count: 2
```

Open browser DevTools:

```text
F12 → Console
```

You will see logs like:

```text
Before update: 0
After update: 0
```

This happens because React state updates are not immediate. React schedules the update and re-renders the component afterward.

---

## 4. Add VS Code Debug Configuration

Create a folder in the project root:

```text
.vscode
```

Inside it, create a file:

```text
launch.json
```

Add this content:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "chrome",
      "request": "launch",
      "name": "Debug React App in Chrome",
      "url": "http://localhost:5173",
      "webRoot": "${workspaceFolder}/src"
    }
  ]
}
```

---

## 5. Start Debugging from VS Code

Make sure the React app is still running:

```bash
npm run dev
```

In VS Code:

```text
Run and Debug → Debug React App in Chrome → Start Debugging
```

VS Code will open a new Chrome window at:

```text
http://localhost:5173
```

---

## 6. Set a Breakpoint

Open:

```text
src/App.jsx
```

Click on the left side of this line:

```jsx
setCount(count + 1);
```

A red dot should appear. That is your breakpoint.

Example:

```jsx
function handleClick() {
  console.log("Before update:", count);

  setCount(count + 1); // breakpoint here

  console.log("After update:", count);
}
```

---

## 7. Trigger the Breakpoint

In the Chrome window opened by VS Code, click:

```text
Increase Count
```

Execution should stop in VS Code at:

```jsx
setCount(count + 1);
```

Now the browser is paused. The page may look frozen. This is normal.

The JavaScript is running in Chrome, but VS Code is attached to Chrome and controls the debugging session.

---

## 8. Inspect Variables

In VS Code, look at the left Debug panel.

You should see:

```text
Variables
count: 0
```

This means React is currently using:

```jsx
count = 0
```

You can also hover over `count` in the code to see its value.

---

## 9. Use Debug Controls

At the top of VS Code, you will see debug buttons:

```text
Continue    F5
Step Over   F10
Step Into   F11
Step Out    Shift + F11
Stop        Shift + F5
```

### Continue

Press:

```text
F5
```

The code continues running.

The browser updates:

```text
Current count: 1
```

### Step Over

Press:

```text
F10
```

VS Code runs the current line and moves to the next line.

### Step Into

Press:

```text
F11
```

VS Code goes inside a function call if possible.

---

## 10. Use the `debugger` Statement

You can also pause code manually by adding:

```jsx
debugger;
```

Example:

```jsx
function handleClick() {
  debugger;

  console.log("Before update:", count);

  setCount(count + 1);

  console.log("After update:", count);
}
```

When you click the button, the browser pauses at `debugger`.

This works with:

```text
Chrome DevTools
VS Code Debugger
```

---

## 11. Better State Debugging Example

Update the click handler:

```jsx
function handleClick() {
  setCount((previousCount) => {
    console.log("Previous count:", previousCount);

    const newCount = previousCount + 1;

    console.log("New count:", newCount);

    return newCount;
  });
}
```

This is better because it uses the latest previous state.

Full `App.jsx`:

```jsx
import { useState } from "react";
import "./App.css";

function App() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount((previousCount) => {
      console.log("Previous count:", previousCount);

      const newCount = previousCount + 1;

      console.log("New count:", newCount);

      return newCount;
    });
  }

  return (
    <div>
      <h1>React Debug Demo</h1>

      <p>Current count: {count}</p>

      <button onClick={handleClick}>
        Increase Count
      </button>
    </div>
  );
}

export default App;
```

---

## 12. Install React Developer Tools

Install the browser extension:

```text
React Developer Tools
```

After installation, open browser DevTools:

```text
F12
```

You should see:

```text
Components
Profiler
```

Use the **Components** tab to inspect:

```text
Props
State
Hooks
Component tree
```

For this example, select the `App` component and check:

```text
Hooks
State: 0
```

After clicking the button, it should update to:

```text
State: 1
```

---

## 13. Recommended Debugging Workflow

Use this order when debugging React:

```text
1. Read the browser console error
2. Add console.log around suspicious values
3. Use React DevTools to inspect props and state
4. Add a VS Code breakpoint
5. Click the UI action that triggers the code
6. Inspect variables in VS Code
7. Use Step Over or Continue
8. Check Network tab for API issues
```

---

## 14. Common Beginner Confusion

### Question

If I set a breakpoint in VS Code, does the browser stop?

### Answer

Yes.

The React app runs inside the browser. VS Code connects to the browser debugger.

When the breakpoint is reached:

```text
Browser JavaScript pauses
VS Code highlights the breakpoint line
The page may look frozen
You inspect variables in VS Code
You press Continue to resume
```

Debugging flow:

```text
VS Code
  |
  | Debugger attached
  |
Chrome Browser
  |
  | Runs React App
  |
React Components
```

---

## 15. Summary

For React frontend debugging, use:

```text
console.log          quick value checks
debugger             simple manual breakpoint
React DevTools       inspect props, state, hooks
VS Code breakpoints  step-by-step code debugging
Network tab          API request debugging
```

Best learning order:

```text
1. console.log
2. debugger
3. React DevTools
4. VS Code breakpoints
5. Network tab
```

