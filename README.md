
# Health API – DevOps Project

## Project Description
This project is a minimal REST API that exposes a health-check endpoint.
It is containerized using Docker and automatically deployed to a VPS
using GitHub Actions CI/CD.

## Architecture Overview
- Node.js + Express – API
- Docker & Docker Compose – Containerization
- Ubuntu VPS on DigitalOcean
- GitHub Actions – CI/CD pipeline
- SSH + UFW – Server security

## Prerequisites
- Docker & Docker Compose
- Node.js (for local dev)
- GitHub account
- Ubuntu VPS

## Local Run
```bash
docker compose up --build