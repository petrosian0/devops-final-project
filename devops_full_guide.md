# DevOps Deployment Guide

## Phase 2: Create Minimal Web Service (API)

### Create a folder for your project:
```bash
mkdir health-api
cd health-api
```

### Initialize Node.js project:
```bash
npm init -y
npm install express
```

### Create `index.js`:
```javascript
const express = require("express");
const app = express();

const port = process.env.PORT || 3000;

app.get("/", (req, res) => {
  res.json({
    status: "healthy",
    student_name: "Your Name",
    server_time: new Date().toISOString(),
    version: "1.0.0"
  });
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```

### Test locally:
```bash
node index.js
curl http://localhost:3000
```

## Phase 3: Dockerize the API

### Create `Dockerfile`:
```dockerfile
# Use Node Alpine image for small size
FROM node:20-alpine

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .

EXPOSE 3000
CMD ["node", "index.js"]
```

### Create `docker-compose.yml`:
```yaml
services:
  api:
    build: .
    container_name: health-api
    ports:
      - "80:3000"
    restart: always
```

### Build and run locally:
```bash
docker compose up --build
curl http://localhost
```

## Phase 4: VPS Setup (Ubuntu/DigitalOcean)

### 1️⃣ Create deploy user
```bash
adduser deploy
usermod -aG sudo deploy
```

### 2️⃣ SSH hardening
Edit `/etc/ssh/sshd_config`:
```
PermitRootLogin no
PasswordAuthentication no
```
```bash
systemctl restart sshd
```
Add your deploy SSH public key to `~/.ssh/authorized_keys` for deploy user.

### 3️⃣ Install Docker + Docker Compose
```bash
sudo apt update
sudo apt install -y docker.io docker-compose-plugin
sudo usermod -aG docker deploy
```
Log out and back in for deploy group to take effect.

### 4️⃣ Firewall (UFW)
```bash
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw enable
sudo ufw status
```

## Phase 5: GitHub Actions CI/CD

### 1️⃣ Generate dedicated deploy key
```bash
ssh-keygen -t ed25519 -f ~/.ssh/github_actions_deploy -C "github-actions-deploy" -N ""
cat ~/.ssh/github_actions_deploy.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
chmod 600 ~/.ssh/github_actions_deploy
chmod 700 ~/.ssh
```

### 2️⃣ Add private key to GitHub Secrets
- Name: `VPS_SSH_KEY`
- Value: full contents of `~/.ssh/github_actions_deploy` (private key)

Also add:
- `VPS_HOST` → VPS IP (e.g., `164.92.180.14`)
- `VPS_USER` → `deploy`

### 3️⃣ Create workflow `.github/workflows/deploy.yml`
```yaml
name: Deploy API to VPS

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: ubuntu-droplet

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Deploy via SSH
        uses: appleboy/ssh-action@v1.0.3
        with:
          host: ${{ secrets.VPS_HOST }}
          username: ${{ secrets.VPS_USER }}
          key: ${{ secrets.VPS_SSH_KEY }}
          port: 22
          script: |
            cd ~/devops-final-project
            git pull origin main
            docker compose down || true
            docker compose up -d --build
```

### 4️⃣ Test manually (optional)
```bash
ssh -i ~/.ssh/github_actions_deploy deploy@164.92.180.14
```
Should log in without password.

## Phase 6: Push and Deploy

Push code to GitHub from your PC:
```bash
git add .
git commit -m "Initial API + Docker setup"
git push origin main
```
GitHub Actions will trigger automatically → pull latest code on VPS → rebuild Docker → run API.

## Phase 7: Verify Deployment

On VPS:
```bash
docker ps
curl http://localhost
```

From browser:
```
http://164.92.180.14
```
Should return JSON:
```json
{
  "status": "healthy",
  "student_name": "Your Name",
  "server_time": "2025-12-29T00:00:00Z",
  "version": "1.0.0"
}
```
<img width="1917" height="811" alt="image" src="https://github.com/user-attachments/assets/0e70d4db-dada-472c-8cd0-adc22dc73a42" />

