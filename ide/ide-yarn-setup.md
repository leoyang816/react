# React Development with Yarn on WSL Ubuntu

## Overview

This guide describes a modern React development setup using:

* Windows Laptop
* WSL Ubuntu
* Node.js 22 LTS
* Corepack
* Yarn 4
* VS Code
* React
* Vite

This setup is ideal for React training projects and provides a consistent development experience for students and instructors.

---

# Why Use Yarn Instead of npm?

Both npm and Yarn work well for React development.

Advantages of Yarn include:

* Simpler commands
* Faster dependency installation
* Better dependency management
* Consistent lock files
* Widely used in modern React projects
* Aligns with many enterprise React environments

Examples:

| Action               | npm                 | Yarn             |
| -------------------- | ------------------- | ---------------- |
| Install dependencies | `npm install`       | `yarn`           |
| Start dev server     | `npm run dev`       | `yarn dev`       |
| Build project        | `npm run build`     | `yarn build`     |
| Preview build        | `npm run preview`   | `yarn preview`   |
| Add dependency       | `npm install axios` | `yarn add axios` |

---

# Recommended Architecture

```text
Windows Laptop
│
├── VS Code
├── Chrome / Edge
│
└── WSL Ubuntu
    ├── Node.js 22
    ├── Corepack
    ├── Yarn 4
    ├── Git
    └── React Projects
```

---

# Step 1: Install Node.js

Open Ubuntu:

```bash
wsl
```

Install Node.js using NVM:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash
```

Reload shell:

```bash
source ~/.bashrc
```

Install Node.js:

```bash
nvm install 22
nvm use 22
```

Verify:

```bash
node --version
```

Example:

```text
v22.x.x
```

---

# Step 2: Enable Corepack

Node.js 22 includes Corepack.

Enable it:

```bash
corepack enable
```

Verify:

```bash
corepack --version
```

---

# Step 3: Install Yarn

Install the latest stable Yarn version:

```bash
yarn set version stable
```

Verify:

```bash
yarn --version
```

Example:

```text
4.x.x
```

---

# Step 4: Create a React Project

Create a project directory:

```bash
mkdir ~/projects
cd ~/projects
```

Create a React application using Vite:

```bash
yarn create vite react-kids-training --template react
```

Enter the project directory:

```bash
cd react-kids-training
```

Install dependencies:

```bash
yarn
```

---

# Alternative Method

You may also create the project using npm and immediately switch to Yarn:

```bash
npm create vite@latest react-kids-training -- --template react
```

Move into project:

```bash
cd react-kids-training
```

Remove npm lock file:

```bash
rm package-lock.json
```

Install using Yarn:

```bash
yarn
```

The project will now use:

```text
yarn.lock
```

instead of:

```text
package-lock.json
```

---

# Open Project in VS Code

From the project directory:

```bash
code .
```

VS Code should display:

```text
WSL: Ubuntu
```

in the lower-left corner.

---

# Recommended Project Location

Store projects inside the Linux filesystem.

Good:

```text
~/projects/react-kids-training
```

Example:

```text
/home/username/projects/react-kids-training
```

---

Avoid:

```text
/mnt/c/Users/...
```

Reasons:

* Slower file access
* Slower hot reload
* Less reliable file watching
* Reduced performance

---

# Daily Development Workflow

## Start WSL

```bash
wsl
```

---

## Open Project

```bash
cd ~/projects/react-kids-training
```

---

## Open VS Code

```bash
code .
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

Example output:

```text
Local: http://localhost:5173/
```

---

## Open Browser

Navigate to:

```text
http://localhost:5173
```

WSL automatically forwards ports to Windows.

---

# Common Yarn Commands

## Install Dependencies

```bash
yarn
```

---

## Add a Package

Example:

```bash
yarn add axios
```

---

## Add a Development Package

Example:

```bash
yarn add -D eslint
```

---

## Start Development Server

```bash
yarn dev
```

---

## Build Production Version

```bash
yarn build
```

Output:

```text
dist/
```

---

## Preview Production Build

```bash
yarn preview
```

---

# Project Structure

```text
react-kids-training/
├── public/
├── src/
│   ├── components/
│   ├── App.jsx
│   ├── main.jsx
│   └── index.css
├── package.json
├── yarn.lock
└── vite.config.js
```

---

# Example React Training Workspace

```text
~/projects/
│
├── react-kids-training/
├── color-changer/
├── emoji-clicker/
├── todo-list/
├── flash-card-app/
├── pokemon-explorer/
├── weather-app/
└── movie-explorer/
```

Each project can be managed using the same Yarn workflow.

---

# React Developer Tools

Install the React Developer Tools browser extension.

Benefits:

* Inspect React components
* View component hierarchy
* Monitor state changes
* Debug props

Example component tree:

```text
App
 └── Counter
```

This is extremely useful when teaching React concepts.

---

# Environment Verification Checklist

Verify all tools are installed:

```bash
node --version
npm --version
yarn --version
git --version
code --version
```

Expected result:

* Node.js installed
* npm installed
* Yarn installed
* Git installed
* VS Code accessible from WSL

---

# Recommended Setup for React Training

```text
Windows Laptop
│
├── VS Code
├── Chrome
│
└── WSL Ubuntu
    ├── Node.js 22 LTS
    ├── Corepack
    ├── Yarn 4
    ├── Git
    └── React + Vite
```

Recommended commands:

```bash
yarn
yarn dev
yarn build
yarn add
```

This provides a clean, modern, and beginner-friendly environment for all React training projects.

