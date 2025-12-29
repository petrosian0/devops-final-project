# DevOps Final Project: Health API

## Project Description
This project is a minimal web service (API) that provides a health check endpoint.  
It returns a JSON response with server status, student name, current server time, and version.  
The goal is to demonstrate full DevOps workflow: API development, Dockerization, VPS deployment, and CI/CD automation using GitHub Actions.

## Architecture Overview
- **Node.js + Express**: for the API backend.
- **Docker & Docker Compose**: containerize the API for reproducible deployment.
- **Ubuntu VPS (DigitalOcean)**: host the Dockerized application.
- **UFW firewall**: basic VPS security.
- **GitHub Actions**: CI/CD pipeline to automatically deploy changes from `main` branch to the VPS.
- **SSH keys**: secure authentication for automated deployment.

## Prerequisites
To run this project locally or deploy it, you need:
- Node.js and npm installed (for local development)
- Docker and Docker Compose installed
- Git installed
- GitHub account with repository access
- Access to the VPS server with SSH key authentication
- Basic knowledge of CLI commands

## Demo / test
Public IP:
```bash
http://164.92.180.14
```
Expected output:
```json
{
  "status": "healthy",
  "student_name": "Petar Simic",
  "server_time": "2025-12-29T01:24:29.570Z",
  "version": "1.0.0"
}
```

## How to Use the Project

### Run Locally
1. Clone the repository:  
```bash
git clone https://github.com/petrosian0/devops-final-project.git
```

2. Change directory
```bash
cd devops-final-project
```

3. Start docker
```bash 
docker compose up --build
```