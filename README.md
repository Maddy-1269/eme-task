# Task – Dockerized Flask Application with CI Pipeline

## Overview
This project is a **simple Python Flask application** that exposes HTTP endpoints, is containerized using **Docker**, orchestrated with **Docker Compose**, and automatically built and published using a **GitHub Actions CI pipeline**.

The goal of this project is to demonstrate:
- Environment-based configuration
- Docker containerization best practices
- CI automation
- Image publishing and verification

---

## Application Details

### Endpoints

| Endpoint | Description |
|--------|------------|
| `/` | Returns application name and listening port |
| `/health` | Returns application health status |

---

## Port Configuration

### What port the app listens on and why
- The application listens on **port 5000**
- The port is read from an environment variable (`PORT`), not hard-coded
- This makes the application:
  - Portable
  - Cloud-friendly
  - Compatible with Docker, CI pipelines, and orchestration tools

---

### Why the container port and host port are different
- Inside the container, the app listens on **port 5000**
- On the host machine, the port is mapped as **8000 → 5000**
- Docker allows these ports to be different (e.g., `8000:5000`) to:
  - Avoid host port conflicts
  - Run multiple containers using the same internal port
- In this project, they are the same for simplicity and clarity

---

### How Docker Compose handles this mapping
Docker Compose manages port mapping using the `ports` section:

```yaml
ports:
  - "8000:5000"

Docker Port Mapping:

HOST:8000 → CONTAINER:5000

Docker Compose automatically:
- Creates the container
- Applies the port mapping
- Exposes the application on the host machine

Docker Setup:

Dockerfile Highlights:
- Uses a lightweight Python base image
- Installs Flask and Gunicorn
- Uses Gunicorn instead of Flask’s development server
- Binds Gunicorn to the port provided via environment variables

Docker Compose:

Docker Compose is used for **local development and testing**.

Responsibilities:
- Build the Docker image
- Run the container
- Inject environment variables
- Map ports cleanly

> Docker Compose does **not** push images to Docker Hub.  
> Image pushing is handled by the CI pipeline.

```
## CI Pipeline (GitHub Actions)

The CI pipeline is triggered **automatically on every push to the `main` branch**.

### Pipeline Steps Explained

1. **Checkout Code**  
   Pulls the latest source code from the repository

2. **Set up Docker Build Environment**  
   Prepares Docker Buildx for image building

3. **Login to Docker Hub**  
   Authenticates using GitHub Secrets  
   Uses a Docker Hub access token with Read & Write permissions

4. **Build Docker Image**  
   Builds the Docker image from the Dockerfile  
   Tags it with the GitHub run number

5. **Tag Docker Image**  
   Adds an additional `latest` tag for convenience

6. **Push Docker Image**  
   Pushes both versioned and `latest` tags to Docker Hub

7. **Verification Step**  
   Runs the container  
   Calls `/health`  
   Fails the pipeline if the app is not healthy

8. **Cleanup**  
   Stops and removes the test container

---

## Image Tagging Strategy

Images are tagged as:
- `<run_number>` → unique, traceable build
- `latest` → most recent successful build

### Example


This ensures:
- Easy rollback
- Clear build history
- Production readiness

---

## Design Decisions

### Decision 1: Using Environment Variables for Port
**Why**
- Avoids hardcoding values
- Makes the app cloud-ready

**What I would do with more time**
- Add validation for additional environment variables
- Support default values for local development

---

### Decision 2: Using Gunicorn Instead of Flask Dev Server
**Why**
- Flask dev server is not production-ready
- Gunicorn provides:
  - Better performance
  - Proper worker management

**What I would do with more time**
- Tune Gunicorn workers based on CPU
- Add structured logging

---

### Decision 3: Using GitHub Actions Instead of Jenkins
**Why**
- Native GitHub integration
- No infrastructure management required
- Faster setup and maintenance

**What I would do with more time**
- Add branch-based environments
- Add security scanning for Docker images
- Add notifications (Slack / Email)

---

## What I Would Improve With More Time
- Add unit tests and a test stage in CI
- Add `.env.example` for developers
- Add multi-stage Docker builds
- Add a CD pipeline (deploy to EC2 or Kubernetes)
- Add monitoring and logging integration

---

## How to Run Locally

```bash
docker compose up --build

http://localhost:8000
http://localhost:8000/health

##Docker Hub Repository

https://hub.docker.com/r/manikantamaddy/eme-task

```
## Conclusion

## This project demonstrates a complete CI-ready containerized application, following best practices for:

- Configuration management
- Docker usage
- CI automation
- Verification and safety checks
