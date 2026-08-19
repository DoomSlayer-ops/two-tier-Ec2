# 🚀 CI/CD Automation for Dockerized 2-Tier Application

A complete **CI/CD pipeline for a Dockerized 2-tier Flask application**, automated using **GitHub Actions**, **Docker Hub**, and **AWS EC2**.

The application consists of a **Flask backend** connected to a **MySQL database**. Docker Compose manages the application containers, while GitHub Actions automatically builds, pushes, and deploys the application whenever changes are pushed to the `main` branch.

---

## 📌 Project Overview

This project demonstrates an automated DevOps workflow where application changes move from **source code to production deployment** without requiring manual rebuilding or redeployment.

Whenever code is pushed to the `main` branch:

1. GitHub Actions checks out the latest code.
2. A Docker image of the Flask application is built.
3. The image is pushed to Docker Hub.
4. GitHub Actions connects to the AWS EC2 instance using SSH.
5. The latest repository changes are pulled.
6. Docker Compose downloads the latest image.
7. The containers are recreated with the updated application.
8. Unused Docker images are automatically removed.

---

## 🏗️ Architecture

```text
Developer
    │
    │ git push
    ▼
GitHub Repository
    │
    ▼
GitHub Actions
    │
    ├── Build Docker Image
    │
    ├── Push Image to Docker Hub
    │
    ▼
Docker Hub
    │
    ▼
AWS EC2
    │
    ▼
Docker Compose
    │
    ├── Flask Application
    │       │
    │       ▼
    └──── MySQL Database
```

---

## 🔄 CI/CD Workflow

```text
Code Push
   │
   ▼
GitHub Actions Triggered
   │
   ▼
Checkout Source Code
   │
   ▼
Login to Docker Hub
   │
   ▼
Build Docker Image
   │
   ▼
Push Image to Docker Hub
   │
   ▼
SSH into AWS EC2
   │
   ▼
Pull Latest Git Changes
   │
   ▼
Pull Latest Docker Image
   │
   ▼
Docker Compose Redeployment
   │
   ▼
Application Updated
```

---

## 🛠️ Tech Stack

| Technology         | Purpose                          |
| ------------------ | -------------------------------- |
| **Python**         | Application programming language |
| **Flask**          | Backend web framework            |
| **MySQL**          | Database                         |
| **Docker**         | Application containerization     |
| **Docker Compose** | Multi-container orchestration    |
| **Docker Hub**     | Docker image registry            |
| **GitHub Actions** | CI/CD automation                 |
| **AWS EC2**        | Application hosting              |
| **Git & GitHub**   | Source code management           |

---

## 📂 Project Structure

```text
CI-CD-Automation-for-Dockerized-2-Tier-Application/
│
├── .github/
│   └── workflows/
│       └── deploy.yml
│
├── templates/
│
├── .dockerignore
├── .env
├── .gitignore
├── Dockerfile
├── app.py
├── docker-compose.yml
├── message.sql
├── requirements.txt
└── README.md
```

### Important Files

**`app.py`**

Contains the Flask application logic and handles communication with the MySQL database.

**`Dockerfile`**

Creates the Docker image for the Flask application using a Python 3.9 slim base image.

**`docker-compose.yml`**

Defines and manages the two application services:

```text
Flask Application
        │
        ▼
     MySQL
```

It also configures networking, environment variables, health checks, persistent database storage, and application startup dependencies.

**`.github/workflows/deploy.yml`**

Contains the GitHub Actions CI/CD pipeline responsible for automatically building and deploying the application.

**`message.sql`**

Initializes the MySQL database structure.

---

# 🐳 Docker Architecture

The application runs using two Docker containers.

### Flask Container

The Flask application runs on:

```text
Port 5000
```

Docker image:

```text
shreemantgit/flask:latest
```

### MySQL Container

The database uses:

```text
mysql:5.7
```

Both containers communicate using the Docker network:

```text
two-tier
```

---

## ❤️ Container Health Checks

The project includes health checks for both services.

### MySQL

Docker verifies that MySQL is responding before the Flask application starts.

```bash
mysqladmin ping
```

### Flask

The Flask container exposes a health endpoint:

```text
/health
```

Docker periodically checks:

```bash
curl -f http://localhost:5000/health
```

The Flask container starts only after the MySQL service becomes healthy.

---

# ⚙️ CI/CD Pipeline

The CI/CD workflow is located at:

```text
.github/workflows/deploy.yml
```

The workflow automatically runs whenever code is pushed to:

```text
main
```

---

## 1️⃣ Checkout Source Code

GitHub Actions retrieves the latest repository code.

```yaml
uses: actions/checkout@v4
```

---

## 2️⃣ Authenticate with Docker Hub

GitHub Actions logs in to Docker Hub using repository secrets.

Required secrets:

```text
DOCKERHUB_USERNAME
DOCKERHUB_TOKEN
```

---

## 3️⃣ Build Docker Image

The workflow builds the Flask Docker image using the project's `Dockerfile`.

The image repository used by this project is:

```text
shreemantgit/flask
```

---

## 4️⃣ Push Docker Images

Two image tags are pushed to Docker Hub.

### Latest tag

```text
shreemantgit/flask:latest
```

### Commit-specific tag

```text
shreemantgit/flask:<github-commit-sha>
```

Using the Git commit SHA creates a traceable image for each deployment.

---

## 5️⃣ Deploy to AWS EC2

After successfully building and pushing the image, GitHub Actions connects to the EC2 instance over SSH.

Required GitHub Secrets:

```text
EC2_HOST
EC2_USER
EC2_SSH_KEY
```

---

## 6️⃣ Update Application

Once connected to the EC2 instance, the deployment workflow performs:

```bash
cd two-tier-flask-app

git pull origin main

docker compose pull

docker compose up -d

docker image prune -f
```

This ensures the EC2 instance always runs the latest application version.

---

# 🔐 GitHub Actions Secrets

Configure the following secrets under:

```text
GitHub Repository
→ Settings
→ Secrets and variables
→ Actions
```

Add:

| Secret               | Description                            |
| -------------------- | -------------------------------------- |
| `DOCKERHUB_USERNAME` | Docker Hub username                    |
| `DOCKERHUB_TOKEN`    | Docker Hub access token                |
| `EC2_HOST`           | EC2 public IP address or hostname      |
| `EC2_USER`           | EC2 SSH username                       |
| `EC2_SSH_KEY`        | Private SSH key used to connect to EC2 |

> Never store passwords, tokens, or SSH private keys directly inside the GitHub repository.

---

# ☁️ AWS EC2 Setup

The deployment server needs:

```text
Git
Docker
Docker Compose
```

The EC2 Security Group should allow the required ports.

Typical configuration:

| Port   | Purpose           |
| ------ | ----------------- |
| `22`   | SSH               |
| `5000` | Flask application |

For production deployments, exposing the application through ports `80` or `443` using a reverse proxy is recommended.

---

# 🚀 Running the Project Locally

## 1. Clone the repository

```bash
git clone https://github.com/Shreemant-Acharya/CI-CD-Automation-for-Dockerized-2-Tier-Application.git
```

Navigate into the directory:

```bash
cd CI-CD-Automation-for-Dockerized-2-Tier-Application
```

---

## 2. Start the application

Using Docker Compose:

```bash
docker compose up -d
```

To rebuild the image locally:

```bash
docker compose up -d --build
```

---

## 3. Verify containers

```bash
docker ps
```

You should see:

```text
flask-app
mysql
```

---

## 4. Access the application

Open:

```text
http://localhost:5000
```

If running on EC2:

```text
http://<EC2-PUBLIC-IP>:5000
```

---

# 🐳 Running Without Docker Compose

The application can also be started manually.

## Build the Flask Image

```bash
docker build -t flaskapp .
```

---

## Create Docker Network

```bash
docker network create twotier
```

---

## Start MySQL

```bash
docker run -d \
  --name mysql \
  -v mysql-data:/var/lib/mysql \
  --network=twotier \
  -e MYSQL_DATABASE=mydb \
  -e MYSQL_ROOT_PASSWORD=admin \
  -p 3306:3306 \
  mysql:5.7
```

---

## Start Flask Application

```bash
docker run -d \
  --name flaskapp \
  --network=twotier \
  -e MYSQL_HOST=mysql \
  -e MYSQL_USER=root \
  -e MYSQL_PASSWORD=admin \
  -e MYSQL_DB=mydb \
  -p 5000:5000 \
  flaskapp:latest
```

---

# 🗄️ Database

The application stores submitted messages inside MySQL.

Example schema:

```sql
CREATE TABLE messages (
    id INT AUTO_INCREMENT PRIMARY KEY,
    message TEXT
);
```

The project's `message.sql` file is mounted into:

```text
/docker-entrypoint-initdb.d/
```

allowing MySQL to initialize the required database structure when the database is created.

---

# 🔁 Deployment Example

Suppose a change is made to:

```text
app.py
```

Commit and push:

```bash
git add .

git commit -m "Update Flask application"

git push origin main
```

GitHub Actions automatically performs:

```text
Push to main
        ↓
Build Docker Image
        ↓
Push Image to Docker Hub
        ↓
SSH into EC2
        ↓
Pull Latest Repository
        ↓
Pull Latest Docker Image
        ↓
Restart Containers
        ↓
Deployment Complete
```

No manual Docker build or deployment is required on the EC2 server.

---

# 🧹 Stop the Application

To stop and remove the containers:

```bash
docker compose down
```

To remove containers and associated volumes:

```bash
docker compose down -v
```

---

# 🎯 Key DevOps Concepts Demonstrated

This project demonstrates practical implementation of:

- Continuous Integration
- Continuous Deployment
- GitHub Actions workflows
- Docker image creation
- Docker image registries
- Docker Compose
- Multi-container applications
- Container networking
- Container health checks
- Persistent database storage
- Environment variables
- GitHub Secrets
- Automated EC2 deployment
- SSH-based deployment
- Docker image versioning using Git commit SHA

---

# 👨‍💻 Author

**Shreemant Acharya**

GitHub: `Shreemant-Acharya`

---

## ⭐ Project Summary

> Built an automated CI/CD pipeline for a Dockerized Flask and MySQL two-tier application using GitHub Actions, Docker Hub, Docker Compose, and AWS EC2. Every push to the main branch automatically builds and publishes a Docker image and redeploys the latest application version to EC2.
