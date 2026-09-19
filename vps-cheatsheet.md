# 🚀 Ultimate VPS Deployment Cheat Sheet
> CSE 3100 — VPS: `187.52.122.100`

---

## 📁 Table of Contents
1. [SSH Key Setup (Windows PowerShell)](#1-ssh-key-setup-windows-powershell)
2. [Connect to VPS](#2-connect-to-vps)
3. [Manual Deployment](#3-manual-deployment)
4. [MySQL Database Setup](#4-mysql-database-setup)
5. [Nginx Setup](#5-nginx-setup)
6. [PM2 Process Manager](#6-pm2-process-manager)
7. [Useful Debug Commands](#7-useful-debug-commands)

---

## 🗺️ Quick Flow — When to Do What

```
Got a new project?
        ↓
1. Fix SSH key permissions (PowerShell - once only)
        ↓
2. SSH into VPS
        ↓
3. Clone repo → ~/bookapi
        ↓
4. npm install (inside backend folder)
        ↓
5. Create ~/bookapi/.env
        ↓
6. Setup MySQL → create DB & user → update .env
        ↓
7. chmod 755 on home/bookapi/frontend folders
        ↓
8. pm2 start server.js → pm2 save
        ↓
9. Create Nginx config → enable → reload
        ↓
10. curl http://127.0.0.1:5000/api/health ✅
        ↓
   Visit: http://s20230204045.austattendance.online 🚀

--- Something broke? ---
500 error?   → sudo tail -f /var/log/nginx/error.log
               then chmod 755 folders
App crashed? → pm2 logs myapp --lines 30
DB error?    → check .env DB credentials
               check GRANT ALL PRIVILEGES in MySQL
502 error?   → pm2 status (is app running?)
               check proxy_pass port matches PORT in .env
```

---

## 1. SSH Key Setup (Windows PowerShell)

> Do this **once** after downloading your private key from teacher's Google Drive.
> Search your roll number with "s" prefix (e.g. s20230204045)

### ⚡ One Linear Command (Recommended!)
```powershell
icacls id_rsa /reset; icacls id_rsa /inheritance:r; icacls id_rsa /grant:r "$($env:USERNAME):(R)"
```

> ✅ After this, SSH will use your key automatically — no password needed!

---

## 2. Connect to VPS

```powershell
# Connect to VPS
ssh -i C:\Users\YourName\Downloads\privatekey s20230204045@187.52.122.100
```

### Copy files from PC to VPS (SCP)
```powershell
# Copy entire project folder to VPS
scp -i C:\Users\YourName\Downloads\privatekey -r C:\path\to\project s20230204045@187.52.122.100:~/bookapi
```

---

## 3. Manual Deployment

### Step 1 — Install Node.js on VPS (once)
```bash
sudo apt update
sudo apt install nodejs npm -y
node -v
npm -v
```

### Step 2 — Install PM2 (once)
```bash
sudo npm install -g pm2
pm2 startup    # auto-start on reboot
```

### Step 3 — Clone Project
```bash
# Clone directly into bookapi (teacher requires this folder name!)
git clone https://github.com/<your-username>/<your-repo> ~/bookapi
```

### Step 4 — Install Dependencies
```bash
cd ~/bookapi/backend
npm install
```

### Step 5 — Create .env File (REQUIRED by teacher)
```bash
nano ~/bookapi/.env
```
Add inside:
```
PORT=5000
DB_HOST=127.0.0.1
DB_PORT=3307
DB_USER=s20230204045
DB_PASSWORD=your_chosen_password
DB_NAME=s20230204045
```
Save: `Ctrl+X` → `Y` → `Enter`

### Step 6 — Fix Folder Permissions (CRITICAL!)
> Without this, Nginx gives 500 error!
```bash
chmod 755 /home/s20230204045
chmod 755 /home/s20230204045/bookapi
chmod 755 /home/s20230204045/bookapi/frontend
chmod 644 /home/s20230204045/bookapi/frontend/index.html
```

### Step 7 — Start App with PM2
```bash
cd ~/bookapi/backend
pm2 start server.js --name myapp
pm2 save
pm2 status
```

### Step 8 — Test App is Running
```bash
curl http://127.0.0.1:5000/api/health
```

---

## 4. MySQL Database Setup

### MySQL Container Info (this VPS)
```
Container name : bookdb
Host           : 127.0.0.1
Port           : 3307  (127.0.0.1:3307 → container 3306)
Root user      : root
Root password  : rootpass
```

### Step 1 — Enter MySQL Shell
```bash
sudo docker exec -it bookdb mysql -uroot -prootpass
```

### Step 2 — Create Your Database & User
```sql
CREATE DATABASE s20230204045;
CREATE USER 's20230204045'@'%' IDENTIFIED BY 'your_chosen_password';
GRANT ALL PRIVILEGES ON s20230204045.* TO 's20230204045'@'%';
FLUSH PRIVILEGES;
EXIT;
```

### If app needs a specific database name:
```sql
CREATE DATABASE student_registration;
GRANT ALL PRIVILEGES ON student_registration.* TO 's20230204045'@'%';
FLUSH PRIVILEGES;
EXIT;
```

### Step 3 — Restart App to Apply .env
```bash
pm2 restart myapp --update-env
```

### Quick MySQL Commands
```bash
# Enter MySQL as root
sudo docker exec -it bookdb mysql -uroot -prootpass

# List all databases
SHOW DATABASES;

# List all users
SELECT user, host FROM mysql.user;

# Use your database
USE s20230204045;

# Show tables
SHOW TABLES;
```

---

## 5. Nginx Setup

### Install Nginx (once)
```bash
sudo apt install nginx -y
sudo ufw allow 80/tcp
```

### For Backend Only (API):
```bash
sudo nano /etc/nginx/sites-available/s20230204045.conf
```
```nginx
server {
    listen 80;
    server_name s20230204045.austattendance.online;

    location / {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### For Frontend + Backend (Full Stack):
```bash
sudo nano /etc/nginx/sites-available/s20230204045.conf
```
```nginx
server {
    listen 80;
    server_name s20230204045.austattendance.online;

    # Serve frontend static files
    location / {
        root /home/s20230204045/bookapi/frontend;
        index index.html;
        try_files $uri $uri/ /index.html;
    }

    # Forward API calls to backend
    location /api/ {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Enable Config
```bash
# Enable your config (don't remove default — shared VPS!)
sudo ln -s /etc/nginx/sites-available/s20230204045.conf /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

### ⚠️ Fix Permissions After Setup (CRITICAL!)
```bash
chmod 755 /home/s20230204045
chmod 755 /home/s20230204045/bookapi
chmod 755 /home/s20230204045/bookapi/frontend
chmod 644 /home/s20230204045/bookapi/frontend/index.html
```

---

## 6. PM2 Process Manager

```bash
# Start app
pm2 start server.js --name myapp

# Stop app
pm2 stop myapp

# Restart app
pm2 restart myapp

# Restart and reload .env variables
pm2 restart myapp --update-env

# Delete app
pm2 delete myapp

# Check status
pm2 status

# View logs
pm2 logs myapp --lines 50

# View error logs only
pm2 logs myapp --err --lines 30

# Save process list (survives reboot)
pm2 save

# Auto-start on reboot (run once)
pm2 startup
```

---

## 7. Useful Debug Commands

```bash
# Check if app responds internally
curl http://127.0.0.1:5000/api/health

# Check nginx error logs (most useful for 500 errors!)
sudo tail -f /var/log/nginx/error.log

# Check nginx status
sudo systemctl status nginx

# Check nginx config syntax
sudo nginx -t

# Check what's listening on ports
ss -tlnp

# Check enabled nginx sites
ls -la /etc/nginx/sites-enabled/

# Check your folder structure
ls ~/bookapi

# Check .env file
cat ~/bookapi/.env

# View app logs
pm2 logs myapp --lines 20

# Check UFW firewall
sudo ufw status

# Check docker containers
sudo docker ps

# Find credentials file
sudo cat /home/credentials/bookdb.txt
```

---

## 🗺️ Full Architecture

```
Your PC (PowerShell)
   │
   │  ssh / scp / git clone
   ▼
VPS: 187.52.122.100
   │
   ├── ~/bookapi/                    ← teacher scans THIS folder
   │   ├── .env                      ← required by dashboard
   │   ├── backend/
   │   │   └── server.js             ← Node.js app on PORT 5000
   │   └── frontend/
   │       └── index.html            ← static files served by Nginx
   │
   ├── PM2                           ← keeps app alive
   │
   ├── Nginx (port 80)               ← forwards to port 5000
   │   ├── /       → frontend files
   │   └── /api/   → backend:5000
   │
   └── MySQL Docker (bookdb)         ← 127.0.0.1:3307
```

---

## ✅ Teacher's Dashboard Checklist

| Check | What it looks for | Fix |
|---|---|---|
| `bookapi directory` | `~/bookapi` folder exists | `mkdir -p ~/bookapi` |
| `.env in bookapi` | `~/bookapi/.env` file exists | `nano ~/bookapi/.env` |
| `health probe 200` | app returns 200 on health endpoint | check pm2 status |
| `PM2 online` | process running via pm2 | `pm2 start server.js` |
| `DB USER` | MySQL user `s<roll>` exists | create user in MySQL |

---

## 🗑️ Undo / Remove Deployment

### Stop & Remove App
```bash
pm2 stop myapp
pm2 delete myapp
pm2 save
```

### Remove Project Files
```bash
rm -rf ~/bookapi
```

### Remove Nginx Config
```bash
sudo rm /etc/nginx/sites-enabled/s20230204045.conf
sudo rm /etc/nginx/sites-available/s20230204045.conf
sudo nginx -t
sudo systemctl reload nginx
```

### Remove MySQL DB & User
```bash
sudo docker exec -it bookdb mysql -uroot -prootpass
```
```sql
DROP DATABASE s20230204045;
DROP USER 's20230204045'@'%';
FLUSH PRIVILEGES;
EXIT;
```

### Full Undo (all at once)
```bash
# 1. Stop app
pm2 delete myapp && pm2 save

# 2. Remove files
rm -rf ~/bookapi

# 3. Remove nginx
sudo rm -f /etc/nginx/sites-enabled/s20230204045.conf
sudo rm -f /etc/nginx/sites-available/s20230204045.conf
sudo nginx -t && sudo systemctl reload nginx
```

---

## ⚠️ Common Errors & Fixes

| Error | Cause | Fix |
|---|---|---|
| `Permission denied (publickey)` | Key permissions wrong | Run one linear icacls command |
| `No such file or directory` | Folder doesn't exist | `mkdir -p ~/bookapi/backend` |
| `pm2: command not found` | PM2 not installed | `sudo npm install -g pm2` |
| `EADDRINUSE port 3307` | Wrong PORT in .env (3307 is MySQL!) | Change PORT to 5000 |
| `ER_DBACCESS_DENIED_ERROR` | DB user has no permission | `GRANT ALL PRIVILEGES ON db.* TO 'user'@'%'` |
| `Permission denied` in nginx logs | Folder permissions wrong | `chmod 755` on home and project folders |
| `502 Bad Gateway` | App not running on expected port | Check pm2 status and proxy_pass port |
| `500 error` | Folder permission issue | `sudo tail -f /var/log/nginx/error.log` |
| `package-lock.json not found` | No lock file in repo | Run `npm install` locally then push |

---

> 💡 **Pro tips:**
> - Always check `sudo tail -f /var/log/nginx/error.log` for 500 errors
> - Always check `pm2 logs myapp --lines 30` for app crashes
> - Never use port 3307 as your app PORT — that's MySQL!
> - Always run `chmod 755` after copying frontend files!

---

Same flow but with one extra step — **build React first**:

```
Got a React + Node + MySQL project?
        ↓
1. Fix SSH key permissions (PowerShell - once only)
        ↓
2. SSH into VPS
        ↓
3. Clone repo → ~/bookapi
        ↓
4. Install & BUILD React frontend
   cd ~/bookapi/frontend
   npm install
   npm run build        ← creates /build folder
        ↓
5. Install backend deps
   cd ~/bookapi/backend
   npm install
        ↓
6. Create ~/bookapi/.env
        ↓
7. Setup MySQL → create DB & user → update .env
        ↓
8. chmod 755 on home/bookapi/frontend/build folders
   chmod 755 /home/s20230204045/bookapi/frontend
   chmod 755 /home/s20230204045/bookapi/frontend/build
   chmod 644 /home/s20230204045/bookapi/frontend/build/index.html
        ↓
9. pm2 start server.js → pm2 save
        ↓
10. Nginx config → point root to /frontend/build not /frontend!
    root /home/s20230204045/bookapi/frontend/build;
        ↓
11. Enable Nginx → reload
        ↓
12. Visit site 🚀
```

---

## ⚠️ Key Difference from Plain HTML Frontend

| | Plain HTML | React |
|---|---|---|
| Frontend folder | `frontend/` | `frontend/build/` |
| Extra step | ❌ | `npm run build` |
| Nginx root | `frontend/` | `frontend/build/` |
| After code change | just copy files | must rebuild! |

---

## Nginx Config for React

```nginx
server {
    listen 80;
    server_name s20230204045.austattendance.online;

    location / {
        root /home/s20230204045/bookapi/frontend/build;
        index index.html;
        try_files $uri $uri/ /index.html;
    }

    location /api/ {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Want me to add this to the `.md` file?
