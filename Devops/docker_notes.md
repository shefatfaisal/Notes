# Docker Complete Notes

---

## 1. What is a Dockerfile?

A **Dockerfile** is a plain text script with instructions that Docker uses to automatically build a container image. Think of it as a **recipe** — it tells Docker exactly how to assemble your app's environment.

---

## 2. Dockerfile — Key Instructions

```dockerfile
FROM node:18-alpine       # Base image to start from
WORKDIR /app              # Set working directory inside container
COPY package*.json ./     # Copy files from host to container
RUN npm install           # Run command during BUILD time
COPY . .                  # Copy rest of source code
EXPOSE 3000               # Document which port the container uses
CMD ["node", "server.js"] # Command to run when container STARTS
```

### Instruction Reference Table

| Instruction | Purpose |
|---|---|
| `FROM` | Sets the base image. Every Dockerfile must begin with this |
| `WORKDIR` | Sets working directory inside container for subsequent commands |
| `COPY` | Copies files/folders from host machine into the container |
| `ADD` | Like COPY, but also supports URLs and auto-extracts `.tar` files |
| `RUN` | Executes a command during BUILD time (e.g., install dependencies) |
| `ENV` | Sets environment variables inside the container |
| `EXPOSE` | Documents which port the container listens on |
| `CMD` | Default command to run when container starts. Can be overridden |
| `ENTRYPOINT` | Like CMD, but harder to override — defines main executable |
| `ARG` | Defines build-time variables (passed via `--build-arg`) |
| `VOLUME` | Creates a mount point for persistent or shared data |
| `USER` | Sets the user to run subsequent commands (security) |

---

## 3. RUN vs CMD vs ENTRYPOINT

```dockerfile
RUN npm install        # Executes at BUILD time → baked into the image

CMD ["npm", "start"]   # Executes at RUN time → can be overridden
                       # docker run myimage npm test  ← overrides CMD

ENTRYPOINT ["node"]    # Executes at RUN time → NOT easily overridden
CMD ["server.js"]      # Acts as default argument to ENTRYPOINT
                       # → runs: node server.js
```

### Simple Mental Model

| | `RUN` | `CMD` |
|---|---|---|
| **When it runs** | During `docker build` | During `docker run` |
| **Purpose** | Set up the image (install, configure) | Start your app |
| **Saved to image?** | Yes | No, just an instruction |
| **Can override?** | No | Yes |

> **Think of it like building a house:**
> - `RUN` = construction work (build time) — laying bricks, permanent
> - `CMD` = what you do every morning (run time) — starts fresh each time

---

## 4. Multi-Stage Builds

Keeps final image small by separating build and runtime:

```dockerfile
# Stage 1: Build
FROM node:18 AS builder
WORKDIR /app
COPY . .
RUN npm install && npm run build

# Stage 2: Production (only copies built output)
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
```

---

## 5. The `docker build` Command

After writing your Dockerfile, run `docker build` to turn it into an **image**.

```bash
docker build -t my-app:1.0 .
```

| Part | Meaning |
|---|---|
| `docker build` | Command to build an image |
| `-t my-app:1.0` | Tag — gives image a name and version |
| `.` | Build context — current folder where Dockerfile lives |

### What Happens Step by Step

```
Step 1/5 : FROM node:18-alpine      ← pulls base image
Step 2/5 : WORKDIR /app             ← sets working directory
Step 3/5 : COPY package*.json ./    ← copies files
Step 4/5 : RUN npm install          ← installs dependencies
Step 5/5 : CMD ["node", "app.js"]   ← sets start command

Successfully built a1b2c3d4e5f6
```

### Important Flags

```bash
docker build -t my-app .                          # name and tag
docker build -f Dockerfile.prod -t my-app .       # different Dockerfile
docker build --build-arg ENV=production -t my-app # pass build variables
docker build --no-cache -t my-app .               # fresh build, no cache
docker build --target builder -t my-app:dev .     # build specific stage
```

### Build Context & .dockerignore

The `.` sends your entire folder to Docker. Use `.dockerignore` to exclude unnecessary files:

```
# .dockerignore
node_modules
.git
*.log
.env
```

---

## 6. Building Multiple Images

```bash
# Build only a specific stage and tag it
docker build --target builder    -t my-app:dev  .
docker build --target production -t my-app:prod .

# Separate Dockerfiles for unrelated images
docker build -t frontend-app ./frontend
docker build -t backend-app  ./backend
```

### Docker Compose (Easiest for Multiple Images)

```yaml
# docker-compose.yml
services:
  frontend:
    build:
      context: ./frontend
    image: my-frontend:1.0

  backend:
    build:
      context: ./backend
    image: my-backend:1.0
```

```bash
docker compose build    # builds ALL images at once
docker compose up       # runs all containers together
```

---

## 7. ENV — Environment Variables

`ENV` sets variables inside the container — like configuration values your app reads at runtime. Similar to how `~` is `$HOME` on Linux, ENV variables are pre-set values the container knows.

```dockerfile
ENV PORT=3000
ENV NODE_ENV=production
ENV DB_HOST=localhost
```

Your app reads them:

```javascript
const port = process.env.PORT       // → 3000
const env  = process.env.NODE_ENV   // → production
```

### Override ENV at Runtime

```bash
docker run -e PORT=5000 my-app                      # override single
docker run -e PORT=5000 -e NODE_ENV=development my-app  # override multiple
docker run --env-file .env my-app                   # from .env file
```

### ENV vs ARG

| | `ENV` | `ARG` |
|---|---|---|
| **Available at** | Build time + Run time | Build time only |
| **App can read it?** | Yes | No |
| **Override with** | `-e` flag at `docker run` | `--build-arg` at `docker build` |

### ENV Lifetime

```
docker run → container created → ENV variables loaded → app starts → app reads them
```

ENV variables live **as long as the container is alive** — just like `$HOME` lives as long as your terminal session is alive.

### ⚠️ Never Put Secrets in ENV

```dockerfile
# ❌ Bad — baked into image, anyone can see it
ENV DB_PASSWORD=mysecretpassword
```

Use `.env` files, Docker Secrets, or secret management tools instead.

---

## 8. Real Dockerfile Example — Apache on Ubuntu

```dockerfile
FROM ubuntu

RUN apt update -y
RUN apt install apache2 -y

COPY website/ /var/www/html/

RUN service apache2 restart   # ← redundant, has no effect at runtime

EXPOSE 80

CMD ["/usr/sbin/apachectl", "-D", "FOREGROUND"]
```

### Line by Line

| Line | What it does |
|---|---|
| `FROM ubuntu` | Start from clean Ubuntu OS |
| `RUN apt update -y` | Update package list, `-y` auto-confirms |
| `RUN apt install apache2 -y` | Install Apache web server |
| `COPY website/ /var/www/html/` | Copy your website into Apache's serving folder |
| `EXPOSE 80` | Document that container uses port 80 (default HTTP) |
| `CMD [...FOREGROUND]` | Start Apache and keep it running in foreground |

---

## 9. Processes, PID & Systemd — Deep Dive

### What is a PID?

Every process running on Linux gets a **Process ID (PID)** — a unique number.

```bash
ps aux
# PID 1 = systemd  ← always PID 1, the "mother of all processes"
# PID 500 = sshd
# PID 501 = apache2
```

**PID 1 is special** — it is the first process the kernel starts when Linux boots. Everything else is a child of PID 1.

### What is Systemd?

`systemd` is always PID 1 on modern Linux. Its job:

```
Machine boots
      ↓
Kernel starts systemd as PID 1
      ↓
systemd starts everything else:
    → networking, ssh, apache, database, your app...
```

`systemctl` is a tool to talk to systemd:

```bash
systemctl start apache2    # tell systemd → please start apache
systemctl enable apache2   # tell systemd → start apache on every boot
systemctl status apache2   # ask systemd  → is apache running?
```

Without systemd running, `systemctl` has nobody to talk to — it fails.

### Normal Linux Boot Process

```
Power ON → BIOS/UEFI → Bootloader (GRUB) → Linux Kernel → systemd (PID 1)
      → systemd reads config → starts all services → Machine is ready
```

### How Docker is Different

Docker does NOT boot a full OS. It skips everything and directly runs your process:

```
docker run my-app
      ↓
Docker creates a container
      ↓
Docker starts YOUR PROCESS as PID 1
      (apache, node, python — whatever CMD says)
      ↓
No BIOS, no bootloader, no kernel boot, NO systemd
```

```bash
# Inside a running docker container
docker exec -it my-app ps aux
# PID 1 = apache2   ← YOUR APP is PID 1, not systemd
```

### Normal Linux vs Docker

| Concept | Normal Linux | Docker |
|---|---|---|
| PID 1 | systemd | Your app (apache, node, etc.) |
| Starting services | `systemctl start` | `CMD` in Dockerfile |
| Auto-restart | `systemctl enable` | `--restart=always` |
| Multiple services | systemd manages all | One process per container |
| Boot time | Full OS boot | Milliseconds |
| Logs | `/var/log/` | `docker logs` |

---

## 10. Why `systemctl` Does NOT Work in Docker

```dockerfile
RUN systemctl start apache2    # ❌ FAILS
RUN systemctl enable apache2   # ❌ FAILS
```

Error:
```
System has not been booted with systemd as init system (PID 1)
```

Docker containers are lightweight — meant to run **one process only**. No need for a service manager.

---

## 11. Why FOREGROUND Matters

When a service runs normally:
```bash
systemctl start apache2
# apache starts in BACKGROUND, terminal returns immediately
```

But in Docker, if PID 1 runs in the **background**, Docker thinks nothing is running and kills the container:

```dockerfile
# ❌ Wrong — apache starts in background, container exits immediately
CMD ["service", "apache2", "start"]

# ✅ Correct — apache runs in foreground, container stays alive
CMD ["/usr/sbin/apachectl", "-D", "FOREGROUND"]
```

> **FOREGROUND = stay running, don't go to background = keeps PID 1 alive = keeps container alive**

---

## 12. Container Lifecycle = Process Lifecycle

```
Container is RUNNING  ←→  PID 1 process is RUNNING
Container is EXITED   ←→  PID 1 process has STOPPED
```

If your app crashes → PID 1 dies → container immediately exits.

### Docker Equivalent of `systemctl enable`

```bash
docker run --restart=always my-app       # always restart if stopped
docker run --restart=on-failure my-app   # restart only if crashed
docker run --restart=unless-stopped my-app # restart unless manually stopped
```

---

## 13. PID 1 Responsibilities in Docker

### Handling Signals
```
docker stop my-container
      ↓
Docker sends SIGTERM to PID 1
      ↓
PID 1 must handle it gracefully → shutdown cleanly
      ↓
If no response in 10s → Docker sends SIGKILL → force kill
```

### Zombie Processes
```
Normal Linux:  systemd (PID 1) → automatically cleans up dead child processes
Docker:        your app (PID 1) → may NOT clean up → zombie processes pile up
```

### Solution — Use tini (Minimal Init for Containers)

```dockerfile
FROM ubuntu
RUN apt install tini -y
ENTRYPOINT ["/usr/bin/tini", "--"]
CMD ["your-app"]
```

---

## 14. Starting an Exited Container

```bash
docker ps -a                    # show ALL containers including exited
docker start <container_id>     # start by ID
docker start my-app             # start by name
docker start -a my-app          # start and attach to logs
docker start -ai my-app         # start and attach interactively
```

### Container Command Reference

| Command | What it does |
|---|---|
| `docker run` | Creates a brand new container and starts it |
| `docker start` | Starts an already existing exited container |
| `docker stop` | Stops a running container (keeps it, just exits) |
| `docker rm` | Deletes the container permanently |

---

## 15. Quick Summary — One Liners

> **RUN** = build time. **CMD** = run time.

> **ENV** lives inside the container for as long as the container runs — like `$HOME` in your terminal.

> **"App"** = whatever technology runs in your container — Tomcat, Node, Apache, Python, etc.

> **systemctl won't work in Docker** — there's no systemd. Use CMD + --restart flags instead.

> **In Docker, your process IS the container** — when it dies, the container dies.

> **FOREGROUND** = keep process alive = keep container alive.



## Why `CMD ["/usr/sbin/apachectl", "-D", "FOREGROUND"]`?

Let's break it into 3 parts:

---

## Part 1 — Why `CMD`?

if we use RUN then apache start running but never stops so build will never finish

Because we want Apache to start **every time the container runs**, not during build time.

dockerfile

```dockerfile
RUN ...   # build time  → installs apache into the image
CMD ...   # run time    → actually STARTS apache when container launches
```

---

## Part 2 — Why `/usr/sbin/apachectl`?

`apachectl` is the **control script for Apache** — it's the proper way to start Apache.

bash

```bash
/usr/sbin/apachectl    # full path used because Docker doesn't load
                       # your normal $PATH like a terminal does
                       # so always use full paths in CMD
```

Just like you say:

bash

```bash
which apachectl        # → /usr/sbin/apachectl
```

---

## Part 3 — Why `-D FOREGROUND`? (Most Important)

This is the key part. Let's understand it step by step.

### Normally Apache starts in Background:

bash

```bash
apachectl start
# apache starts → goes to background → terminal returns
# apache is running silently behind the scenes
```

### But Docker works differently:

```
docker run my-app
      ↓
Container starts
      ↓
CMD runs → apache starts → goes to BACKGROUND
      ↓
Docker sees "nothing running in foreground"
      ↓
Docker thinks the process is done ❌
      ↓
Container EXITS immediately 💀
```

### `-D FOREGROUND` fixes this:

```
docker run my-app
      ↓
Container starts
      ↓
CMD runs → apache starts → stays in FOREGROUND
      ↓
Docker sees "process is still running"
      ↓
Container stays ALIVE ✅
      ↓
Apache keeps serving your website 🌐
```

---

## Simple Real Life Analogy

Think of it like a security guard:

```
Without FOREGROUND:
Guard comes → does his check → goes home
Building is now unprotected ❌

With FOREGROUND:
Guard comes → STAYS at the door all day
Building is protected ✅
```

---

## Why Array Syntax `["/usr/sbin/apachectl", "-D", "FOREGROUND"]`?

There are two ways to write CMD:

dockerfile

```dockerfile
# Shell form — runs via /bin/sh
CMD apachectl -D FOREGROUND

# Exec form (array) — runs directly, NO shell involved ✅ preferred
CMD ["/usr/sbin/apachectl", "-D", "FOREGROUND"]
```

Array form is preferred because:

- Runs the process **directly as PID 1**
- Properly receives **Docker stop signals** (SIGTERM)
- No unnecessary shell process in between