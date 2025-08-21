## 🛠️ Setting Up GitHub Actions Workflow
### ✅ Prerequisites
- Source code is available and committed in your GitHub repository.
- Basic familiarity with previous project workflows or CI/CD concepts.

### 🎯 Goal
- Create a GitHub Actions workflow that:
- Runs code quality checks using Maven, Checkstyle, and Sonar Scanner.
- Uploads the reports to SonarCloud.
- Validates against quality gates.

### 🧠 Key Concepts
| Jenkins  | GitHub Actions |
| -------- | -------------- |
| Pipeline | Workflow       |
| Stages   | Jobs           |
| Steps    | Steps          |

### Jenkins Vs Github Actions

| Feature                   | GitHub Actions                                  | Jenkins                                             |
| ------------------------- | ----------------------------------------------- | --------------------------------------------------- |
| **Setup**                 | Built-in, no server setup needed                | Requires self-hosted server or Jenkins Cloud        |
| **Integration**           | Native GitHub integration                       | Requires webhooks and plugins for GitHub            |
| **Maintenance**           | No maintenance—fully managed by GitHub          | Needs updates, plugin management, backups           |
| **Scalability**           | Automatically scales with GitHub-hosted runners | You manage scalability yourself                     |
| **Cost**                  | Free for public repos (limits apply to private) | Free, but infrastructure costs may apply            |
| **Security**              | GitHub handles runner security and updates      | You’re responsible for securing your Jenkins server |
| **UI/UX**                 | Clean, modern, GitHub-native                    | Outdated UI; steeper learning curve for beginners   |
| **Speed to Deploy**       | Quick to start and use                          | Takes time to install, configure, and test          |
| **Secrets Management**    | Built-in GitHub Secrets                         | Separate management, plugins may be needed          |
| **Community & Templates** | Marketplace for ready-to-use actions            | Plugin ecosystem, but sometimes less maintained     |



### 📁 GitHub Actions Setup
- Go to your repository → Actions tab.
- Click "Set up a workflow yourself" (skip templates).
- GitHub will create a folder structure:

```bash
.github/workflows/
```
- Add a .yml file (e.g. main.yml).

### 📝 Sample Workflow
- Here's a basic manual workflow to verify setup:

```yaml
name: HProfile Actions

on: workflow_dispatch  # Manual trigger

jobs:
  testing:
    runs-on: ubuntu-latest
    steps:
      - name: Testing Workflow
        run: echo "Workflow works!"
```

### 🖥️ Explanation:
- workflow_dispatch: Trigger workflow manually via GitHub UI(only run when click on run button).
- jobs: Defines a job named testing.
- runs-on: Uses a GitHub-hosted Ubuntu runner.
- steps: Executes a shell command.

### 🚀 Running the Workflow
- Commit and push the .yml file.
- Go to Actions → Click on your workflow → Run workflow → Confirm.
- View the logs under the job and verify the echo output.

### 🔄 Sync with VS Code
- Pull the latest changes to VS Code.
- You’ll see the new file at:

```bash
.github/workflows/main.yml
```

# GitHub Actions: Maven and Code Analysis Workflow

## Overview

We now understand how to trigger a GitHub Actions workflow and execute basic steps. Next, we’ll expand the workflow to:

- Clone the repository
- Run Maven tests
- Perform Checkstyle analysis
- Prepare for SonarCloud integration

> Refer to Github marketplace to know in details about the workflows
---

## Important Notes

- **GitHub Actions doesn’t clone the source code by default.**  
  You must manually include a step to checkout your code using an official GitHub Action.

---

## Updated Workflow Steps

### 1. Checkout Source Code

Use the official `actions/checkout@v4` action:

```yaml
- name: Checkout source code
  uses: actions/checkout@v4
```
### 2. Run Maven Tests
```yaml
- name: Run Maven Tests
  run: mvn test
```

### 3. Checkstyle Analysis
```yaml
- name: Run Checkstyle
  run: mvn checkstyle:checkstyle
```

## Pushing the Workflow
- After editing your workflow in VS Code:
- Save, Commit & Push to GitHub.
- Go to the Actions tab .Select your workflow (e.g., "HProfileActions")
- Click Run Workflow

> You should see the workflow execute the test and Checkstyle steps successfully.

# Setting Up SonarCloud Integration with GitHub Actions

## Prerequisites
- Sign up and log in to **SonarCloud** using your GitHub account.
- Create an **organization** and a **project** within SonarCloud.
- Generate a **SonarCloud token** for authentication.

---

## Step 1: Create Organization and Project on SonarCloud
- Click the **+** icon and select **Create new organization**.
- Provide a unique organization name (e.g., `hprofile`).
- Create a new **project** with a unique key (e.g., `actions-code`).
- Keep the project **public** to use the free plan.
- Complete project setup.

---

## Step 2: Generate SonarCloud Token
- Go to **My Account > Security** on SonarCloud.
- Generate a new token (name it e.g., `githubactions`).
- Copy the token securely as it will be used in GitHub Secrets.

---

## Step 3: Add SonarCloud Secrets to GitHub Repository
- In your GitHub repository, navigate to **Settings > Secrets and variables > Actions**.
- **NOTE**
- Add the following secrets:
    - `SONAR_URL` — SonarCloud URL (`https://sonarcloud.io`)
    - `SONAR_TOKEN` — The generated token from SonarCloud
    - `SONAR_ORGANIZATION` — Your SonarCloud organization key (e.g., `hprofile786`)
    - `SONAR_PROJECT_KEY` — Your SonarCloud project key (e.g., `actionscode`)

> **Note:** Use the organization and project keys, not display names, for these secrets.

---

## Step 4: Configure GitHub Actions Workflow
> Refer to Github Marketpkace for the following setups
### Java Setup
Use the official action to set up **Java 11** (required by SonarScanner):
```yaml
- name: Set up Java 11
  uses: actions/setup-java@v3
  with:
    distribution: 'temurin'
    java-version: '11'
```
## SonarScanner Setup
- Use a trusted SonarScanner CLI action (e.g., from warchant):
```yaml
name: Setup SonarScanner CLI
uses: warchant/sonar-scanner-action@v1
```

## Run SonarScanner
- Execute the scanner with parameters from your secrets (Get if from github marketplace):
```yaml
- name: Run SonarScanner
  run: |
    sonar-scanner \
      -Dsonar.host.url=${{ secrets.SONAR_URL }} \
      -Dsonar.login=${{ secrets.SONAR_TOKEN }} \
      -Dsonar.organization=${{ secrets.SONAR_ORGANIZATION }} \
      -Dsonar.projectKey=${{ secrets.SONAR_PROJECT_KEY }} \
      -Dsonar.sources=src/ \
      -Dsonar.java.binaries=target/classes \
      -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml
```

## Key Points
- GitHub Actions runners don’t include SonarScanner or Java by default, so you must install them in the workflow.
- Use GitHub Secrets to securely store sensitive data like tokens.
- The commands you automate in GitHub Actions mirror manual project steps.
- Understanding your project’s build and analysis commands is crucial before automation.
- SonarCloud integration helps maintain code quality by checking analysis results and quality gates.

## 🧩 Creating a Custom Quality Gate
> Gates created under organization level and configured under project level
### Step-by-step:
- Go to your SonarCloud Organization
- Click Quality Gates > Create
- Name it (e.g., ActionScript)
- Add a condition:
```text
Metric: Bugs
Operator: <
Value: 30
```
- Save and assign it to your project under Project > Administration > Quality Gate

## 🚦 Quality Gate Check in GitHub Actions
- Add to Workflow:
```yaml
- name: SonarCloud Quality Gate Check
  id: sonar_quality_gate
  uses: SonarSource/sonarcloud-github-action@master
  timeout-minutes: 5
  env:
  SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
  SONAR_HOST_URL: ${{ secrets.SONAR_URL }}
```
### 📝 Ensure variable names match the ones in your GitHub secrets.

## ✅ Test the Pipeline
- Commit & Push changes.
- Trigger the workflow via Actions > Run Workflow.
- The job should fail if the number of bugs exceeds the threshold (as designed).

- Check the result in:
- GitHub Actions log (look for "Quality Gate failed")
- SonarCloud project dashboard

## 🔄 Rollback Quality Gate
- To pass the workflow again:
- Either switch back to the default Sonar Way quality gate, or
- Modify the threshold in your custom gate (e.g., set bugs < 50)
- Re-run the workflow to confirm success.

# CI/CD Workflow Summary – GitHub Actions + AWS ECR/RDS Setup

## ✅ Completed: Code Testing & Analysis

The **first job** in the GitHub Actions workflow performs:

- Fetching source code
- Unit testing using **Maven**
- Code analysis with **SonarQube** via **SonarCloud**
- Quality gate checks via SonarCloud

✅ This concludes the CI stage.

---

## 🏗️ Next Step: Build & Push Docker Image to AWS ECR

### Objective

- Build a **Docker image** from the source code.
- Push the image to **Amazon ECR (Elastic Container Registry)**.

> ⚠️ Note: Although in a true GitOps project infrastructure would be provisioned using **Terraform**, here we are focusing only on the **CI/CD** portion. Setup will be **manual** for this project.

---

## 🛠️ AWS Setup (Manual)

### 1. Create IAM User (for GitHub Actions)

- Go to **IAM → Users → Create User**
- Attach the following policies:
  - `AmazonEC2ContainerRegistryFullAccess`
  - `AmazonECS_FullAccess`  
    ⚠️ Use **custom, minimal policies** in production environments.
- Generate **Access Key & Secret** and save them securely.

---

### 2. Create ECR Repository

- Go to **ECR → Repositories → Create repository**
- Set repository type to **private**
- Name it e.g., `pro-app-actions`
- GitHub Actions will handle the Docker push, so no need to use CLI push commands here.

---

### 3. Set Up RDS (MySQL)

- Go to **RDS → Create database (Standard)**
  - **Engine**: MySQL 8.0
  - **Template**: Free Tier
  - **DB Name**: `proapp-actions`
  - **Username**: `admin`, **Password**: auto-generate (save it!)
  - **Instance**: `db.t3.micro` (Free tier)
- Create a custom **Security Group**:
  - **Inbound Rule**: Port `3306` (MySQL)
  - Source: Allow access from EC2 instance (MySQL client)

---
### 4. SSH into the instance:

```bash
ssh -i downloads/mysql-client-key.pem ubuntu@<EC2_PUBLIC_IP>
````
### 5. Initialize the Database

- Launch a temporary **EC2 instance** (Ubuntu) to use as a **MySQL client**.
- Install MySQL client:
  ```bash
  sudo -i
   apt update &&  apt install mysql-client -y
  ```
- Connect to RDS:
```bash
mysql -h <RDS_ENDPOINT> -u admin -p accounts
````
- Clone your GitHub repository and run schema:

```bash
git clone <repo-url>
cd profile-project/src/main/resources
mysql -h <RDS_ENDPOINT> -u admin -p accounts < db_backup.sql
```
- Verify:

```sql
SHOW TABLES;
```
- ✅ Exit and terminate the EC2 instance.

### 🔐 Store AWS & DB Credentials in GitHub Secrets
- Go to:
- Repository → Settings → Secrets and variables → Actions
- Add the following secrets:

| Secret Name             | Description                         |
| ----------------------- | ----------------------------------- |
| `AWS_ACCESS_KEY_ID`     | IAM Access Key                      |
| `AWS_SECRET_ACCESS_KEY` | IAM Secret Access Key               |
| `AWS_ACCOUNT_ID`        | AWS Account ID                      |
| `AWS_REGISTRY`          | ECR Registry URL (ends with `.com`) |
| `RDS_USER`              | Database username (`admin`)         |
| `RDS_PASS`              | Database password                   |
| `RDS_ENDPOINT`          | RDS endpoint URL                    |

>💡 Use exact secret names as above to match the workflow, or update them accordingly in the pipeline config.

# 🐳 Build & Push Docker Image to AWS ECR – CI/CD Workflow

In this lecture, we will:

- Review the Dockerfile used for building the image.
- Set up a GitHub Actions job to build and push the Docker image to **AWS ECR**.
- Inject secrets into the application.
- Configure job dependencies.

---

## 📦 Dockerfile Overview

The project includes a **multi-stage Dockerfile**:

### 🔹 Stage 1: Build

- Uses `openjdk` base image.
- Installs Maven.
- Copies the full source code (`COPY ./ ./`).
- Runs:
```bash
  mvn install
```
- to generate the build artifact (.war file).

### 🔹 Stage 2: Package
- Uses Tomcat base image.
- Removes default ROOT app.
- Copies the generated profile-v2.war into webapps/ROOT.war.
- Exposes port 8080.
- Starts Tomcat with:
```text
catalina.sh run
```

### Configuration Injection
- 🔹 File: src/main/resources/application.properties
- The file contains placeholder DB credentials:
```properties
jdbc.username=db01
jdbc.password=admin123
```
- These will be replaced at runtime using sed with values stored in GitHub Secrets:
- RDS_USER ,RDS_PASS ,RDS_ENDPOINT

### ⚙️ GitHub Actions – Add Job to Build & Push Docker Image
📁 File: .github/workflows/main.yml
1. Define Environment Variable
- At the top of the file:
```yaml
env:
REGION: us-east-2  # Use your AWS region
```
2. Create New Job: build_and_publish
```yaml
build_and_publish:
needs: testing   # Ensures this job runs after 'testing' job
runs-on: ubuntu-latest
steps:
- name: Checkout code
uses: actions/checkout@v3

    - name: Update application.properties
      run: |
        sed -i "s/^jdbc.username.*/jdbc.username=${{ secrets.RDS_USER }}/" src/main/resources/application.properties
        sed -i "s/^jdbc.password.*/jdbc.password=${{ secrets.RDS_PASS }}/" src/main/resources/application.properties
        sed -i "s/^jdbc.url.*/jdbc.url=jdbc:mysql:\/\/${{ secrets.RDS_ENDPOINT }}:3306\/accounts/" src/main/resources/application.properties

    - name: Build & Push Docker image to AWS ECR
      uses: appleboy/docker-ecr-action@master
      with:
        access_key: ${{ secrets.AWS_ACCESS_KEY_ID }}
        secret_key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        registry: ${{ secrets.AWS_REGISTRY }}
        region: ${{ env.REGION }}
        repository: pro-app-actions   # Replace with your ECR repo name
        dockerfile: ./Dockerfile
        context: ./
        tags: latest,${{ github.run_number }}
```
### 🔁 Fix Job Parallelism with needs
- By default, all jobs run in parallel.
- To ensure that the build_and_publish job only runs after the testing job completes, add:
```yaml
needs: testing
to the build_and_publish job.
```

### 🚀 Run the Workflow
- Commit and push the workflow changes.
- Trigger the workflow manually or by push.

- Check GitHub Actions tab:

- Testing runs first , Once it passes, build_and_publish is triggered

### ✅ Verify Image in AWS ECR
- Go to AWS ECR Console.
- Open your repository (e.g., pro-app-actions).

- You should see new image tags:
> latest ; The build number (e.g., 8, 9, etc.)

# ECS Cluster Setup and Service Deployment

In this lecture, we will:

- Set up an **AWS Elastic Container Service (ECS)** cluster.
- Create a task definition for our container.
- Create an ECS service with a load balancer.
- Configure security groups and validate connectivity.

---

## Step 1: Create ECS Cluster

- Search for **Elastic Container Service** in AWS Console.
- Click **Create Cluster**.
- Choose **Networking only (AWS Fargate)**.
- Enter cluster name:  
  `vPro-app-act2`
- Make sure the region matches your ECR region (Ohio in this case).
- Add a tag:  
  Key: `Name`  
  Value: `vPro-app-act2`
- Click **Create**.  
  *(Pause if needed until creation completes.)*

---

## Step 2: Create Task Definition

- Go to **Task Definitions**.
- Click **Create New Task Definition**.
- Select **Fargate** launch type.
- Set task name:  
  `vpro-app-act-tdef`
- Set memory to **2 GB** (to save costs).
- Under **Task role**, select **Create new role**.
- Add container:
  - Container name: `vproapp`
  - Image URI:  
    Get this from your ECR repository via **View push commands** (exclude the `:latest` tag).
  - Container port: `8080`
- Enable **Enable logging** with **CloudWatch Logs** (important for troubleshooting).
- Add tag:  
  `app-act-dev`
- Click **Create**.

---

## Step 3: Attach CloudWatch Logs Policy to Task Role

- Open the task execution role in **IAM**.
- Check if the role has **CloudWatch Logs Full Access** policy.
- If not, **attach** the policy:
  - Search: `CloudWatch Logs Full Access`
  - Attach and save.

---

## Step 4: Create ECS Service

- Go to your **Cluster**.
- Click on **Services** > **Create**.
- Select launch type: **Fargate**.
- Service type: **Replica** (to run one or more copies).
- Choose your task definition and revision.
- Service name:  
  `vPro-app-act-svc`
- Number of tasks: `1`
- Deployment type: **Rolling update** (default)
- Deployment failure detection: **Uncheck** for now.
- Networking:
  - Keep subnet default.
  - Create new Security Group with rules: `vpro-app-act-svc-sg`
    - HTTP (port 80) from anywhere (0.0.0.0/0) — for load balancer.
    - Custom TCP (port 8080) from anywhere — for container communication.
- Load balancer:
  - Select **Application Load Balancer**.
  - Create new load balancer:
    - Name: `vpro-app-act-ELB`
    - Load balancer port: `80`
  - Create new target group:
    - Name: `vpro-app-act-TG`
    - Health check path: `/login` (matches your app's login endpoint)
- Add tag:  
  `vpro-app-act-svc`
- Click **Create**.

*(Pause if needed until service deployment stabilizes.)*

---

## Step 5: Verify Deployment

- Wait for service status to be **ACTIVE** and tasks running.
- Go to the load balancer details.
- Copy the **DNS name** (endpoint).
- Open the endpoint in your browser.
- Login with credentials:
  - Username: `admin_vp`
  - Password: `admin_vp`
- Confirm login and connectivity.

---

## Step 6: Update Security Groups for Database Access

- Go to **EC2** > **Security Groups** for your RDS instance.
- Edit **Inbound rules**:
  - Add rule:
    - Type: MySQL/Aurora
    - Port: 3306
    - Source: ECS service security group (allow ECS tasks to connect)
- Save.

Try logging in again.

---

## Troubleshooting

- If unable to login:
  - Check security group rules.
  - Make sure your database is initialized with the schema.
  - Confirm the DB name is `accounts`.
  - Use MySQL client on EC2 to run your DB backup SQL file again if needed.

---

## Next Steps

- We have an ECS cluster running with a service and load balancer.
- Next lecture will cover **deploying new Docker image tags automatically** on container updates.
- Stay tuned!

---

# 🚀 Deploying Updated Docker Images to ECS using GitHub Actions

In this lecture, we’ll extend our CI/CD pipeline by deploying the updated Docker image from AWS ECR to our ECS cluster.

---

## 🧠 Concept Recap

- **ECS Task Definitions**: Define container settings (image, tag, CPU/memory, etc.).
- **ECS Services**: Use task definitions to run containers and keep them healthy.
- **Deployment**: We update the task definition with a new image tag and deploy it using GitHub Actions.

---

## 📁 Step 1: Get the Task Definition JSON

1. Go to **ECS > Task Definitions**.
2. Click your task definition revision.
3. Click on the **JSON** tab.
4. Copy the entire JSON.
5. In your source code repo (VS Code), go to `-files/task-definition.json`:
  - Delete old content.
  - Paste the copied JSON.
  - Save the file.

---

## ⚙️ Step 2: Create the Deploy Job in GitHub Actions

### 🧩 Job Structure

We’ll create a new job in the workflow that:

- Depends on the `build-and-publish` job.
- Updates the task definition file with the new image tag.
- Pushes the new revision to ECS.
- Waits for the service to become stable.

### ✅ Job YAML Outline

```yaml
deploy:
  name: Deploy to ECS
  needs: build-and-publish
  runs-on: ubuntu-latest
  env:
    ECR_REPOSITORY: act-app
    SERVICE_NAME: vPro-app-act-svc-task1
    CLUSTER_NAME: vPro-app-act
    TASK_DEFINITION: -files/task-definition.json
    CONTAINER_NAME: vPro-app

  steps:
    - name: Checkout source
      uses: actions/checkout@v4

    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v4
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: us-east-2

    - name: Update ECS task definition
      id: task-def
      uses: aws-actions/amazon-ecs-render-task-definition@v1
      with:
        task-definition: ${{ env.TASK_DEFINITION }}
        container-name: ${{ env.CONTAINER_NAME }}
        image: ${{ secrets.REGISTRY_URI }}/${{ env.ECR_REPOSITORY }}:${{ github.run_number }}

    - name: Deploy to ECS service
      uses: aws-actions/amazon-ecs-deploy-task-definition@v1
      with:
        service: ${{ env.SERVICE_NAME }}
        cluster: ${{ env.CLUSTER_NAME }}
        task-definition: ${{ steps.task-def.outputs.task-definition }}
        wait-for-service-stability: true
```
## 📦 Step 3: Trigger Deployment on Code Push
- Modify your workflow trigger to allow automatic deployment on push:

```yaml
on:
  [workflow_dispatch, push]
```
- Now deployment will happen on every push as well as manually via GitHub UI.

## 📌 Step 4: Commit and Push Workflow Changes
- Save your workflow file.
- Commit with message:
```bash
git commit -am "Added deploy job to ECS in workflow"

Push:
git push
```
## 🚀 Step 5: Monitor the Deployment
- Go to GitHub Actions.
- View the running workflow.
- Watch as:
- The image is built and pushed to ECR.
- Task definition is updated.
- ECS service deploys the new task.
- Stability is confirmed via health checks.

## 🔎 Validation
- Open ECS > Cluster > Service > Tasks.
- Click the running task.
- Verify the image tag matches the github.run_number (e.g. 9).
- Confirm revision number increased.
- Check that task status is RUNNING and target is healthy.
- Visit your load balancer DNS to confirm the app works.

## 🧹 Cleanup (Optional but Recommended)
- To avoid unnecessary charges:
- 🔻 Delete ECS Resources
       Delete ECS Service:
- Go to your cluster > service.
- Choose Delete service > type delete.
- Delete ECS Cluster:
- Go to Clusters > choose yours > click Delete.
- Confirm with cluster name.

## 🔒 Delete IAM User
- Go to IAM > Users > select the user.
- Choose Delete user.

## 🧠 Recap
- Here’s what we achieved:
- Built a complete CI/CD pipeline using GitHub Actions.
- Used SonarCloud for code quality.
- Built and pushed Docker images to AWS ECR.
- Deployed to ECS Fargate via updated task definitions.
- Validated through health checks and load balancer.
- Triggered deployment on code push or manually.

## 🌱 What's Next?
- In future projects:
- Use Terraform for full infrastructure as code.
- Replace ECS with Kubernetes (EKS).
- Manage pipelines with GitOps principles.

> ✅ GitHub Actions is a powerful and widely used CI/CD tool. If you’ve used Jenkins before, this will feel intuitive. Mastering this flow prepares you for real-world production DevOps pipelines.
