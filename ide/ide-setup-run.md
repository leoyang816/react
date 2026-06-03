# React Development Setup on Windows with WSL Ubuntu

## Overview

This guide explains how to set up a React development environment using:

* Windows Laptop
* WSL (Windows Subsystem for Linux)
* Ubuntu
* VS Code
* Node.js
* React
* Git

This setup provides the performance and tooling of Linux while allowing development directly from Windows.

---

# Architecture Overview

```text
Windows Laptop
│
├── VS Code
├── Chrome / Edge Browser
│
└── WSL Ubuntu
    ├── Node.js
    ├── npm
    ├── React Project
    └── Git
```

---

# Recommended Development Environment

## Install Node.js Inside WSL

It is recommended to install Node.js inside Ubuntu rather than directly on Windows.

Open Ubuntu:

```bash
wsl
```

Install NVM (Node Version Manager):

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash
```

Reload the shell:

```bash
source ~/.bashrc
```

Install Node.js 22:

```bash
nvm install 22
nvm use 22
```

Verify installation:

```bash
node --version
npm --version
```

Example output:

```text
v22.x.x
11.x.x
```

---

# Install Visual Studio Code

Download and install Visual Studio Code:

https://code.visualstudio.com

---

# Install the VS Code WSL Extension

Install:

```text
Remote - WSL
```

Extension:

https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl

This allows VS Code to run directly inside the Ubuntu environment.

---

# Create a React Project

Create a working directory:

```bash
mkdir ~/projects
cd ~/projects
```

Create a React application using Vite:

```bash
npm create vite@latest react-kids-training -- --template react
```

Enter the project directory:

```bash
cd react-kids-training
```

Install dependencies:

```bash
npm install
```

---

# Open the Project in VS Code

From inside the project directory:

```bash
code .
```

VS Code should automatically open connected to WSL.

Look for:

```text
WSL: Ubuntu
```

in the lower-left corner of VS Code.

---

# Store Source Code in Linux Filesystem

## Recommended

Store projects inside Ubuntu:

```text
/home/<username>/projects/
```

or

```text
~/projects/
```

Example:

```text
~/projects/react-kids-training
```

---

## Not Recommended

Avoid storing active React projects under:

```text
/mnt/c/Users/...
```

Example:

```text
/mnt/c/Users/Tao/Documents
```

Reasons:

* Slower file access
* Slower hot reload
* Less reliable file watching
* Poorer overall React development experience

---

# Run the React Application

Start the development server:

```bash
npm run dev
```

Example output:

```text
Local: http://localhost:5173/
```

---

# Open the Application

Open Chrome or Edge on Windows and navigate to:

```text
http://localhost:5173
```

WSL automatically forwards ports to Windows.

No additional configuration is required.

---

# Daily Development Workflow

## Step 1: Start WSL

```bash
wsl
```

---

## Step 2: Open Project

```bash
cd ~/projects/react-kids-training
```

---

## Step 3: Open VS Code

```bash
code .
```

---

## Step 4: Start React Development Server

```bash
npm run dev
```

---

## Step 5: Open Browser

```text
http://localhost:5173
```

---

# Install Git

Git is useful for version control and GitHub integration.

Install:

```bash
sudo apt update
sudo apt install git
```

Verify:

```bash
git --version
```

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

This is especially useful for learning React.

---

# Useful Commands

## Check Node Version

```bash
node --version
```

---

## Check npm Version

```bash
npm --version
```

---

## Install Project Dependencies

```bash
npm install
```

---

## Start Development Server

```bash
npm run dev
```

---

## Create Production Build

```bash
npm run build
```

Output:

```text
dist/
```

---

## Preview Production Build

```bash
npm run preview
```

---

# Environment Verification Checklist

Run the following commands:

```bash
node --version
npm --version
git --version
code --version
```

Expected Result:

* Node.js installed
* npm installed
* Git installed
* VS Code available from WSL

If all commands succeed, the environment is fully ready for React development.

---

# Recommended Folder Structure

```text
~/projects/
│
├── react-kids-training/
├── todo-app/
├── pokemon-explorer/
├── weather-app/
└── movie-explorer/
```

As React training progresses, each project can be created under the same `~/projects` directory.

---

# Next Steps

After completing the environment setup:

1. Build the Kid-Friendly Counter App
2. Build a Color Changer App
3. Build an Emoji Clicker Game
4. Build a Todo List
5. Build a Flash Card Quiz
6. Build a Pokemon Explorer
7. Build a Weather Application
8. Build a Movie Explorer

This progression provides a gradual path from React fundamentals to real-world application development.

