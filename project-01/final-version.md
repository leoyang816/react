# Kid-Friendly React Counter - Complete Working Application

## Overview

This document contains the complete source code for the final version of the Kid-Friendly React Counter application.

Features include:

* Counter
* Add and Minus buttons
* Reset button
* Conditional messages
* Custom step size
* Win condition
* Multiple React components

---

# Project Setup

Create a new React application:

```bash
npm create vite@latest react-kids-training -- --template react
cd react-kids-training
npm install
npm run dev
```

---

# Folder Structure

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

# File: src/App.jsx

```jsx
import Title from "./components/Title";
import Counter from "./components/Counter";

function App() {
  return (
    <div className="app">
      <Title />
      <p>Today we are learning React by building a small counter game.</p>
      <Counter />
    </div>
  );
}

export default App;
```

---

# File: src/components/Title.jsx

```jsx
function Title() {
  return <h1>React Kids Training</h1>;
}

export default Title;
```

---

# File: src/components/Counter.jsx

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);
  const [step, setStep] = useState(1);

  function increase() {
    setCount(count + step);
  }

  function decrease() {
    setCount(count - step);
  }

  function reset() {
    setCount(0);
    setStep(1);
  }

  return (
    <div className="card">
      <h2>Counter Game</h2>

      <p className="count">{count}</p>

      <p>
        {count > 10
          ? "Wow, big number!"
          : "Keep clicking!"}
      </p>

      {count >= 20 && <h3>You win!</h3>}

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

# File: src/main.jsx

```jsx
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App";
import "./index.css";

ReactDOM.createRoot(
  document.getElementById("root")
).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

---

# File: src/index.css

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

# Running the Application

Start the development server:

```bash
npm run dev
```

Open:

```text
http://localhost:5173
```

You should see:

* React Kids Training title
* Counter Game card
* Current counter value
* Step size input
* Add button
* Minus button
* Reset button
* Dynamic messages
* Win message when count reaches 20

---

# Concepts Demonstrated

This application demonstrates:

* Functional Components
* JSX
* useState Hook
* Event Handling
* Conditional Rendering
* Controlled Inputs
* Component Reuse
* CSS Styling
* Project Structure

This serves as an excellent foundation for future React projects such as Todo Lists, Flash Cards, Pokemon Explorer, and Weather Applications.

