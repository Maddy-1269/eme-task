# EME Task – Dockerized Flask Application with CI Pipeline

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
- On the host machine, the port is mapped as **5000 → 5000**
- Docker allows these ports to be different (e.g., `8080:5000`) to:
  - Avoid host port conflicts
  - Run multiple containers using the same internal port
- In this project, they are the same for simplicity and clarity

---

### How Docker Compose handles this mapping
Docker Compose manages port mapping using the `ports` section:

```yaml
ports:
  - "5000:5000"

