# 🚀 Spring Boot — CI/CD Pipeline with Docker, GitHub Actions & AWS

[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-4.1.0-brightgreen?logo=springboot&logoColor=white)](https://spring.io/projects/spring-boot)
[![Java](https://img.shields.io/badge/Java-21-orange?logo=openjdk&logoColor=white)](https://openjdk.org/)
[![Docker](https://img.shields.io/badge/Docker-Containerized-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)
[![GitHub Actions](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-2088FF?logo=githubactions&logoColor=white)](https://github.com/features/actions)
[![AWS](https://img.shields.io/badge/Deploy-AWS%20EC2-FF9900?logo=amazonaws&logoColor=white)](https://aws.amazon.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

> **End-to-end CI/CD pipeline** that automatically builds, containerizes, and deploys a Spring Boot application using **Docker**, **GitHub Actions**, and **AWS EC2** — from code push to production in minutes.

---

## 📑 Table of Contents

- [Overview](#-overview)
- [Architecture](#-architecture)
- [Tech Stack](#-tech-stack)
- [Prerequisites](#-prerequisites)
- [Getting Started](#-getting-started)
  - [Clone the Repository](#1-clone-the-repository)
  - [Build the Project](#2-build-the-project)
  - [Run Locally](#3-run-locally)
- [Docker Workflow](#-docker-workflow)
  - [Build Docker Image](#1-build-docker-image)
  - [Run Docker Container](#2-run-docker-container)
  - [Push to Docker Hub](#3-push-to-docker-hub)
- [CI/CD Pipeline](#-cicd-pipeline)
  - [Pipeline Stages](#pipeline-stages)
  - [GitHub Actions Workflow](#github-actions-workflow)
  - [Setting Up Secrets](#setting-up-secrets)
- [AWS Deployment](#-aws-deployment)
- [API Endpoints](#-api-endpoints)
- [Project Structure](#-project-structure)
- [Contributing](#-contributing)
- [License](#-license)

---

## 🔍 Overview

This project demonstrates a **production-grade CI/CD pipeline** built around a Spring Boot web application. Every push to the `main` branch triggers an automated workflow that:

1. **Builds** the application using Maven
2. **Runs** automated tests
3. **Packages** the app into a Docker image
4. **Pushes** the image to Docker Hub
5. **Deploys** to an AWS EC2 instance via SSH

The goal is to provide a hands-on, real-world reference for implementing continuous integration and continuous deployment from scratch.

---

## 🏗 Architecture

```
┌──────────────┐       ┌──────────────────┐       ┌────────────────┐       ┌──────────────────┐
│              │       │                  │       │                │       │                  │
│  Developer   │──────▶│  GitHub (Push)   │──────▶│ GitHub Actions │──────▶│    Docker Hub     │
│  Workstation │       │  Repository      │       │   CI/CD        │       │   (Image Repo)   │
│              │       │                  │       │                │       │                  │
└──────────────┘       └──────────────────┘       └───────┬────────┘       └────────┬─────────┘
                                                          │                         │
                                                          │   SSH Deploy            │  Docker Pull
                                                          ▼                         ▼
                                                  ┌───────────────────────────────────────────┐
                                                  │                                           │
                                                  │           AWS EC2 Instance                │
                                                  │     ┌─────────────────────────┐           │
                                                  │     │   Docker Container      │           │
                                                  │     │   (Spring Boot App)     │           │
                                                  │     │   Port 8081             │           │
                                                  │     └─────────────────────────┘           │
                                                  │                                           │
                                                  └───────────────────────────────────────────┘
```

---

## 🛠 Tech Stack

| Layer              | Technology                          |
|--------------------|--------------------------------------|
| **Language**       | Java 21                              |
| **Framework**      | Spring Boot 4.1.0                    |
| **Template Engine**| Thymeleaf                            |
| **Build Tool**     | Maven (with Maven Wrapper)           |
| **Containerization** | Docker (Eclipse Temurin 21 JRE)   |
| **CI/CD**          | GitHub Actions                       |
| **Registry**       | Docker Hub                           |
| **Cloud Provider** | AWS EC2                              |

---

## ✅ Prerequisites

Before getting started, make sure you have the following installed:

- **Java 21** (JDK) — [Download](https://adoptium.net/)
- **Maven 3.9+** — [Download](https://maven.apache.org/download.cgi) _(or use the included Maven Wrapper)_
- **Docker** — [Install Docker](https://docs.docker.com/get-docker/)
- **Git** — [Install Git](https://git-scm.com/downloads)
- **Docker Hub Account** — [Sign Up](https://hub.docker.com/)
- **AWS Account** _(for cloud deployment)_ — [Sign Up](https://aws.amazon.com/)

---

## 🚀 Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/abhnvkanth/CI-CD-Pipeline.git
cd CI-CD-Pipeline
```

### 2. Build the Project

Using the Maven Wrapper (no Maven installation required):

```bash
# Linux / macOS
./mvnw clean package -DskipTests

# Windows
mvnw.cmd clean package -DskipTests
```

This generates a JAR file in the `target/` directory.

### 3. Run Locally

```bash
java -jar target/spring-boot-app-0.0.1-SNAPSHOT.jar
```

The application starts on **http://localhost:8081**

---

## 🐳 Docker Workflow

### Dockerfile

```dockerfile
FROM eclipse-temurin:21-jre

WORKDIR /app

COPY target/*.jar app.jar

EXPOSE 8081

ENTRYPOINT ["java", "-jar", "app.jar"]
```

### 1. Build Docker Image

```bash
docker build -t spring-boot-app .
```

### 2. Run Docker Container

```bash
docker run -d -p 8081:8081 --name spring-app spring-boot-app
```

Verify: **http://localhost:8081/api/welcome**

### 3. Push to Docker Hub

```bash
# Log in to Docker Hub
docker login

# Tag the image
docker tag spring-boot-app <your-dockerhub-username>/spring-boot-app:latest

# Push
docker push <your-dockerhub-username>/spring-boot-app:latest
```

---

## ⚙ CI/CD Pipeline

### Pipeline Stages

```
  Push to main
       │
       ▼
 ┌─────────────┐    ┌──────────────┐    ┌──────────────┐    ┌───────────────┐    ┌──────────────┐
 │  Checkout    │───▶│  Build &     │───▶│  Build       │───▶│  Push to      │───▶│  Deploy to   │
 │  Code        │    │  Test (Maven)│    │  Docker Image│    │  Docker Hub   │    │  AWS EC2     │
 └─────────────┘    └──────────────┘    └──────────────┘    └───────────────┘    └──────────────┘
```

### GitHub Actions Workflow

Create `.github/workflows/ci-cd.yml` in your repository:

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      # Step 1 — Checkout source code
      - name: Checkout Code
        uses: actions/checkout@v4

      # Step 2 — Set up Java 21
      - name: Set up JDK 21
        uses: actions/setup-java@v4
        with:
          java-version: '21'
          distribution: 'temurin'

      # Step 3 — Build with Maven
      - name: Build with Maven
        run: mvn clean package -DskipTests

      # Step 4 — Log in to Docker Hub
      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      # Step 5 — Build & Push Docker Image
      - name: Build and Push Docker Image
        uses: docker/build-push-action@v6
        with:
          context: .
          push: true
          tags: ${{ secrets.DOCKERHUB_USERNAME }}/spring-boot-app:latest

      # Step 6 — Deploy to AWS EC2
      - name: Deploy to AWS EC2
        uses: appleboy/ssh-action@v1
        with:
          host: ${{ secrets.EC2_HOST }}
          username: ${{ secrets.EC2_USERNAME }}
          key: ${{ secrets.EC2_SSH_KEY }}
          script: |
            docker pull ${{ secrets.DOCKERHUB_USERNAME }}/spring-boot-app:latest
            docker stop spring-app || true
            docker rm spring-app || true
            docker run -d -p 8081:8081 --name spring-app ${{ secrets.DOCKERHUB_USERNAME }}/spring-boot-app:latest
```

### Setting Up Secrets

Navigate to your GitHub repository → **Settings** → **Secrets and variables** → **Actions**, and add:

| Secret Name           | Description                                  |
|-----------------------|----------------------------------------------|
| `DOCKERHUB_USERNAME`  | Your Docker Hub username                     |
| `DOCKERHUB_TOKEN`     | Docker Hub access token (not password)       |
| `EC2_HOST`            | Public IP or DNS of your AWS EC2 instance    |
| `EC2_USERNAME`        | SSH username (e.g., `ubuntu` or `ec2-user`)  |
| `EC2_SSH_KEY`         | Private SSH key (`.pem` file contents)       |

---

## ☁ AWS Deployment

### Setting Up Your EC2 Instance

1. **Launch an EC2 Instance** — Choose Ubuntu 22.04 LTS or Amazon Linux 2
2. **Configure Security Group** — Open ports:
   - `22` (SSH)
   - `8081` (Application)
3. **Install Docker on EC2:**

```bash
# Update system packages
sudo apt update && sudo apt upgrade -y

# Install Docker
sudo apt install docker.io -y

# Start and enable Docker
sudo systemctl start docker
sudo systemctl enable docker

# Add user to Docker group (avoids needing sudo)
sudo usermod -aG docker $USER

# Verify installation
docker --version
```

4. **Pull and Run the Image:**

```bash
docker pull <your-dockerhub-username>/spring-boot-app:latest
docker run -d -p 8081:8081 --name spring-app <your-dockerhub-username>/spring-boot-app:latest
```

5. **Access the App:**  
   `http://<your-ec2-public-ip>:8081/api/welcome`

---

## 📡 API Endpoints

| Method | Endpoint         | Description                     |
|--------|------------------|---------------------------------|
| `GET`  | `/api/welcome`   | Returns a welcome message       |

**Example Response:**

```
Welcome to spring boot app
```

---

## 📂 Project Structure

```
spring-boot-app/
├── .github/
│   └── workflows/
│       └── ci-cd.yml                  # GitHub Actions CI/CD workflow
├── src/
│   ├── main/
│   │   ├── java/com/substring/spring_boot_app/
│   │   │   ├── SpringBootAppApplication.java    # Main application entry point
│   │   │   └── Controller/
│   │   │       └── WelcomeController.java       # REST API controller
│   │   └── resources/
│   │       ├── application.properties           # App configuration
│   │       ├── static/                          # Static assets
│   │       └── templates/                       # Thymeleaf templates
│   └── test/                                    # Unit & integration tests
├── Dockerfile                                   # Docker build instructions
├── pom.xml                                      # Maven project configuration
├── mvnw / mvnw.cmd                              # Maven Wrapper scripts
└── README.md                                    # Project documentation
```

---

## 🤝 Contributing

Contributions are welcome! Follow these steps:

1. **Fork** the repository
2. **Create** a feature branch
   ```bash
   git checkout -b feature/amazing-feature
   ```
3. **Commit** your changes
   ```bash
   git commit -m "feat: add amazing feature"
   ```
4. **Push** to the branch
   ```bash
   git push origin feature/amazing-feature
   ```
5. **Open** a Pull Request

---

## 📄 License

This project is licensed under the **MIT License**. See the [LICENSE](LICENSE) file for details.

---

<div align="center">

**⭐ If you found this project helpful, please give it a star!**

Made with ❤️ by [Abhinav Kanth](https://github.com/abhnvkanth)

</div>
