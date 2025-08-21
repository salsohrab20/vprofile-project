# 💻 VProfile Project: Setting Up VMs with Vagrant

---

## 🎯 Objective

In this lecture, we'll **set up 5 virtual machines** using **Vagrant** and a **Vagrantfile** provided in the project source code.

---

## 🔗 Step 1: Clone the Source Code

1. Visit: [`https://github.com/HKCoder/vprofile-project`](https://github.com/HKCoder/vprofile-project)
2. Click the **Code** dropdown → Copy the **HTTPS URL**.
3. Open your editor (**VS Code**, or any other).
4. Go to **Source Control** > `Clone Repository`
   Paste the copied URL.
5. Choose a destination folder (e.g., `F:\hk-hcoder`) and open the project.

---

## 🌳 Step 2: Switch Git Branch

The repository has multiple branches.
For local VM setup, **switch to the `local` branch**:

* Click on `main` at the bottom left in VSCode.
* Select: `origin/local`
* Confirm the branch is now `local`.

---

## 🗂️ Step 3: Navigate to Vagrant Folder

Within the project, open:

```bash
vprofile-project/vagrant/manual-provisioning-windows-mac-intel
```

* ✅ For Windows, macOS (Intel), or Linux → Use this folder
* 🍏 For macOS (M1/M2/M3) → Use the corresponding folder

---

## 📄 Step 4: Review the PDF Setup Guide

In the folder, open:
`Vagrant_VM_Setup_Guide.pdf`
Also available in the **lecture resources**.

---

## 🧩 Step 5: Install Required Vagrant Plugin

Install the `vagrant-hostmanager` plugin:

```bash
vagrant plugin install vagrant-hostmanager
```

🔌 This plugin adds **host mapping entries** automatically, so you can use VM hostnames instead of IPs.

---

## ⚙️ Step 6: Understand the Vagrantfile

Open `Vagrantfile` in VS Code.

It defines 5 VMs:

| VM Name | Purpose   | OS       | Memory |
| ------- | --------- | -------- | ------ |
| `db01`  | MySQL DB  | CentOS 9 | 600 MB |
| `mc01`  | Memcached | CentOS 9 | 600 MB |
| `rmq01` | RabbitMQ  | CentOS 9 | 600 MB |
| `app01` | Tomcat    | CentOS 9 | 800 MB |
| `web01` | NGINX     | Ubuntu   | 800 MB |

> 💡 Minimum RAM allocation is used to support most systems. If you have ≥ 16 GB RAM, feel free to increase.

Also notice these two entries:

```ruby
config.hostmanager.enabled = true
config.hostmanager.manage_host = true
```

These are used by the plugin to manage your system's `/etc/hosts` file.

---

## 🧼 Step 7: Clean Up Old VMs (if any)

Before creating new VMs, check for any old/stuck VMs:

```bash
vagrant global-status
```

If you see running VMs:

* Go to the shown folder path
* Run: `vagrant destroy`

Then clean up stale entries:

```bash
vagrant global-status --prune
```

---

## 🚀 Step 8: Launch the VMs

From the correct folder:

```bash
vagrant up
```

> ⏳ This will take a few minutes.

---

## 🔍 Step 9: Verify the Setup

Once VMs are up:

1. **SSH into web VM**:

   ```bash
   vagrant ssh web01
   ```

2. **Check Hostname**:

   ```bash
   hostname
   # Output: web01
   ```

3. **Switch to Root (Optional)**:

   ```bash
   sudo -i
   ```

4. **Check `/etc/hosts` File**:

   ```bash
   cat /etc/hosts
   ```

   You should see auto-added entries like:

   ```
   192.168.56.11   db01
   192.168.56.12   mc01
   ...
   ```

5. **Ping Test**:

   ```bash
   ping app01 -c 4
   ```

   This tests DNS resolution and network connectivity between VMs.

---

## 🔁 Startup & Shutdown Best Practices

🟢 **Startup Order**:

1. MySQL (db01)
2. Memcached (mc01)
3. RabbitMQ (rmq01)
4. Tomcat (app01)
5. NGINX (web01)

🔴 **Shutdown Order** (Reverse):

1. NGINX (web01)
2. Tomcat (app01)
3. RabbitMQ (rmq01)
4. Memcached (mc01)
5. MySQL (db01)

> 🧠 This order ensures dependencies are correctly managed during startup/shutdown.

---

## 🧪 Optional Exercise

✅ SSH into each VM:

```bash
vagrant ssh app01
```

✅ Ping backend services:

```bash
ping db01 -c 4
ping mc01 -c 4
```

If a VM doesn’t respond, reboot that VM:

```bash
vagrant reload <vm-name>
```

---

## ✅ Summary

You have now:

* Cloned the project from GitHub
* Switched to the correct Git branch
* Reviewed and understood the Vagrantfile
* Installed necessary plugin
* Brought up 5 VMs with proper DNS-like resolution
* Verified connectivity between VMs

---

# 🛠️ VProfile Project: MySQL Database Setup

---

## 🎯 Objective

Set up the **MySQL (MariaDB)** database in the `db01` virtual machine, initialize the schema, and configure users for the VProfile application.

---

## 🧼 Prerequisites

* ✅ VMs are already up and running (from the previous lecture)
* ✅ You're in the correct folder:
  `vprofile-project/vagrant/manual-provisioning-windows-mac-intel`
* ✅ If you've taken a long break:
  Run `vagrant reload` to reboot all VMs before proceeding

---

## 🪵 Step 1: Login to the DB VM

```bash
vagrant ssh db01
sudo -i    # Switch to root
```

---

## 🔄 Step 2: Update the OS

```bash
dnf update -y
```

> ⏳ This will take some time. It's essential to apply the latest security patches and package versions.

---

## 📦 Step 3: Install Required Packages

```bash
dnf install epel-release -y
dnf install git mariadb-server -y
```

---

## 🚀 Step 4: Start & Enable MariaDB

```bash
systemctl start mariadb
systemctl enable mariadb
systemctl status mariadb   # Should show “active (running)”
```

Press `q` to quit the status screen.

---

## 🔒 Step 5: Secure the Database

Run the built-in hardening tool:

```bash
mysql_secure_installation
```

### Expected Prompts & Answers:

| Prompt                                | Response            |
| ------------------------------------- | ------------------- |
| Enter current root password           | *(press Enter)*     |
| Switch to unix\_socket authentication | `Y` *(press Enter)* |
| Change root password?                 | `Y` → `admin123`    |
| Remove anonymous users?               | `Y`                 |
| Disallow root login remotely?         | `Y`                 |
| Remove test database?                 | `Y`                 |
| Reload privilege tables now?          | `Y`                 |

> ⚠️ Use a stronger password in production.

---

## 🧱 Step 6: Create Database & User

### Login to MariaDB

```bash
mysql -u root -p
# Password: admin123
```

### Inside MySQL Prompt:

```sql
CREATE DATABASE accounts;

GRANT ALL PRIVILEGES ON accounts.* TO 'admin'@'localhost' IDENTIFIED BY 'admin123';
GRANT ALL PRIVILEGES ON accounts.* TO 'admin'@'%' IDENTIFIED BY 'admin123';

FLUSH PRIVILEGES;
EXIT;
```

---

## 📥 Step 7: Clone Project Source Code (Inside VM)

```bash
cd /tmp
git clone -b local https://github.com/HKCoder/vprofile-project.git
cd vprofile-project
```

---

## 🧩 Step 8: Import the Database Schema

Check for the SQL dump file:

```bash
ls src/main/resources/
# Should see: db_backup.sql
```

Run the import command:

```bash
mysql -u root -p accounts < src/main/resources/db_backup.sql
# Password: admin123
```

---

## 🧪 Step 9: Verify the Import

Log in again:

```bash
mysql -u root -p accounts
# Password: admin123
```

Then run:

```sql
SHOW TABLES;
```

You should see a list of all the tables.

Optionally:

```sql
SHOW DATABASES;
```

You should see `accounts`, `mysql`, `information_schema`, etc.

Exit:

```sql
EXIT;
```

---

## 🛡️ Step 10 (Optional): Firewall Settings

> We’ll cover firewalls later.
> These commands are optional and not needed for now.

```bash
# Example (Skip for now):
# firewall-cmd --zone=public --add-port=3306/tcp --permanent
# firewall-cmd --reload
```

---

## ✅ Summary

You've completed the **database setup**:

* Updated system packages
* Installed MariaDB and Git
* Secured the DB with `mysql_secure_installation`
* Created the `accounts` database and `admin` user
* Imported schema from `db_backup.sql`

---

➡️ Next: We'll proceed with **Memcached setup** in `mc01`.

# 🛠️ VProfile Project: Memcached Setup

---

## 🎯 Objective

Set up the **Memcached service** on the `mc01` virtual machine (used for caching in the VProfile stack).

---

## 🧼 Step 1: SSH into `mc01` VM

If you're still logged into `db01`, exit first:

```bash
exit
```

Now connect to `mc01`:

```bash
vagrant ssh mc01
sudo -i   # Switch to root
```

---

## 🔄 Step 2: Update Packages (Optional)

```bash
dnf update -y
```

> ⚠️ This step is optional and can be skipped to save time. It's typically recommended when provisioning a fresh VM.

---

## 📦 Step 3: Install Required Packages

Install the EPEL repository and Memcached:

```bash
dnf install epel-release -y
dnf install memcached -y
```

---

## 🚀 Step 4: Start and Enable Memcached Service

```bash
systemctl start memcached
systemctl enable memcached
```

---

## ⚙️ Step 5: Allow Remote Connections

Memcached by default listens only to localhost (`127.0.0.1`), so we must update it to listen to all IPs (`0.0.0.0`).

### Use `sed` to replace IP binding:

```bash
sed -i 's/127.0.0.1/0.0.0.0/' /etc/sysconfig/memcached
```

> 🔍 This file `/etc/sysconfig/memcached` sets environment variables used when the service starts.
> Look for the `OPTIONS` variable—it controls the IP binding.

### Optionally: View file manually

```bash
cat /etc/sysconfig/memcached
```

You should now see:

```bash
OPTIONS="-l 0.0.0.0"
```

---

## 🔁 Step 6: Restart Memcached

Restart to apply configuration changes:

```bash
systemctl restart memcached
```

---

## 🧪 Step 7 (Optional): Run Memcached in Daemon Mode (Again)

Run Memcached explicitly in the background (daemon mode), specifying ports:

```bash
memcached -p 11211 -U 11111 -u memcached -d
```

> * `-p 11211`: TCP port (default for Memcached)
> * `-U 11111`: UDP port
> * `-u memcached`: Run under `memcached` user
> * `-d`: Run as a daemon (background process)

This is sometimes helpful if your service isn’t picked up by `systemd` alone or for debugging custom configs.

---

## 🛡️ Step 8 (Optional): Firewall Rules

> 🔐 You can skip this for now. Firewall concepts will be covered later.

---

## ✅ Summary

You’ve completed the **Memcached setup**:

* Installed and enabled the service
* Configured it to allow remote connections (from Tomcat)
* Restarted the service with updated settings

---

## 🏁 Next Step

➡️ Proceed to the [RabbitMQ Setup](#) in the next lecture.

---

# 🛠️ VProfile Project: RabbitMQ Setup

---

## 🎯 Objective

Install and configure **RabbitMQ**, a message queuing service, on the `rmq01` virtual machine.

---

## 🧼 Step 1: SSH into `rmq01` VM

```bash
vagrant ssh rmq01
sudo -i  # Switch to root
```

---

## 🔄 Step 2: Update System Packages

This is optional but recommended when starting a new VM:

```bash
dnf update -y
```

---

## 📦 Step 3: Install Required Tools

```bash
dnf install epel-release wget -y
```

> ✅ `wget` may already be installed.

---

## 📥 Step 4: Add RabbitMQ Repository and Install

```bash
dnf install https://packagecloud.io/rabbitmq/rabbitmq-server/packages/el/8/rabbitmq-release-3.8-2.noarch.rpm -y
dnf --enablerepo=rabbitmq_rabbitmq-server -y install rabbitmq-server
```

> This installs the RabbitMQ release package, enables the repository, and installs RabbitMQ.

---

## 🚀 Step 5: Enable and Start RabbitMQ Service

You can do this in one combined command:

```bash
systemctl enable --now rabbitmq-server
```

> This starts the service **and** enables it to start on boot.

---

## ⚙️ Step 6: Configuration File Update

Overwrite the RabbitMQ config file with required minimal config:

```bash
echo '[{rabbit, [{loopback_users, []}]}].' > /etc/rabbitmq/rabbitmq.config
```

> This disables the restriction on loopback-only access for default users.

---

## 🔁 Step 7: Restart RabbitMQ

```bash
systemctl restart rabbitmq-server
```

---

## 👤 Step 8: Add RabbitMQ User & Permissions

### Add user named `test` with password `test`:

```bash
rabbitmqctl add_user test test
```

### Assign admin tag to the user:

```bash
rabbitmqctl set_user_tags test administrator
```

### Grant full permissions to the user:

```bash
rabbitmqctl set_permissions -p / test ".*" ".*" ".*"
```

---

## 🔁 Step 9: Restart & Verify Status

Restart one more time to ensure config changes are picked up:

```bash
systemctl restart rabbitmq-server
```

Check the status:

```bash
systemctl status rabbitmq-server
```

> Look for `Active: active (running)`
> Press `Q` to exit the status screen.

---

## ✅ Summary

You have successfully:

* Installed RabbitMQ from a custom repo
* Enabled the service
* Configured a new user (`test`)
* Applied necessary permissions

RabbitMQ is now ready to be used by the VProfile application.

---

## 🏁 Next Step

➡️ Proceed to the [Tomcat + VProfile App Setup](#) in the next lecture.

---

# 🚀 VProfile Project: Tomcat + VProfile Application Setup

---

## 🎯 Objective

Set up **Tomcat 10** on the `app01` VM, and deploy the **VProfile application** by:

1. Installing and configuring Tomcat.
2. Installing Maven.
3. Building the VProfile source code.
4. Deploying the generated `.war` file to Tomcat.

---

## 🧼 Step 1: SSH into `app01` VM

```bash
vagrant ssh app01
sudo -i
```

---

## 🔄 Step 2: Update System Packages

```bash
dnf update -y
```

---

## 📦 Step 3: Install Dependencies

```bash
dnf install epel-release git wget java-17-openjdk-devel -y
```

---

## 💡 Step 4: Download and Install Tomcat 10

### Navigate to `/tmp` and download the binary:

```bash
cd /tmp
wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.20/bin/apache-tomcat-10.1.20.tar.gz
```

> Make sure the link is up to date if it doesn’t work.

### Extract the archive:

```bash
tar xvf apache-tomcat-10.1.20.tar.gz
```

---

## 👤 Step 5: Create Tomcat User

```bash
useradd -r -m -U -d /usr/local/tomcat -s /bin/false tomcat
```

> Creates a system user `tomcat` and the home directory `/usr/local/tomcat`.

---

## 📁 Step 6: Move Tomcat Files

```bash
cp -r apache-tomcat-10.1.20/* /usr/local/tomcat
```

---

## 🔐 Step 7: Set Ownership

```bash
chown -R tomcat:tomcat /usr/local/tomcat
```

---

## ⚙️ Step 8: Create Systemd Service File

### Create and edit:

```bash
vim /etc/systemd/system/tomcat.service
```

### Paste the following content:

```ini
[Unit]
Description=Tomcat 10 servlet container
After=network.target

[Service]
Type=forking

User=tomcat
Group=tomcat

Environment="JAVA_HOME=/usr/lib/jvm/java-17-openjdk"
Environment="CATALINA_PID=/usr/local/tomcat/temp/tomcat.pid"
Environment="CATALINA_HOME=/usr/local/tomcat"
Environment="CATALINA_BASE=/usr/local/tomcat"
Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"
Environment="JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom"

ExecStart=/usr/local/tomcat/bin/startup.sh
ExecStop=/usr/local/tomcat/bin/shutdown.sh

[Install]
WantedBy=multi-user.target
```

Save and exit (`:wq`)

---

## 🚀 Step 9: Start and Enable Tomcat Service

```bash
systemctl daemon-reexec  # OR daemon-reload if no major change
systemctl enable --now tomcat
systemctl status tomcat
```

Look for: `Active: active (running)`

---

## 🛠️ Step 10: Install Maven

```bash
cd /tmp
wget https://downloads.apache.org/maven/maven-3/3.9.6/binaries/apache-maven-3.9.6-bin.zip
unzip apache-maven-3.9.6-bin.zip
cp -r apache-maven-3.9.6 /usr/local/maven
```

### Set a temporary memory fix (needed due to VM size):

```bash
export MAVEN_OPTS="-Xmx512m"
```

---

## 📥 Step 11: Clone and Build VProfile Source Code

```bash
git clone -b local https://github.com/devopsjourney1/vprofile-project.git
cd vprofile-project
```

---

## 🔍 Step 12: Review `application.properties` Configuration

```bash
vim src/main/resources/application.properties
```

✅ Ensure the following entries match your service hostnames and credentials:

```properties
spring.datasource.url=jdbc:mysql://db01:3306/accounts
spring.datasource.username=admin
spring.datasource.password=admin123

memcached.active.host=mc01
rabbitmq.address=rmq01
rabbitmq.username=test
rabbitmq.password=test
```

---

## 📦 Step 13: Build with Maven

```bash
/usr/local/maven/bin/mvn install
```

⏳ Takes time. When complete, look for a `target/` directory with:

```bash
target/vprofile-v2.war
```

---

## 🚚 Step 14: Deploy WAR File to Tomcat

### Remove default ROOT app:

```bash
rm -rf /usr/local/tomcat/webapps/ROOT
```

### Copy WAR file as `ROOT.war`:

```bash
cp target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war
```

Tomcat will automatically extract it into `/usr/local/tomcat/webapps/ROOT`.

---

## 🔐 Step 15: Fix Permissions

```bash
chown -R tomcat:tomcat /usr/local/tomcat/webapps
```

---

## 🔁 Step 16: Restart and Verify

```bash
systemctl restart tomcat
systemctl status tomcat
```

✅ You should see: `Active: active (running)`

---

## ✅ Success!

Your VProfile application is now deployed and running on Tomcat in `app01`.

You can validate it later via a browser once Nginx is set up as the front-end proxy.

---

## ➡️ Next Step

Proceed to the next lecture: **Nginx Setup**
You'll use it to expose the VProfile application through a web-friendly interface.

---

Here's the **final lecture documentation** for setting up **Nginx as a reverse proxy (load balancer)** for the VProfile project. It's written in **clean Markdown format** and follows a professional DevOps style that you can use for documentation, GitHub repos, or exporting as a PDF later.

---

# 🌐 VProfile Project: Nginx Setup as Load Balancer (Web01)

---

## 🎯 Objective

Set up **Nginx** on `web01` (Ubuntu machine) to act as a reverse proxy (load balancer) for the Tomcat application running on `app01`.

---

## 🧼 Step 1: SSH into Web Server

```bash
vagrant ssh web01
sudo -i
```

---

## 🔄 Step 2: Update & Upgrade the System

```bash
apt update && apt upgrade -y
```

> You may encounter prompts — use **Tab** to select OK and press **Enter**.

---

## 📦 Step 3: Install Nginx

```bash
apt install nginx -y
```

---

## 📁 Step 4: Understand Nginx Directory Structure

* **Configuration directory**:

  ```
  /etc/nginx
  ```

* **Virtual host configs (create here)**:

  ```
  /etc/nginx/sites-available/
  ```

* **Enabled host configs (symbolic links live here)**:

  ```
  /etc/nginx/sites-enabled/
  ```

---

## ⚙️ Step 5: Create a Reverse Proxy Config File

### Open the config file:

```bash
vim /etc/nginx/sites-available/vproapp
```

### Paste the following config:

```nginx
upstream vproapp {
    server app01:8080;
}

server {
    listen 80;

    location / {
        proxy_pass http://vproapp;
    }
}
```

> This means:
>
> * Listens on port 80.
> * Forwards all traffic to `app01` on port `8080` (Tomcat).
> * `upstream` block defines backend app server(s).

---

## 🧹 Step 6: Remove Default Nginx Site (Optional)

```bash
rm -f /etc/nginx/sites-enabled/default
```

---

## 🔗 Step 7: Enable Your Site Configuration

```bash
ln -s /etc/nginx/sites-available/vproapp /etc/nginx/sites-enabled/
```

---

## 🔁 Step 8: Restart Nginx

```bash
systemctl restart nginx
systemctl status nginx
```

✅ You should see `Active: active (running)`

---

## ✅ Completion

🎉 Nginx is now running as a **reverse proxy** and forwarding HTTP requests to the Tomcat server running on `app01`.

Next, we’ll **validate the full stack** by accessing the VProfile application in the browser.

---

## 🧠 Final Notes

* Nginx will forward `http://web01` (port 80) ➝ `app01:8080`.
* You don’t need to memorize any commands — reuse documentation & tools.
* You'll revisit this stack often in:

    * CI/CD pipelines
    * AWS deployment
    * Docker/Kubernetes setups

---

## 🚀 Next Step: Validate the Stack

Join the next lecture where we test the full VProfile stack — from the browser to the database.

---

Got it! Here’s a polished summary of your **validation & wrap-up lecture** for the VProfile project. This is something you can keep as part of your course notes or documentation for quick reference:

---

# ✅ VProfile Project: Validation & Wrap-up

---

## 🖥️ Step 1: Access the Application

* IP of `web01`: `56.11` (check from `Vagrantfile` or network config)

* Open your browser and enter:

  ```
  http://56.11
  ```

* Default HTTP port is `80` — no need to specify `:80`.

* Nginx listens on port 80 and proxies requests to Tomcat (`app01:8080`).

---

## 🎯 What Happens

* The browser hits Nginx on `web01`.
* Nginx forwards to Tomcat app on `app01:8080`.
* The **VProfile web page** loads — proving Nginx → Tomcat connection is working.

---

## 🔐 Step 2: Login to Application

* Use the credentials stored in the database:

  ```
  Username: admin_VP
  Password: admin_VP
  ```

* Successful login means the **database connection** is working correctly.

---

## 🚦 Step 3: Validate Other Services from UI

* **RabbitMQ test:**

    * Click the button labeled for `admin_VP`.
    * Should generate and confirm queue connection.
    * If no confirmation, RabbitMQ is not connected properly.

* **Memcached test:**

    * Click “All users” to load users from the database.
    * Select a user (e.g., Ajaz Habib).
    * Message appears: "Data is from DB and inserted in cache."
    * Go back, click the same user again.
    * Data loads faster, confirming cache retrieval.

---

## 🎉 All core components validated:

| Service   | Status                |
| --------- | --------------------- |
| Nginx     | Request forwarding OK |
| Tomcat    | Application running   |
| Database  | Login & queries OK    |
| RabbitMQ  | Queue connection OK   |
| Memcached | Cache working         |

---

## 🧠 Final Advice

* Don’t try to **memorize commands** — understand the *why* behind each step.
* Keep your own **documentation** for all projects — this helps troubleshooting and repetition.
* Use Google, ChatGPT, and official docs freely in real time.
* Understanding setup helps with future **automation**, CI/CD, containerization.
* Setup experience = confidence boost!

---

## 🔄 Optional Cleanup

* When done, you can destroy the Vagrant environment:

  ```bash
  vagrant destroy --force
  ```

* Next lecture: automatic setup via scripts — no manual commands needed.

---





