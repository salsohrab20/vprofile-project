# Automated Provisioning with Vagrant and Bash Scripts

## Repository Structure
- `vagrant` folder contains automated provisioning setup.
- Choose folder based on OS and chip:
  - Windows & macOS (Intel chip)
  - macOS (M1/M2 chip)

## Overview
- Same `Vagrantfile` as manual provisioning with an extra step:
  - Each VM points to a bash script that provisions services automatically.

## Bash Scripts Overview
- Scripts are simple shell scripts (`.sh`) containing the same commands used in manual setup.
- Example: MySQL script (`mysql.sh`)
  - Starts with shebang `#!/bin/bash`
  - Sets variables (e.g., DB password)
  - Installs and enables MariaDB service
  - Runs SQL schema deployment commands (via CLI)
  - Includes firewall commands (covered later in AWS security groups)
- Other services have similar scripts:
  - Memcache (`memcached.sh`)
  - RabbitMQ (`rabbitmq.sh`)
  - Tomcat (`tomcat_ubuntu.sh`)
    - Creates system files using `cat << EOF` syntax to inject config
    - Starts and enables Tomcat service
    - Clones source, runs `mvn install`, deploys artifact
  - Nginx (`nginx.sh`)
    - Installs Nginx
    - Creates config files and disables default site
    - Enables custom site and starts Nginx service

## Key Notes
- Scripts automate what was done manually before.
- Even without bash scripting knowledge, these scripts are easy to understand.
- Bash scripting will be covered in detail later in the course.
- Next lecture: Run `vagrant up` to provision all VMs and services automatically and test the application.

---

*Ready to automate your entire stack with a single command!*

# Automated Stack Provisioning with Vagrant

## Running the Setup
- Open Git Bash and navigate to:  
  `source_code/vagrant/automated_provisioning`
- Run the command:   
```bash
  vagrant up
```

* This command will start bringing up all the virtual machines (VMs) automatically.

## Provisioning Flow

* VirtualBox launches VMs in sequence:

    1. **db01** (MySQL)
    2. **memcache**
    3. **rabbitmq**
    4. **app01** (Tomcat)
    5. **web01** (Nginx)
* Each VM waits to become stable before running its provisioning shell script.
* Provisioning runs tasks like installing packages, setting up services, cloning source code, and deploying applications.
* The entire process can take 15 to 30 minutes depending on your internet speed.
* No manual VM login required—Vagrant automates everything.

## Validation Steps

* Access the frontend via browser:
  `http://web01` (or use the static IP from `Vagrantfile`)
* Login credentials:
    * Username: `admin_vp`
    * Password: `admin_vp`
* Verify connectivity to all services:
    * Database login success
    * RabbitMQ functionality
    * Memcache caching and retrieval speed

## Managing the Environment

* To stop all VMs:

  ```bash
  vagrant halt
  ```
* To check the status of VMs:

  ```bash
  vagrant status
  ```
* To restart the stack without reprovisioning:

  ```bash
  vagrant up
  ```
* Provisioning happens only once during initial creation; subsequent starts just power on the VMs.

## Benefits

* Eliminates manual, error-prone, and time-consuming setups.
* Fully automated and repeatable environment.
* Infrastructure as Code approach ensures consistent, reliable deployments.

---

**Great job completing the project! Keep practicing and see you in the next one.**



