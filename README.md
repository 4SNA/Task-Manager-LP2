# Assignment 13 - MERN-Based Task Management Application

# Assignment 14 - Cloud-Based To-Do List Manager

## Problem Statement - Assignment 13

Deploy a MERN-based task management system where users can create, update, and delete tasks. Ensure the frontend communicates with the backend API and the database is hosted properly.

---

## Problem Statement - Assignment 14

Deploy a task management application that allows users to create, update, and delete tasks. Configure the application so that it stores data in a backend database hosted on the cloud.

---

# Repository

```text
https://github.com/4SNA/Task-Manager-LP2
```

---

# Technologies Used

- AWS EC2 Ubuntu Instance
- React + Vite Frontend
- Node.js + Express.js Backend
- MongoDB Atlas Database
- Express
- PM2
- Git
- npm
- NVM (Node Version Manager)

---

# Features

- Create tasks
- Update tasks
- Delete tasks
- Task status management
- MongoDB Atlas database integration
- Public cloud deployment
- MERN architecture

---

# Architecture

```text
User Browser
     |
     v
AWS EC2 Instance
     |
     |-- React + Vite Frontend
     |-- Express Backend API (Port 5000)
     |
     v
MongoDB Atlas Database
```

---

# Step 1 - Create AWS EC2 Instance

1. Open AWS Console.
2. Go to EC2.
3. Click Launch Instance.
4. Select Ubuntu 22.04 LTS or Ubuntu 24.04 LTS.
5. Select t2.micro.
6. Create/select key pair.
7. Download `.pem` file.
8. Configure Security Group.

---

# Security Group Inbound Rules

| Type | Port | Source |
|---|---|---|
| SSH | 22 | My IP |
| HTTP | 80 | 0.0.0.0/0 |
| HTTPS | 443 | 0.0.0.0/0 |
| Custom TCP | 5000 | 0.0.0.0/0 |
| Custom TCP | 5173 | 0.0.0.0/0 |

---

# Step 2 - Connect to EC2

Open terminal or PowerShell where `.pem` exists.

```bash
chmod 400 your-key.pem
```

```bash
ssh -i your-key.pem ubuntu@YOUR_PUBLIC_IP
```

Example:

```bash
ssh -i task.pem ubuntu@15.206.xxx.xxx
```

---

# Step 3 - Update Ubuntu

```bash
sudo apt update
sudo apt upgrade -y
```

---

# Step 4 - Install Git

```bash
sudo apt install git -y
```

Verify:

```bash
git --version
```

---

# Step 5 - Install Node.js Using NVM

Install curl:

```bash
sudo apt install curl -y
```

Install NVM:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```

Reload terminal:

```bash
source ~/.bashrc
```

Install latest LTS Node.js:

```bash
nvm install --lts
```

Verify:

```bash
node -v
npm -v
```

---

# Step 6 - Install PM2

```bash
sudo npm install -g pm2
```

Verify:

```bash
pm2 --version
```

---

# Step 7 - MongoDB Atlas Setup

Use MongoDB Atlas cloud database.

---

# MongoDB Atlas Details

```text
Username: lp2_user
Password: lp2password123
Database Name: taskmanager
Cluster Name: Cluster0
```

---

# Atlas Setup Steps

1. Open MongoDB Atlas.
2. Create free M0 cluster.
3. Create database user:
   ```text
   lp2_user
   ```
4. Set password:
   ```text
   lp2password123
   ```
5. Go to:
   ```text
   Network Access
   ```
6. Click:
   ```text
   Add IP Address
   ```
7. Click:
   ```text
   Allow Access From Anywhere
   ```
8. Confirm:
   ```text
   0.0.0.0/0
   ```

MongoDB Atlas automatically creates database `taskmanager` after first insertion.

---

# Step 8 - Clone Repository

```bash
git clone https://github.com/4SNA/Task-Manager-LP2.git
```

```bash
cd Task-Manager-LP2
```

Check files:

```bash
ls
```

Expected:

```text
backend
frontend
README.md
```

---

# Step 9 - Frontend Setup

Go to frontend:

```bash
cd frontend
```

Install frontend dependencies:

```bash
npm install
```

---

# Step 9.1 - Fix localhost API Issue

Search localhost references:

```bash
grep -R "localhost" .
```

If you find:

```text
http://localhost:5000
```

replace with your EC2 public IP.

Example:

```text
http://15.206.xxx.xxx:5000
```

Quick replace:

```bash
grep -rl "localhost:5000" . | xargs sed -i 's|http://localhost:5000|http://15.206.xxx.xxx:5000|g'
```

---

# Step 9.2 - Configure Vite Public Hosting

Open Vite config:

```bash
nano vite.config.js
```

OR:

```bash
nano vite.config.ts
```

Ensure:

```js
server: {
  host: "0.0.0.0",
  port: 5173
}
```

Example:

```js
import { defineConfig } from 'vite'

export default defineConfig({
  server: {
    host: "0.0.0.0",
    port: 5173
  }
})
```

---

# Step 9.3 - Build Frontend

```bash
npm run build
```

This creates a `dist` folder.

---

# Step 10 - Backend Setup

Go to backend:

```bash
cd ../backend
```

Install backend dependencies:

```bash
npm install
```

Create `.env`:

```bash
nano .env
```

Paste:

```env
PORT=5000
MONGODB_URI=mongodb+srv://lp2_user:lp2password123@cluster0.tki1kaj.mongodb.net/taskmanager?retryWrites=true&w=majority&appName=Cluster0
JWT_SECRET=sarthak123
```

Save:

```text
CTRL + X → Y → Enter
```

Check `.env`:

```bash
cat .env
```

---

# Step 11 - Express 5 Fix

Open backend file:

```bash
nano index.js
```

Add this before `app.listen()`:

```js
const path = require('path');

// Serve frontend static files
app.use(express.static(path.join(__dirname, '../frontend/dist')));

// Express 5 Catch-all Route Fix
app.get(/.*/, (req, res) => {
  res.sendFile(path.join(__dirname, '../frontend/dist', 'index.html'));
});
```

Replace existing `app.listen()` with:

```js
app.listen(PORT, '0.0.0.0', () => {
  console.log(`Server running on port ${PORT}`);
});
```

Save:

```text
CTRL + X → Y → Enter
```

---

# Alternative Express Fix (Optional)

If wildcard route error occurs:

```bash
npm uninstall express
npm install express@4
npm list express
```

---

# Step 12 - Run Backend Using PM2

Check files:

```bash
ls
```

If `index.js` exists:

```bash
pm2 start index.js --name task-manager-api
```

Save PM2 process:

```bash
pm2 save
```

Configure startup:

```bash
pm2 startup
```

Run the command PM2 outputs.

Check PM2 status:

```bash
pm2 status
```

---

# Step 13 - Run Frontend Publicly

Open another SSH terminal.

Go to frontend:

```bash
cd ~/Task-Manager-LP2/frontend
```

Run frontend:

```bash
npm run dev -- --host 0.0.0.0
```

Expected:

```text
Local:   http://localhost:5173
Network: http://172.xxx.xxx.xxx:5173
```

---

# Step 14 - Test Backend Locally

```bash
curl http://localhost:5000
```

If HTML or JSON appears:
backend is working.

---

# Step 15 - Access Application Publicly

## Backend

```text
http://YOUR_PUBLIC_IP:5000
```

---

## Frontend

```text
http://YOUR_PUBLIC_IP:5173
```

Example:

```text
http://15.206.xxx.xxx:5173
```

---

# Step 16 - Verify MongoDB Atlas

1. Open MongoDB Atlas.
2. Go to Database.
3. Click Browse Collections.
4. Add one task using the application.
5. Database `taskmanager` should appear.
6. Collections/documents should be visible.

---

# Useful Commands

## Check PM2

```bash
pm2 list
```

---

## View Logs

```bash
pm2 logs
```

---

## Restart Backend

```bash
pm2 restart all
```

---

## Stop Backend

```bash
pm2 stop all
```

---

## Delete PM2 Processes

```bash
pm2 delete all
```

---

## Test Backend

```bash
curl http://localhost:5000
```

---

# Common Errors and Fixes

---

## 1. pm2 command not found

```bash
sudo npm install -g pm2
```

---

## 2. Frontend only works on localhost

Fix Vite config:

```js
server: {
  host: "0.0.0.0",
  port: 5173
}
```

Run:

```bash
npm run dev -- --host 0.0.0.0
```

---

## 3. localhost API issue

Replace:

```text
http://localhost:5000
```

with:

```text
http://YOUR_PUBLIC_IP:5000
```

---

## 4. MongoDB Atlas connection failed

Check:

```text
Network Access → 0.0.0.0/0
```

Check `.env`:

```env
MONGODB_URI=mongodb+srv://lp2_user:lp2password123@cluster0.tki1kaj.mongodb.net/taskmanager?retryWrites=true&w=majority&appName=Cluster0
```

Restart:

```bash
pm2 restart all
pm2 logs
```

---

## 5. PathError Missing parameter name

Express v5 wildcard route issue.

Fix:

```js
app.get(/.*/, (req, res) => {
  res.sendFile(path.join(__dirname, '../frontend/dist', 'index.html'));
});
```

OR downgrade:

```bash
npm uninstall express
npm install express@4
```

---

## 6. EADDRINUSE port 5000 already in use

Fix:

```bash
pm2 delete all
pm2 start index.js --name task-manager-api
```

---

## 7. Cannot find module

Run:

```bash
npm install
```

inside frontend/backend.

---

# Viva Questions

## What is EC2?

AWS virtual machine service.

---

## What is MongoDB Atlas?

Cloud MongoDB database service.

---

## What is PM2?

Node.js process manager.

---

## Why use NVM?

NVM helps manage multiple Node.js versions.

---

## Why ports 5000 and 5173?

- 5000 → Backend API
- 5173 → React frontend

---

## What is Express?

Backend framework for Node.js.

---

# Output

The Task Management Application / To-Do List Manager is successfully deployed and publicly accessible using:

```text
http://YOUR_PUBLIC_IP:5173
```

---

# Conclusion

The MERN-based Task Management Application and To-Do List Manager were successfully deployed on AWS EC2 Ubuntu using React frontend, Node.js/Express backend, and MongoDB Atlas database. The application supports CRUD task management operations and is publicly accessible through EC2 public IP.
