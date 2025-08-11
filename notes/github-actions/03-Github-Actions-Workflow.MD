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