# 🚀 CI/CD Pipeline with Jenkins, Docker & GitHub Webhook

## 📌 Overview

This project demonstrates a complete **CI/CD pipeline** using:

* Jenkins (Pipeline as Code)
* Docker (Build & Deployment)
* GitHub (Source Code)
* Webhooks (Auto Trigger)
* Ngrok (Expose local Jenkins)

Whenever code is pushed to GitHub, Jenkins automatically:

1. Pulls latest code
2. Builds Docker image
3. Pushes image to Docker Hub
4. Deploys container

---

## 🏗️ Architecture

```
GitHub (Push)
     │
     ▼
Webhook (ngrok URL)
     │
     ▼
Jenkins (localhost:8080)
     │
     ▼
Pipeline Execution
     │
     ├── Checkout Code
     ├── Build Docker Image
     ├── Push to Docker Hub
     └── Run Container
```

---

## ⚙️ Tech Stack

* CI/CD: Jenkins
* Containerization: Docker
* Repository: GitHub
* Tunneling: ngrok
* Language: Python (Flask App)

---

## 🔧 Jenkins Pipeline Stages

1. **Checkout Code**
2. **Build Docker Image**
3. **Tag Image**
4. **Login to Docker Hub**
5. **Push Image**
6. **Stop Old Container**
7. **Run New Container**
8. **Verify Deployment**

---

## 🔑 Prerequisites

* Jenkins installed (Docker container)
* Docker installed & Docker socket mounted
* GitHub repository
* Docker Hub account
* Ngrok installed

---

## 🔌 Jenkins Setup

### 1. Run Jenkins with Docker Socket

```bash
docker run -d \
  -p 8080:8080 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts
```

---

### 2. Configure Jenkins Job

* Create Pipeline Job
* Select: **Pipeline script from SCM**
* SCM: Git
* Repo: `https://github.com/himwasnik/todo-app.git`
* Branch: `*/master`

Enable:

* ✅ GitHub hook trigger for GITScm polling

---

### 3. Add Docker Hub Credentials

* Go to Jenkins → Credentials
* Add:

  * ID: `dockerhub-creds`
  * Username: Docker Hub username
  * Password: Access Token

---

## 🌐 Webhook Setup

### 1. Start ngrok

```bash
ngrok http 8080
```

---

### 2. Add Webhook in GitHub

Go to:
GitHub → Repo → Settings → Webhooks

* Payload URL:

```
https://<ngrok-url>/github-webhook/
```

* Content Type:

```
application/json
```

* Events:

```
Just the push event
```

---

## 🐳 Docker Flow

* Build image:

```
docker build -t todo-app .
```

* Tag image:

```
docker tag todo-app himwasnik/todo-app:latest
```

* Push image:

```
docker push himwasnik/todo-app:latest
```

---

## 🚀 Deployment

Container runs on:

```
http://localhost:8000
```

---

## 🧪 Testing Webhook

Trigger pipeline manually:

```bash
git commit --allow-empty -m "trigger webhook"
git push origin master
```

---

## 📊 Outcome

✔ Fully automated CI/CD pipeline
✔ Auto build & deployment on code push
✔ Dockerized application
✔ Real-time webhook triggering

---

## 🔥 Future Improvements

* Use versioned Docker tags (`BUILD_NUMBER`)
* Deploy to Kubernetes
* Add health checks
* Integrate Slack notifications
* Implement rollback strategy

---
