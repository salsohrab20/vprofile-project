# 🌐 Account Setup for DevOps Projects

This guide will walk you through creating and setting up accounts required for your CI/CD and DevOps pipeline projects:

- [x] GitHub
- [x] Domain Purchase (via GoDaddy)
- [x] Docker Hub
- [x] SonarCloud

---

## 🐙 1. Create a GitHub Account

1. Go to [https://github.com](https://github.com)
2. Sign up with:
    - Username
    - Email address
    - Password
3. Confirm your email from your inbox.
4. Complete the setup:
    - Choose your role (e.g., `Teacher`, `Software Engineer`)
    - Select your programming experience
    - Use case: _Learn Git and GitHub_, _Host projects_
5. Choose the **Free Plan**

✅ GitHub account setup complete!

---

## 🌍 2. Purchase a Domain (Optional but Recommended)

We will use a custom domain for real-world use cases.

### Provider: GoDaddy

1. Go to [https://www.godaddy.com](https://www.godaddy.com)
2. Create or log in to your account
3. Search for a cheap domain:
    - Use less popular TLDs like `.xyz` for lower prices
    - Example: `kinfo.xyz`
4. Add the domain to cart
5. Update settings:
    - Set duration to **1 year**
    - Skip add-ons like full domain protection or GoDaddy hosting
    - Select **Free privacy protection**
6. Proceed to checkout and pay

💡 `.xyz` domains are cheaper and sufficient for learning/demo projects.

---

## 🐳 3. Create an Account on Docker Hub

Docker Hub is where we will store our Docker images.

1. Go to [https://hub.docker.com](https://hub.docker.com)
2. Click **Sign Up**
3. Enter:
    - Username
    - Email
    - Password
4. Choose the **Free Plan**
5. Confirm your email address via inbox

✅ Docker Hub account setup complete!

---

## ☁️ 4. Create a SonarCloud Account

SonarCloud is used for code quality analysis.

1. Go to [https://sonarcloud.io](https://sonarcloud.io)
2. Click **Sign Up**
3. Choose **Sign up with GitHub**
4. Authorize SonarCloud to access your GitHub account
5. SonarCloud will:
    - Automatically link with your GitHub account
    - Ask you to create an organization
    - Let you set up projects for code analysis

✅ SonarCloud account and organization setup complete!

---

## ✅ Summary of Accounts Created

| Platform     | Purpose                                  |
|--------------|-------------------------------------------|
| GitHub       | Code repository + GitHub Actions CI/CD   |
| GoDaddy      | Custom domain for production scenarios   |
| Docker Hub   | Store and manage Docker images           |
| SonarCloud   | Code quality and static analysis         |

---
# ☁️ AWS Account Setup Guide (Free Tier + Security + Essentials)

In this guide, you will:

- Create an AWS Free Tier account
- Set up Multi-Factor Authentication (MFA)
- Create an IAM user (secure, limited access)
- Set billing alerts (to avoid surprise charges)
- Create an HTTPS certificate via ACM
- Understand IAM login URLs and aliasing

---

## 1️⃣ Create AWS Free Tier Account

1. Go to [AWS Free Tier Signup](https://aws.amazon.com/free/)
2. Click **Create a Free Account**
3. Provide:
    - **Email**
    - **Account name**
    - **Secure password**
4. Select: `Personal` account type
5. Provide contact details and **payment method** (debit/credit card):
    - ₹2 (INR) or $1 (USD) will be temporarily charged for verification
6. Verify your **phone number** via OTP
7. Choose the **Basic Support Plan**
8. Complete sign-up and go to [AWS Console](https://console.aws.amazon.com/)

✅ Account created!

---

## 2️⃣ Verify Activation

- Navigate to `EC2` from AWS Console.
- If you see:
    - Instances: 0
    - No errors
    - Your dashboard is visible

✅ Your account is **active**

---

## 3️⃣ Enable MFA (Multi-Factor Authentication) for Root User

1. Go to **IAM** service
2. Under **Security Recommendations**, click `Assign MFA to Root User`
3. Select `Virtual MFA Device`
4. Use the **Google Authenticator** app:
    - Install on mobile
    - Scan QR code
    - Enter 2 consecutive 6-digit codes
5. Save

✅ Root account now has MFA

---

## 4️⃣ Create IAM Admin User (Avoid using Root!)

1. Go to **IAM > Users**
2. Click `Add User`
3. Username: `admin`
4. Enable: `Provide user access to the AWS Management Console`
5. Select: `Auto-generate password`
6. Enable: `User must change password at next sign-in`
7. Attach permission policy:
    - ✅ `AdministratorAccess`
8. Create user & **Download CSV file** with login credentials

### ➕ Add MFA to IAM User
1. Go to **Users > admin > Security credentials**
2. Click `Assign MFA device`
3. Use **Google Authenticator** again
4. Confirm MFA

✅ IAM user created securely

---

## 5️⃣ Enable Billing Alerts

### 🔁 Update Billing Preferences
1. Go to **Billing Dashboard**
2. Select `Billing preferences`
3. Enable:
    - ✅ Receive PDF invoice
    - ✅ Receive Free Tier usage alerts
    - ✅ Receive CloudWatch billing alerts

### 🛎 Create Billing Alarm in CloudWatch

1. Go to **CloudWatch** service
2. Ensure you're in: `N. Virginia (us-east-1)`
3. Navigate to `Alarms > All Alarms > Create Alarm`
4. Choose metric:
    - `Billing > Total Estimated Charge > USD`
5. Set threshold:
    - **Static threshold**: > `5 USD`
6. Notifications:
    - Create new **SNS topic**
    - Add your **email address**
    - Confirm email from your inbox
7. Name the alarm: `BillingAlert`

✅ Billing alert will notify you if costs exceed $5

---

## 6️⃣ Request Public SSL Certificate (ACM)

We’ll need this to enable HTTPS for your website.

### 📝 Steps:
1. Go to **ACM (AWS Certificate Manager)**
2. Region: **N. Virginia**
3. Click `Request certificate > Public certificate`
4. Add domain:
    - Format: `*.yourdomain.xyz`
5. Choose validation: `DNS Validation`
6. Submit request

### 📌 Validate Certificate in GoDaddy

1. Go to **GoDaddy > My Products > Manage DNS**
2. Add a **CNAME record**:
    - Name: (from ACM)
    - Value: (from ACM)
    - TTL: Default
3. Save the record

⏳ Wait up to **48 hours** (usually 15–30 mins)

Once validated:
✅ Certificate status becomes **Issued**

---

## 7️⃣ IAM User Login via Custom URL

### 🌐 Create Console Login Alias:
1. Go to **IAM > Dashboard**
2. Click `Customize` login URL
3. Set alias (e.g., `your-company-name`)
4. Final login URL:
   https://your-company-name.signin.aws.amazon.com/console


### 👨‍💻 Log in as IAM User:
1. Open the login URL in a **different browser**
2. Use credentials from CSV file
3. Enter 6-digit MFA from your Authenticator app
4. Change password at first login

✅ IAM login complete!

---

## ✅ Summary

| Task | Status |
|------|--------|
| ✅ AWS Free Tier Account Created | ✔️ |
| ✅ Root MFA Enabled | ✔️ |
| ✅ IAM Admin User Created | ✔️ |
| ✅ IAM User MFA Enabled | ✔️ |
| ✅ Billing Alert (CloudWatch) Setup | ✔️ |
| ✅ SSL Certificate Requested & Validated | ✔️ |
| ✅ Custom IAM Console URL Created | ✔️ |

---

**You’re now ready to use AWS securely and professionally!**
🔒 Remember: Always use your **IAM user**, not root, for day-to-day AWS work.

---

