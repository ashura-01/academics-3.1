---
tags: [cse3100, devops, exam-prep]
---

# CSE 3100 — Lecture 5: DevOps (Exam Cheat Sheet)

## 1. What is DevOps?
**Dev**elopment + **Op**erations working as one team instead of two silos.

- **Dev team** → builds features, fixes bugs, writes tests, handles security
- **Ops team** → keeps software running, monitors performance, manages infrastructure

> 🍽️ Analogy: Chefs (Dev) cook the food, waiters (Ops) deliver it smoothly. DevOps = they work as one team.

---

## 2. The 7 DevOps Phases
> 🧠 Mnemonic: **"Please Code Builds That Release Deployed Monitors"**

| # | Phase | What happens |
|---|-------|--------------|
| 1 | **Plan** | Define goals, user stories, requirements |
| 2 | **Code** | Write the application |
| 3 | **Build** | Compile app into deployable state (Jenkins, GitHub Actions) |
| 4 | **Test** | Validate quality & functionality |
| 5 | **Release** | Package app after testing |
| 6 | **Deploy** | Push to production/staging (Docker, Kubernetes) |
| 7 | **Monitor** | Watch system in production, feed learnings into next Plan |

All automated via **CI/CD**.

---

## 3. Continuous Integration (CI)
**Definition:** Devs frequently merge code into a shared repo → system **auto-builds + auto-tests** every change → catches bugs early.

**CI Workflow (memorize order):**
1. Dev commits code locally (Git)
2. Push to remote repo (GitHub/GitLab/Bitbucket)
3. CI server detects push → triggers build
4. Automated tests run
5. Feedback: ✅ "green" (pass) or ❌ notify dev to fix
6. If passed → ready for deployment

**Tools:** Jenkins, GitHub Actions, GitLab CI, CircleCI

---

## 4. Continuous Delivery (CD)
**Definition:** Extension of CI — automatically deploys tested code to staging/production.

> Key line: "You can deploy anytime by clicking a button." (release is automated, but final production push can be **manual**)

- **Continuous Delivery** → last deploy-to-production step is **manual**
- **Continuous Deployment** → *everything* including production is **automatic**

**CI/CD Pipeline flow:**
```
Code → Commit → Build → Unit Tests → Integration Tests (CI Pipeline)
     → Review → Staging → Production (CD Pipeline)
```

---

## 5. GitHub Actions
- CI/CD tool built into GitHub
- Automates build/test/deploy when events happen (push, PR, release)
- Code runs on **code-runners** (virtual machines)
- Workflow files live in `.github/workflows/` folder, written in **YAML**

**Things you can do with GitHub Actions:**
- 🔐 **Secrets** — store API keys in Settings → used as `${{ secrets.NAME }}`
- 🔗 **Job dependencies** — via `needs`
- 🧮 **Matrix builds** — run job across multiple versions in parallel
- 📩 **Notifications** — Slack/email on results
- ⚡ **Conditional execution** — using `if`

---

## 6. VPS (Virtual Private Server)
**Definition:** A virtual machine on a physical server, isolated with its own dedicated resources.

**Key concepts:**
- **Virtual isolation** — hypervisor splits physical machine into independent servers
- **Dedicated resources** — guaranteed CPU/RAM/SSD, not shared
- **Full autonomy** — own OS + root access

**Why use VPS?**
- Full config control (install Nginx, DB, custom security)
- Consistent performance (isolated from noisy neighbors)
- On-demand scalability

**Popular providers:** DigitalOcean, AWS, Hostinger

---

## 7. SSH (Secure Shell)
Secure encrypted way to connect to a remote server, replacing password logins.

**Key pair concept:**
- 🔒 **Public key** — the "lock", lives on the server, safe to share
- 🔑 **Private key** — the "key", stays on your machine, NEVER share

**Handshake:** server encrypts a message with your public key → only your private key can decrypt it → proves identity without sending the private key over the network.

**Connect command:**
```powershell
ssh -i ~/.ssh/privatesshkey username@187.52.122.100
```
Default SSH port = **22**

**Alternatives when SSH fails:**
- Web Console (VNC) — browser-based terminal from hosting dashboard
- SFTP/SCP — file transfer over SSH
- Custom ports — reduce bot scans by changing port from 22

---

## 8. Common Linux Commands
| Command | What it does |
|---|---|
| `pwd` | print current directory |
| `ls -a` | list all files (incl. hidden) |
| `cd [dir]` | change directory |
| `mkdir [name]` | create folder |
| `rm -rf` | force remove recursively |
| `cat [file]` | show file contents |
| `nano [file]` | edit file in terminal |
| `tail -f [log]` | live log output |
| `zip`/`unzip` | compress/extract |
| `ssh [user]@[ip]` | connect to remote server |
| `scp [file] [dest]` | copy file securely over SSH |
| `sudo [cmd]` | run as root |
| `whoami` | show current user |
| `journalctl` | view systemd logs |

---

## 9. VPS Firewalls
**Inbound (default = DENY):**
- ✅ Allow: SSH (22, restrict to trusted IPs), HTTP/HTTPS (80/443)
- ❌ Block: everything else, especially DB ports (3306 MySQL / 5432 PostgreSQL)

**Outbound (default = ALLOW, but restrict):**
- ✅ Allow: DNS (53), NTP (123), HTTP/HTTPS (80/443)
- ❌ Block: SMTP (25 — stops spam malware), unassigned ports (stops reverse shells)

**UFW commands:**
```bash
sudo ufw status verbose
sudo ufw enable / disable
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp
sudo ufw allow in 80/tcp
sudo ufw deny in 3306/tcp
sudo ufw allow out 53
sudo ufw deny out 25/tcp
```

---

## 10. Database Access (Shared MySQL on VPS)
```bash
# SSH into VPS
ssh s<your_roll>@187.52.122.100

# Enter MySQL as root inside Docker container
docker exec -it <containerid> mysql -uroot -p"$ROOT_PW"
```
```sql
CREATE DATABASE s<yourroll>;
CREATE USER 's<yourroll>'@'%' IDENTIFIED BY 'your-password';
GRANT ALL PRIVILEGES ON s<yourroll>.* TO 's<yourroll>'@'%';
FLUSH PRIVILEGES;
```
⚠️ Always prefix username/db with **"s"**.

---

## 11. Nginx
**Definition:** High-performance web server + reverse proxy.

**Key features:** static file serving, load balancing, SSL/TLS termination, reverse proxy, high concurrency, low resource use.

### Reverse Proxy
Sits between client and backend servers, forwards requests.
```
Client → Reverse Proxy → Application Server
```
**Why use one?**
- Hides internal infrastructure (security)
- Distributes traffic (scalability)
- Handles SSL centrally
- Caching = better performance

**Sample config:**
```nginx
server {
  listen 80;
  server_name cse3100.aliahnaf.fun;
  location / {
    proxy_pass http://127.0.0.1:3000;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }
}
```
**Installation steps:**
1. `sudo nano /etc/nginx/sites-available/cse3100.conf`
2. `sudo ln -s /etc/nginx/sites-available/cse3100.conf /etc/nginx/sites-enabled/`
3. `sudo nginx -t` (test syntax)
4. `sudo systemctl reload nginx`

---

## 12. DNS (Domain Name System)
**Definition:** Translates domain names → IP addresses.

**How it works (memorize the 8 steps):**
1. User enters `example.com`
2. Browser checks local DNS cache
3. Request → DNS Resolver
4. Resolver queries Root DNS Servers
5. Root server → points to TLD (.com) servers
6. TLD server → gives authoritative DNS server
7. Authoritative server → returns IP address
8. Browser connects to destination server

---

## 13. Cloudflare
Acts as a protective **reverse proxy + CDN** between users and your server.

**Key capabilities:**
- **DNS Resolution** — fast global DNS
- **Security/WAF** — blocks SQLi, DDoS, malicious traffic
- **SSL/TLS** — free auto-provisioned certificates
- **Edge Caching** — caches static files closer to users

**Domain setup:**
```
example.com → Cloudflare DNS → Server IP → Nginx → App (Port 3000)
```
DNS Records needed:
| Type | Name | Value |
|---|---|---|
| A | @ | Server Public IP |
| A | www | Server Public IP |

**Cloudflare WARP VPN:** visit `one.one.one.one` → download → install.

---

## 14. CI/CD to VPS (Full Pipeline)
1. **Push Code** — dev commits/pushes → triggers webhook
2. **Build & Test** — runner installs deps, runs tests, builds artifact/Docker image
3. **VPS Deploy** — artifact copied via SSH/SCP → containers/PM2 restarted (zero downtime)

---

## 15. Infrastructure as Code (IaC)
**Definition:** Provisioning infrastructure (servers, DBs, networks) using **code** instead of manual setup.

**Why it matters for DevOps:**
- ⚡ Faster deployments — run a script instead of manual setup
- 🎯 Consistency — same environment every time
- 🧹 Less manual work — no manual sysadmin config
- 🔁 Same process everywhere — dev/test/prod behave identically

**Popular tools:** Terraform, AWS CloudFormation, Google Cloud Deployment Manager

---

## 16. Classwork Reference (GitHub Actions CI Task)
Build a `.github/workflows/nodejs-ci.yml` that:
1. Triggers on push to `main`
2. Runs on Ubuntu + latest Node.js
3. `npm install` dependencies
4. `npm test` to run unit tests
5. ESLint step — fail pipeline on lint errors
6. Cache `node_modules` for speed
7. Matrix build across Node versions (14.x, 16.x, 18.x)

---

## 🎯 Quick Recall Summary (last-minute glance)
- **DevOps** = Dev + Ops merged → smoother delivery
- **7 phases**: Plan → Code → Build → Test → Release → Deploy → Monitor
- **CI** = auto build + test on every push
- **CD** = auto deploy after CI passes (Delivery = manual final step, Deployment = fully automatic)
- **VPS** = isolated virtual server with root access
- **SSH** = public key (lock) on server, private key (key) stays local
- **Nginx** = reverse proxy, routes traffic to app
- **DNS** = domain name → IP address (8-step resolution)
- **Cloudflare** = reverse proxy + CDN + security shield
- **IaC** = infrastructure defined in code (Terraform etc.) for consistency & speed
