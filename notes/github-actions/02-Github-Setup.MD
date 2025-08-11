# 🔐 GitHub SSH Setup and VS Code Integration for CI/CD Project

In this lecture, we will set up the SSH login from your computer to your GitHub account, fork the source code repository, and integrate it with Visual Studio Code (VS Code).

---

## 🧾 Steps Overview

1. Generate SSH Key
2. Add SSH Key to GitHub
3. Fork the Repository
4. Configure Git to Use SSH
5. Clone Repository via SSH
6. Set Git Identity (Username & Email)
7. Open Project in VS Code
8. Make Test Commit and Push

---

## 🛠️ 1. Generate SSH Key

Open your terminal:

- **Windows**: Use Git Bash
- **Mac/Linux**: Use default terminal

```bash
cd ~
cd .ssh
ssh-keygen
```
- When prompted for a file name, use your GitHub username (optional but helpful).
- Hit Enter three times to complete key generation.
- You should now see two files:

```text
your_key_name (private key)
your_key_name.pub (public key)
```
## 🔑 2. Add Public SSH Key to GitHub
- Log in to your GitHub account
- Go to Settings > SSH and GPG Keys
- Click New SSH Key
- Add a title, then:
```bash
cat your_key_name.pub
```
- Copy the output and paste it into GitHub.
- Click Add SSH Key
- Enter your GitHub password if prompted.

## ✅ 3. Test SSH Authentication
- Run the following command to test the connection:

```bash
ssh -i ~/.ssh/your_key_name -T git@github.com
Type yes if asked to continue.

You should see:
"You've successfully authenticated..."
```

## 🍴 4. Fork the Repository
- Visit:
```arduino
https://github.com/hkhcoder/hprofile
```
- Make sure you're logged into GitHub
- Click Fork > Create a new fork
- Uncheck Copy main branch only
- Click Create Fork
- You should see:

```bash
hprofile forked from hkhcoder/hprofile
```

## ⚙️ 5. Configure Git to Use Your SSH Key
- Temporarily export the SSH key to be used:
``` bash
export GIT_SSH_COMMAND="ssh -i ~/.ssh/your_key_name"
```
- Now create a directory to clone the project:
``` bash
cd ~/Desktop
mkdir actions
cd actions
```
- Clone your forked repo using SSH:

```bash
git clone git@github.com:your_username/hprofile.git
cd hprofile
```
- Clear the environment variable:

```bash
unset GIT_SSH_COMMAND
```
## Inject SSH key into local repo config:
``` bash
git config core.sshCommand "ssh -i ~/.ssh/your_key_name -F /dev/null"
```

## 👤 6. Set Git Identity
- Set your GitHub username and email:
```bash

git config user.name "your_github_username"
git config user.email "your_email@example.com"
```
- Verify with:

``` bash
cat .git/config
```

## 💻 7. Open in VS Code
- Open VS Code
- Go to File > Open Folder
- Navigate to ~/Desktop/actions/edge-profile and open it.

## ✍️ 8. Make a Test Commit
- Open README.md
- Make a small edit (add/remove some # headers)
- Press Ctrl + S to save
- Go to Source Control tab in VS Code:
- Type commit message (e.g., test integration)
- Check the box to commit and push

## 🔍 9. Verify on GitHub
- Go to your forked repository on GitHub
- Refresh the page
- You should see your commit message and changes to README.md


## 🎯 Purpose of GIT_SSH_COMMAND
```bash
export GIT_SSH_COMMAND="ssh -i ~/.ssh/your_key_name"
```
> This command temporarily tells Git to use a specific private SSH key for all Git operations (e.g., clone, push, pull) in the current terminal session.

## ✅ When is this useful?
It’s especially useful in situations like:
1. You have multiple SSH keys
- If you manage:
- Work and personal GitHub accounts
- GitHub + GitLab/Bitbucket
- Multiple organizations
- then you might have different SSH keys for each. This command lets you explicitly specify which key to use without affecting your global configuration.

2. You want a one-time override
- If you only need to use a specific SSH key temporarily (e.g., for cloning one repo), this is a quick way to avoid editing your SSH config or repo settings.

3. Your SSH agent isn’t running or loaded
- If your key isn’t already loaded in the SSH agent, this method ensures the correct key is used without needing ssh-add.

### 🤔 Is it strictly necessary?
- 🚫 No — not always.
- If you already have:
```text
A working ~/.ssh/config with proper Host mappings Or the key is loaded into your ssh-agent Or you're using your default SSH key (~/.ssh/id_rsa)
```
- Then you can skip this step entirely.

### ✅ It becomes necessary when:
- You’re not using the default SSH key 
- You haven’t configured ~/.ssh/config
- You want to avoid permanent config changes
- You want to clone using SSH instead of HTTPS and Git doesn’t know which key to use
- 
#### 🧠 Example: Without GIT_SSH_COMMAND, Git may fail like this:
```bash
Permission denied (publickey).
fatal: Could not read from remote repository.
```
- This means Git tried to connect via SSH but couldn’t authenticate — often because:
- Wrong key
- No key
- Key not loaded

Using GIT_SSH_COMMAND solves that instantly for that session.

#### 💡 Better Long-Term Solutions
- If you're working with multiple SSH keys regularly, it's often better to create an SSH config:
```bash
# ~/.ssh/config
```
```
Host github.com
HostName github.com
User git
IdentityFile ~/.ssh/your_key_name
```
- This way, Git knows which key to use every time — no need for temporary export.

- ✅ Final Verdict

  | Scenario                              | Is `GIT_SSH_COMMAND` Needed? |
  | ------------------------------------- | ---------------------------- |
  | Default key used (`~/.ssh/id_rsa`)    | ❌ Not needed                 |
  | SSH agent is running with correct key | ❌ Not needed                 |
  | Using a non-default key temporarily   | ✅ Yes, it's convenient       |
  | Multiple accounts with different keys | ✅ Yes (or use SSH config)    |


