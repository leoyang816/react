# 🎯 Project 01 - React Kids Training: Counter Game

## 🌟 Project Goal

Welcome to your first React project!

In this project, we will build a simple Counter Game while learning the most important React fundamentals.

By the end of this project, you will understand:

✅ Components

✅ JSX

✅ State (`useState`)

✅ Events (`onClick`)

✅ Conditional Rendering

✅ User Input

✅ Multiple Components

---

# 🗺️ Learning Roadmap

```text
STEP 1 → Create Project
STEP 2 → Build Counter
STEP 3 → Add Messages
STEP 4 → Add Step Size Input
STEP 5 → Create Title Component
STEP 6 → Add Win Condition
STEP 7 → Run and Play
```

---

# 🚀 STEP 1 - Create Project

## 🎯 Goal

Create a React application using Vite.

## 🛠 Command

```bash
yarn create vite react-kids-training --template react
```

Move into project:

```bash
cd react-kids-training
```

Install dependencies:

```bash
yarn
```

Start development server:

```bash
yarn dev
```

Open browser:

```text
http://localhost:5173
```

---

## ✅ Checkpoint

You should see the default Vite React page.

If yes:

🎉 Congratulations! React is running.

---

# 📁 STEP 2 - Create Project Structure

## 🎯 Goal

Organize code into reusable components.

Create the following structure:

```text
src/
├── components/
│   ├── Counter.jsx
│   └── Title.jsx
├── App.jsx
├── main.jsx
└── index.css
```

---

## 🧠 What Are Components?

Think of components like LEGO blocks.

Each component does one job.

Example:

```text
App
├── Title
└── Counter
```

---

# 🔢 STEP 3 - Build the Counter

## 🎯 Goal

Learn React State.

Create:

```text
src/components/Counter.jsx
```

## 🧠 New Concept

State stores data.

React remembers state values.

Example:

```jsx
const [count, setCount] = useState(0);
```

This means:

```text
count = current value

setCount = update value
```

---

## 💻 Counter Code

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  function increase() {
    setCount(count + 1);
  }

  function decrease() {
    setCount(count - 1);
  }

  function reset() {
    setCount(0);
  }

  return (
    <div>
      <h2>Counter Game</h2>

      <p>{count}</p>

      <button onClick={increase}>
        Add
      </button>

      <button onClick={decrease}>
        Minus
      </button>

      <button onClick={reset}>
        Reset
      </button>
    </div>
  );
}

export default Counter;
```

---

## ✅ Checkpoint

Try:

```text
Click Add
Click Minus
Click Reset
```

Expected:

```text
0 → 1 → 2 → 3
3 → 2 → 1
Reset → 0
```

🎉 You just used React State!

---

# 🎨 STEP 4 - Add Dynamic Messages

## 🎯 Goal

Learn Conditional Rendering.

React can display different content based on state.

---

## 💻 Add This Code

Inside Counter component:

```jsx
<p>
  {count > 10
    ? "🌟 Wow, big number!"
    : "🚀 Keep clicking!"}
</p>
```

---

## 🧠 What Is Happening?

If:

```text
count > 10
```

show:

```text
🌟 Wow, big number!
```

Otherwise show:

```text
🚀 Keep clicking!
```

---

## ✅ Checkpoint

Test:

```text
Count = 5
```

Result:

```text
🚀 Keep clicking!
```

Test:

```text
Count = 11
```

Result:

```text
🌟 Wow, big number!
```

---

# 🔧 STEP 5 - Add Step Size Input

## 🎯 Goal

Learn User Input.

Users can choose how much to add or subtract.

---

## 🧠 New Concept

Add another piece of state:

```jsx
const [step, setStep] = useState(1);
```

---

## 💻 Add Input

```jsx
<div>
  <label>
    Step size:
    <input
      type="number"
      value={step}
      onChange={(e) =>
        setStep(Number(e.target.value))
      }
    />
  </label>
</div>
```

---

## 💻 Update Functions

Replace:

```jsx
setCount(count + 1);
```

with:

```jsx
setCount(count + step);
```

Replace:

```jsx
setCount(count - 1);
```

with:

```jsx
setCount(count - step);
```

---

## ✅ Checkpoint

Set:

```text
Step Size = 5
```

Click:

```text
Add
```

Expected:

```text
0 → 5 → 10 → 15
```

🎉 You just connected a form to React state!

---

# 🏗 STEP 6 - Create a Title Component

## 🎯 Goal

Learn Multiple Components.

Create:

```text
src/components/Title.jsx
```

---

## 💻 Title Component

```jsx
function Title() {
  return (
    <h1>
      🎮 React Kids Training
    </h1>
  );
}

export default Title;
```

---

## 💻 Update App.jsx

```jsx
import Title from "./components/Title";
import Counter from "./components/Counter";

function App() {
  return (
    <div className="app">
      <Title />

      <p>
        Today we are learning React by
        building a Counter Game.
      </p>

      <Counter />
    </div>
  );
}

export default App;
```

---

## ✅ Checkpoint

Expected page:

```text
🎮 React Kids Training

Today we are learning React by
building a Counter Game.

Counter Game
```

---

# 🏆 STEP 7 - Add a Win Condition

## 🎯 Goal

Turn the Counter into a Game.

---

## 💻 Add This Code

Inside Counter component:

```jsx
{count >= 20 && (
  <h3>
    🏆 You Win!
  </h3>
)}
```

---

## 🧠 What Is Happening?

If:

```text
count >= 20
```

React displays:

```text
🏆 You Win!
```

Otherwise:

```text
Nothing
```

---

## ✅ Checkpoint

Reach:

```text
20
```

Expected:

```text
🏆 You Win!
```

---

# 🎨 STEP 8 - Add Styling

## 🎯 Goal

Make the app look nice.

Replace:

```text
src/index.css
```

with:

```css
body {
  font-family: Arial, sans-serif;
  background: #f5f5f5;
}

.app {
  max-width: 600px;
  margin: 40px auto;
  text-align: center;
}

.card {
  background: white;
  padding: 24px;
  border-radius: 12px;
  border: 1px solid #ddd;
}

.count {
  font-size: 48px;
  font-weight: bold;
}

input {
  margin-left: 8px;
  padding: 6px;
  width: 80px;
}

button {
  margin: 8px;
  padding: 10px 16px;
  font-size: 16px;
  cursor: pointer;
}
```

---

# 🎉 Final Application Features

Your application now supports:

✅ Counter

✅ Add Button

✅ Minus Button

✅ Reset Button

✅ Custom Step Size

✅ Dynamic Messages

✅ Win Condition

✅ Multiple Components

✅ CSS Styling

---

# 🧠 React Concepts Learned

| Concept               | Learned |
| --------------------- | ------- |
| JSX                   | ✅       |
| Components            | ✅       |
| State                 | ✅       |
| useState              | ✅       |
| Events                | ✅       |
| Forms                 | ✅       |
| Conditional Rendering | ✅       |
| Multiple Components   | ✅       |

---

# 🚀 Challenge Exercises

Try these on your own:

### ⭐ Easy

Add:

```text
🎯 Goal: Reach 50
```

---

### ⭐⭐ Medium

Add:

```text
Maximum count = 100
```

Prevent going higher.

---

### ⭐⭐⭐ Advanced

Add:

```text
High Score
```

Track the largest number reached.

---

# 🏁 Project Complete

Congratulations!

You have built your first React application and learned the core concepts used in almost every React project.

Next Project:

```text
02-color-changer
```

where you will learn:

🎨 Props

🎨 Dynamic Styling

🎨 Component Communication

