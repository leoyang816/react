# GitHub SSH Access Setup Guide

## Overview

This guide explains how to configure GitHub access using SSH public/private keys.

After completing this setup, you will be able to:

* Clone repositories without entering a username/password
* Push code securely to GitHub
* Use Git operations from Linux, WSL, macOS, or Windows

---

# Prerequisites

Verify the following tools are installed:

```bash
git --version
ssh -V
```

Example output:

```text
git version 2.49.0
OpenSSH_9.x
```

---

# Step 1: Check for Existing SSH Keys

List existing SSH keys:

```bash
ls -la ~/.ssh
```

Look for files such as:

```text
id_rsa
id_rsa.pub

or

id_ed25519
id_ed25519.pub
```

If you already have a key pair that you want to use, you may skip to Step 4.

---

# Step 2: Generate a New SSH Key Pair

Recommended algorithm: **Ed25519**

Replace the email address below with your GitHub email address.

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

Example:

```bash
ssh-keygen -t ed25519 -C "leoyang816@gmail.com"
```

---

# Step 3: Save the Key

When prompted:

```text
Enter file in which to save the key
```

Press:

```text
Enter
```

to accept the default location:

```text
~/.ssh/id_ed25519
```

Example:

```text
Enter file in which to save the key:
/home/user/.ssh/id_ed25519
```

---

## Optional: Add a Passphrase

You will be prompted:

```text
Enter passphrase
```

Options:

### Option A: Use a Passphrase (Recommended)

Provides additional security.

```text
MyStrongPassphrase
```

### Option B: No Passphrase

Press Enter twice.

Useful for learning environments and local development.

---

# Step 4: Start SSH Agent

Start the SSH agent:

```bash
eval "$(ssh-agent -s)"
```

Example output:

```text
Agent pid 12345
```

---

# Step 5: Add the Private Key

Add the key to the SSH agent:

```bash
ssh-add ~/.ssh/id_ed25519
```

Verify:

```bash
ssh-add -l
```

Example:

```text
256 SHA256:xxxxxxxxxxxxxxxx id_ed25519
```

---

# Step 6: Copy the Public Key

Display the public key:

```bash
cat ~/.ssh/id_ed25519.pub
```

Example output:

```text
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIExampleKeyValue user@example.com
```

Copy the entire line.

---

# Step 7: Add the Public Key to GitHub

Log in to GitHub.

Navigate to:

```text
Settings
  → SSH and GPG Keys
  → New SSH Key
```

Direct URL:

```text
https://github.com/settings/keys
```

Fill in:

### Title

Example:

```text
WSL-Ubuntu-Laptop
```

### Key Type

```text
Authentication Key
```

### Key

Paste the entire public key.

Click:

```text
Add SSH Key
```

---

# Step 8: Test GitHub Connectivity

Run:

```bash
ssh -T git@github.com
```

First connection:

```text
Are you sure you want to continue connecting (yes/no)?
```

Type:

```text
yes
```

Expected output:

```text
Hi <github-username>! You've successfully authenticated,
but GitHub does not provide shell access.
```

Success!

---

# Step 9: Configure Git Identity

Configure your Git username:

```bash
git config --global user.name "Your Name"
```

Example:

```bash
git config --global user.name "Tao Yang"
```

Configure your email:

```bash
git config --global user.email "leoyang816@gmail.com"
```

Verify:

```bash
git config --global --list
```

---

# Step 10: Use SSH URLs

When cloning repositories, use SSH URLs.

Example:

```bash
git clone git@github.com:username/repository.git
```

Example:

```bash
git clone git@github.com:leoyang816/react-study.git
```

Do NOT use:

```bash
git clone https://github.com/username/repository.git
```

if you want SSH authentication.

---

# Verify Remote URL

Check repository remote:

```bash
git remote -v
```

Example:

```text
origin  git@github.com:username/repository.git (fetch)
origin  git@github.com:username/repository.git (push)
```

---

# Change Existing Repository from HTTPS to SSH

Check current remote:

```bash
git remote -v
```

Example:

```text
https://github.com/username/repository.git
```

Update to SSH:

```bash
git remote set-url origin git@github.com:username/repository.git
```

Verify:

```bash
git remote -v
```

---

# Typical Daily Workflow

Clone repository:

```bash
git clone git@github.com:username/repository.git
```

Create branch:

```bash
git checkout -b feature/my-change
```

Commit changes:

```bash
git add .
git commit -m "Add new feature"
```

Push changes:

```bash
git push origin feature/my-change
```

---

# Troubleshooting

## Permission denied (publickey)

Example:

```text
Permission denied (publickey).
```

Check:

```bash
ssh-add -l
```

If no key appears:

```bash
ssh-add ~/.ssh/id_ed25519
```

---

## Verify SSH Connection

```bash
ssh -vT git@github.com
```

Review verbose output for authentication details.

---

## Verify Public Key Exists

```bash
ls -la ~/.ssh
```

Expected:

```text
id_ed25519
id_ed25519.pub
```

---

# Recommended Setup for WSL Ubuntu

Store keys inside WSL:

```text
~/.ssh/id_ed25519
```

Recommended environment:

```text
Windows
│
├── VS Code
├── Chrome
│
└── WSL Ubuntu
    ├── Git
    ├── SSH Keys
    ├── Node.js
    ├── Yarn
    └── React Projects
```

This keeps Git, SSH, and React development in a single Linux environment and avoids cross-platform configuration issues.
 
