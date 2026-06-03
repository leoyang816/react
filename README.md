# React Study Repository

## Overview

This repository contains a structured React learning journey designed for beginners and students who want to learn React through hands-on projects.

The goal is to:

* Learn React fundamentals
* Understand modern React development practices
* Build real applications step-by-step
* Develop problem-solving and UI development skills
* Progress from simple components to complete applications

This repository uses:

* React
* Vite
* Yarn
* Node.js 22 LTS
* WSL Ubuntu (recommended)
* Visual Studio Code

---

# Learning Philosophy

Instead of learning React through isolated concepts, this repository follows a project-based approach.

Each project introduces a small number of new concepts while reinforcing previously learned skills.

Learning progression:

```text
React Basics
    ↓
Components
    ↓
State Management
    ↓
Forms
    ↓
Component Communication
    ↓
API Integration
    ↓
Routing
    ↓
Application Architecture
```

---

# Recommended Environment

## Development Platform

```text
Windows Laptop
│
├── VS Code
├── Chrome / Edge
│
└── WSL Ubuntu
    ├── Node.js 22 LTS
    ├── Yarn 4
    ├── Git
    └── React Projects
```

## Required Software

* Node.js 22 LTS
* Yarn 4
* Git
* VS Code
* React Developer Tools Browser Extension

---

# Repository Structure

```text
react-study/
│
├── docs/
│   ├── setup/
│   ├── lessons/
│   └── guides/
│
├── projects/
│   ├── 01-counter-app/
│   ├── 02-color-changer/
│   ├── 03-emoji-clicker/
│   ├── 04-todo-list/
│   ├── 05-notes-app/
│   ├── 06-flash-card-app/
│   ├── 07-pokemon-explorer/
│   ├── 08-weather-app/
│   ├── 09-movie-explorer/
│   ├── 10-student-portal/
│   ├── 11-online-store/
│   └── 12-kanban-board/
│
└── README.md
```

---

# Learning Roadmap

## Level 1 - React Fundamentals

### Project 01: Counter App

Concepts:

* Components
* JSX
* useState
* Event Handling

Skills Learned:

* State updates
* React rendering
* Button events

---

### Project 02: Color Changer

Concepts:

* Props
* State

Skills Learned:

* Component communication
* Dynamic styling

---

### Project 03: Emoji Clicker Game

Concepts:

* Arrays
* map()
* Event Handling

Skills Learned:

* Rendering lists
* Dynamic UI updates

---

# Level 2 - Interactive Applications

### Project 04: Todo List

Concepts:

* Forms
* Lists
* CRUD Operations

Skills Learned:

* Add items
* Delete items
* Update items

---

### Project 05: Notes App

Concepts:

* Multiple Components
* State Lifting

Skills Learned:

* Parent-child communication
* Application structure

---

### Project 06: Flash Card App

Concepts:

* Conditional Rendering

Skills Learned:

* Interactive learning applications
* UI state transitions

---

# Level 3 - Working with APIs

### Project 07: Pokemon Explorer

Concepts:

* useEffect
* Fetch API

Skills Learned:

* API requests
* Loading states
* Error handling

---

### Project 08: Weather App

Concepts:

* Async Programming
* External APIs

Skills Learned:

* Real-world API integration

---

### Project 09: Movie Explorer

Concepts:

* Search
* API Integration

Skills Learned:

* Building searchable applications

---

# Level 4 - Multi-Page Applications

### Project 10: Student Portal

Concepts:

* React Router

Skills Learned:

* Navigation
* Multi-page application design

---

### Project 11: Online Store

Concepts:

* Context API

Skills Learned:

* Shared state management
* Shopping cart implementation

---

# Level 5 - Advanced React

### Project 12: Kanban Board

Concepts:

* Complex State Management
* Drag-and-Drop

Skills Learned:

* Enterprise application patterns
* Advanced UI interactions

---

# Getting Started

## Clone Repository

```bash
git clone <repository-url>
cd react-study
```

---

## Open a Project

Example:

```bash
cd projects/01-counter-app
```

---

## Install Dependencies

```bash
yarn
```

---

## Start Development Server

```bash
yarn dev
```

---

## Open Browser

```text
http://localhost:5173
```

---

# Common Yarn Commands

Install dependencies:

```bash
yarn
```

Start development server:

```bash
yarn dev
```

Build project:

```bash
yarn build
```

Preview production build:

```bash
yarn preview
```

Add a package:

```bash
yarn add <package-name>
```

---

# Coding Standards

## Component Naming

Use PascalCase:

```text
Counter.jsx
UserCard.jsx
WeatherDisplay.jsx
```

---

## Function Naming

Use camelCase:

```javascript
handleClick()
addTask()
deleteTask()
```

---

## Folder Organization

```text
src/
├── components/
├── pages/
├── hooks/
├── services/
├── assets/
└── App.jsx
```

---

# Learning Goals

By completing all projects, students will learn:

* React Components
* JSX
* Props
* State Management
* Forms
* Event Handling
* Conditional Rendering
* API Integration
* Routing
* Context API
* Application Design
* Modern React Development Practices

---

# Recommended Resources

## Official Documentation

https://react.dev

## Vite Documentation

https://vitejs.dev

## Yarn Documentation

https://yarnpkg.com

---

# Contributing

Contributions are welcome.

Possible improvements:

* Additional projects
* UI enhancements
* Accessibility improvements
* Bug fixes
* Documentation updates

---

# License

This repository is intended for educational and learning purposes.

Feel free to use, modify, and extend the projects as part of your React learning journey.

---

# Next Step

Start with:

```text
projects/01-counter-app
```

Build it, understand it, modify it, and then move on to the next project.

The best way to learn React is by building React applications.
