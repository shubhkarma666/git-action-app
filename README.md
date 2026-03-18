# 🚀 GitHub CI/CD Pipeline Setup (Self-Hosted Runner + SonarQube)

This guide sets up:
- Self-hosted GitHub Runner  
- SonarQube for code quality  
- Docker environment  

---

## 📌 Prerequisites

- Instance: Minimum **2 CPU & 4GB RAM**  
  Example: `t2.medium` or `c7i-flex.large`  
- OS: Ubuntu / Linux  

---

## ⚙️ Update System

```bash
sudo apt update
```

---

## 🖥️ Setup Self-Hosted GitHub Runner

1. Go to:
   ```
   GitHub → Repo → Settings → Actions → Runners → New self-hosted runner
   ```

2. Select:
   - OS: Linux  
   - Architecture: x64  

3. On your AWS instance:
   - Copy and run all commands provided by GitHub  

4. Configuration prompts:
   - Runner group → press **Enter (default)**  
   - Runner name → e.g., `Runner-1`  
   - Labels → keep `self-hosted`  
   - Work directory → `_work` (default)  

✅ Runner is now ready

---

## 🐳 Setup SonarQube Server (2nd Instance)

### Install Docker

```bash
sudo apt update
sudo apt install ca-certificates curl -y

sudo install -m 0755 -d /etc/apt/keyrings

sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc

sudo chmod a+r /etc/apt/keyrings/docker.asc

echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update

sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

---

### ▶️ Start Docker

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

Check:

```bash
sudo systemctl status docker
```

---

### ⚠️ Fix Docker Permission Issue

```bash
sudo usermod -aG docker ubuntu
newgrp docker
```

Test:

```bash
docker ps
```

---

## 📦 Run SonarQube

```bash
docker run -d -p 9000:9000 --name sonar sonarqube:lts-community
```

Access:
```
http://<instance-public-ip>:9000
```

Default login:
- Username: `admin`  
- Password: `admin`  

---

## 🔑 Generate SonarQube Token

1. Go to:
   ```
   Administration → Security → Users → Tokens
   ```
2. Generate token → copy it  

---

## 🔐 Configure GitHub Secrets & Variables

Go to:
```
GitHub → Repo → Settings → Secrets and Variables
```

### Add Secrets

```
SONAR_TOKEN = <your-sonar-token>
```

### Add Variables

```
SONAR_HOST_URL = http://<instance-ip>:9000
```

---

## 🎉 Done

- ✅ Self-hosted runner ready  
- ✅ SonarQube running  
- ✅ GitHub secrets configured  

Your CI/CD pipeline is now ready 🚀
