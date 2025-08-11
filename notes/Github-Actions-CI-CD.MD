# 🚀 CI/CD with GitHub Actions – GitOps Step-by-Step Guide

Welcome to the project on implementing **CI/CD using GitHub Actions**. This is your first step in the journey toward **GitOps**.

GitHub Actions is a widely used CI/CD tool in the GitOps ecosystem, especially because it's built right into GitHub. In this project, you'll set up a CI/CD pipeline using GitHub Actions to build, test, and deploy a containerized application to **AWS ECS**, backed by **AWS RDS**, and exposed via a **load balancer**.

---

## 🧱 Project Architecture Overview

### 🔧 Tools & Services Used

| Component         | Purpose                                                   |
|-------------------|-----------------------------------------------------------|
| **GitHub**        | Code hosting and GitHub Actions for CI/CD                 |
| **VS Code**       | Local development environment                             |
| **Maven**         | Java build tool                                           |
| **Checkstyle**    | Code style checker                                        |
| **SonarQube**     | Static code analysis                                      |
| **SonarCloud**    | Cloud-based quality gate checks                           |
| **Docker**        | Containerization of application                           |
| **AWS ECR**       | Storing Docker images                                     |
| **AWS ECS**       | Running containers                                        |
| **Load Balancer** | Exposing application to the outside world                 |
| **Amazon RDS**    | Relational database for backend                           |

---

## 🔄 GitHub Actions Workflow Overview

### 1️⃣ Initial Setup

- Fork the GitHub repository.
- Set up SSH access between your local machine and GitHub.
- Connect repository to **VS Code** for development and git operations.

---

### 2️⃣ Workflow Breakdown

Each **GitHub Actions workflow** consists of:

- **Workflows** = CI/CD Pipelines
- **Jobs** = Logical units of work (e.g., test, build, deploy)
- **Steps** = Individual tasks in a job

---

### ✅ Job 1: Test Code

**Steps:**

1. Fetch source code from the GitHub repo.
2. Run `mvn test` to execute unit tests.
3. Run `Checkstyle` for code formatting checks.
4. Use `Sonar Scanner` to analyze code and upload results to **SonarCloud**.
5. Validate code via **SonarCloud quality gates**.

---

### 🐳 Job 2: Build and Push Docker Image

**Steps:**

1. Fetch source code again.
2. Build Docker image.
3. Push image to **AWS ECR**.

> 💡 Other registries like Docker Hub or Google Container Registry can also be used.

---

### 🚀 Job 3: Deploy to AWS ECS

**Steps:**

1. Create/Update ECS Task Definition with new Docker image tag.
2. Deploy service to **ECS Cluster**.
3. ECS pulls image from ECR and runs the container.
4. Connect container to **Amazon RDS**.
5. Expose application via **Load Balancer**.

---

## 🔁 End-to-End Flow

```text
Developer Pushes Code ➡️ GitHub Repository
                    ⬇️
GitHub Actions Workflow Triggered
   ⬇️                ⬇️                     ⬇️
 Test Code     ➡️  Build Docker Image  ➡️  Deploy to ECS
                        ⬇️                       ⬇️
                   Push to ECR           Pull Image, Deploy Container
                                                   ⬇️
                                   App Connects to RDS + Exposed via Load Balancer
```

