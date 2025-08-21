# 🚀 VProfile Project: Local Web Application Stack Setup

## 🎯 Project Overview

Welcome to the **VProfile Project (Local Setup)**!
In this project, we’ll build a **multi-tier web application stack** locally on your machine (laptop or desktop).

### 🧩 Why Are We Doing This?

1. **Foundation for Future Projects**
    * Acts as a **baseline setup**.
    * Future projects will involve:

        * Refactoring
        * Containerization
        * Kubernetes deployments
        * And more...

2. **Personal R&D Environment**

    * Set up your **own lab locally** for experimentation and learning.
    * Simulates a real-world stack for hands-on DevOps practice.

---

## 🛠️ Real-World Scenario

Imagine you're working on a project with:

* 📦 **Databases** (MySQL, PostgreSQL)
* 🌐 **Web Servers** (Apache, NGINX)
* 🔥 **App Servers** (Tomcat, JBoss)
* 📘 A **Runbook** for setup

Challenges:

* ❌ Fear of making changes on real servers
* ❌ Manual setups are **complex**, **time-consuming**, and **not repeatable**

### ✅ Our Solution: Automate Local Stack Setup

We’ll use **Infrastructure as Code (IaC)** to automate and repeat the setup easily.

---

## 🧰 Tools Required

| Tool                        | Purpose                        |
| --------------------------- | ------------------------------ |
| 🧱 **Oracle VM VirtualBox** | Hypervisor to create VMs       |
| 📦 **Vagrant**              | VM provisioning & automation   |
| 🖥️ **Git Bash**            | CLI for commands + Git         |
| 💻 **IDE/Text Editor**      | Code editing (Sublime, VSCode) |

> 📌 These should be installed from the **prerequisite** setup.

---

## 🌐 Stack Architecture Overview

We’ll set up a **social networking web app** written in Java, using the following services:

### 🔄 Application Flow

```plaintext
Browser → IP (Load Balancer - NGINX) → Tomcat → App Code
         ↳ Memcached ↔ MySQL
         ↳ RabbitMQ
```

### 🧱 Services Overview

| Service             | Role                                              |
| ------------------- | ------------------------------------------------- |
| 🌐 **NGINX**        | Load balancer (replaces URL for now with IP)      |
| ☕ **Apache Tomcat** | Java Web App Server (hosts the application)       |
| 🗃️ **MySQL**       | Stores user credentials and app data              |
| ⚡ **Memcached**     | Database cache layer to improve performance       |
| 📩 **RabbitMQ**     | (Dummy) Message queue broker for integration demo |

> 🧠 **Note:** RabbitMQ is added just for practice, not functional.

---

## 📚 Why This Stack?

* Mimics **real-world architectures**
* Helps you understand:

    * Service connectivity
    * Infrastructure layers
    * DevOps automation principles

---

## 🏗️ Automation Architecture

We will automate everything using:

```plaintext
Vagrantfile → Vagrant → VirtualBox → VMs
                          ↳ Shell scripts → Service Setup
```

Each VM will run one or more of:

* NGINX
* Tomcat
* MySQL
* Memcached
* RabbitMQ

---

## 🔁 Project Execution Flow

1. ✅ Set up prerequisites
2. 📥 Clone the source code
3. 📁 `cd` into the Vagrant project directory
4. 📄 Review the `Vagrantfile`
5. 🚀 `vagrant up` to launch VMs
6. 🔍 Validate VM health and interconnectivity
7. 🧱 Set up services:

    * MySQL
    * Memcached
    * RabbitMQ
    * Tomcat
    * NGINX
8. 🛠️ Build and deploy the Java application
9. 🌐 Open browser → Enter IP address → Verify deployment

---

## 🧠 DevOps Learning Goals

* Understand **multi-tier architecture**
* Practice **automated local setups**
* Learn service interaction & integration
* Prepare for:

    * Docker
    * Kubernetes
    * Jenkins
    * Ansible

> 📝 You don’t need deep knowledge of each service now. Focus on flow & implementation. You’ll gain hands-on experience throughout the course.

---


