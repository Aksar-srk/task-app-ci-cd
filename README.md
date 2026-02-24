# CI/CD Deployment — DevOps Assignment

## 👨‍💻 Candidate: Aksar Shaik
**Role:** DevOps Engineer Intern Assignment — Discover Dollar

---

## 🏗️ Architecture Overview

The following diagram illustrates the automated CI/CD flow from the developer's local machine to the AWS EC2 production environment.

```text
                ┌──────────────┐
                │   Developer  │
                │   (Git Push) │
                └──────┬───────┘
                       │
                       ▼
               ┌──────────────┐
               │   GitHub     │
               │   Repository │
               └──────┬───────┘
                      │ Webhook
                      ▼
               ┌──────────────┐
               │   Jenkins    │
               │   CI/CD      │
               └──────┬───────┘
                      │ Build & Push
                      ▼
               ┌──────────────┐
               │ Docker Hub   │
               │   Images     │
               └──────┬───────┘
                      │ Pull
                      ▼
        ┌────────────────────────────┐
        │        AWS EC2 VM          │
        │                            │
        │  ┌──────────────┐          │
        │  │ Docker Compose│         │
        │  └──────┬───────┘          │
        │         │                  │
        │ ┌───────▼───────┐         │
        │ │    Nginx       │  :80    │
        │ └──────┬────────┘         │
        │        │ Reverse Proxy     │
        │  ┌─────▼──────┐  ┌───────▼──────┐
        │  │ Frontend   │  │ Backend      │
        │  │ Angular    │  │ Node/Express │
        │  └────────────┘  └──────┬───────┘
        │                           │
        │                     ┌─────▼──────┐
        │                     │  MongoDB   │
        │                     └────────────┘
        └────────────────────────────┘

```
## 🛠️ Tech Stack

- **Frontend:** Angular  
- **Backend:** Node.js + Express  
- **Database:** MongoDB  
- **Containerization:** Docker  
- **Orchestration:** Docker Compose  
- **CI/CD:** Jenkins + GitHub Webhooks  
- **Reverse Proxy:** Nginx  
- **Cloud:** AWS EC2 (Ubuntu)

## 🚀 Step-by-Step Implementation

### 1️⃣ Repository Setup

The project is structured to separate concerns between frontend, backend, and infrastructure configuration.

#### 📁 Directory Structure

```bash
task-app-ci-cd/
 ├── frontend/           # Angular Application
 ├── backend/            # Node.js API
 ├── nginx/              # Nginx Configuration
 ├── docker-compose.yml  # Multi-container orchestration
 ├── Jenkinsfile         # CI/CD Pipeline script
 └── README.md
```
## 2️⃣ Dockerization

### 📍 Backend Dockerfile (`backend/Dockerfile`)
The backend service is containerized using Node.js to run the Express API.

### 📍 Frontend Dockerfile (`frontend/Dockerfile`)
The frontend uses a **multi-stage build** to keep the production image lightweight and optimized for deployment.

---

## 3️⃣ Docker Hub Integration

Docker images are tagged and pushed to Docker Hub for centralized storage and version management.

```bash
docker login
docker build -t aksarsr/dd-backend ./backend
docker build -t aksarsr/dd-frontend ./frontend
docker push aksarsr/dd-backend
docker push aksarsr/dd-frontend
```
## 4️⃣ Docker Compose Deployment

The `docker-compose.yml` file manages the lifecycle of all services, including:

- MongoDB database
- Backend API
- Frontend application
- Nginx reverse proxy

Docker Compose ensures that all containers run together within a single network, enabling seamless communication between services.

---

## 5️⃣ Nginx Reverse Proxy

Nginx is configured to route traffic to the appropriate container based on the URL path:

- `/` → Frontend (Angular application)
- `/api` → Backend (Node.js API)

This setup allows the entire application to be accessed through **port 80** using a single entry point.

## 6️⃣ CI/CD Pipeline (Jenkins)

The Jenkins pipeline automates the complete build and deployment workflow whenever code is pushed to GitHub.

### 🔄 Pipeline Stages

#### 🔹 Build
Create Docker images from the latest source code.

#### 🔹 Push
Upload Docker images to Docker Hub for centralized storage.

#### 🔹 Deploy
Pull the latest images on the EC2 instance and restart containers using Docker Compose.

---

## 🔐 Docker Hub Credentials Setup in Jenkins

To securely push images to Docker Hub:

1. Go to **Jenkins Dashboard → Manage Jenkins → Manage Credentials**
2. Select **Global credentials**
3. Click **Add Credentials**
4. Choose **Username with password**
5. Enter:
   - **Username:** Docker Hub username
   - **Password:** Docker Hub password or access token
   - **ID:** `dockerhub-creds`
6. Save

The Jenkins pipeline uses these credentials to authenticate during image push.

---

## 🔌 Required Jenkins Plugins

Install the following plugins for CI/CD functionality:

- **Docker Pipeline Plugin** — enables Docker commands in pipelines
- **Git Plugin** — allows Jenkins to pull code from GitHub
- **GitHub Integration Plugin** — enables webhook triggers
- **Pipeline Plugin** — required for Jenkinsfile pipelines
- **Pipeline Stage View Plugin** — visualizes pipeline stages

### 📊 Stage View

After installing the **Pipeline Stage View Plugin**, Jenkins displays a visual pipeline flow showing:

- Checkout
- Build
- Push
- Deploy

This helps monitor pipeline progress and debug failures quickly.

---

## 🔗 GitHub Webhook Configuration

To enable automatic pipeline triggers on code changes:

- **Payload URL:** `http://<EC2-IP>:8080/github-webhook/`
- **Content Type:** `application/json`
- **Trigger:** Push events

Once configured, every GitHub push triggers the Jenkins pipeline automatically.
