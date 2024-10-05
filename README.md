<div align=center>

# Deployment

</div>

# SSH Guide

**SSH** (Secure Shell) is a protocol used to securely log into remote systems. It’s an essential tool for system administrators, particularly for accessing remote Linux servers and managing GitHub repositories securely. This guide will walk you through the core aspects of SSH, including configuration, key-based authentication, SSH for GitHub, and more.

## Table of Contents
- [Introduction](#introduction-ssh)
- [Core Syntax](#core-syntax)
- [How Does SSH Work?](#how-does-ssh-work)
- [How To Configure SSH](#how-to-configure-ssh)
- [Log Into SSH with Keys](#log-into-ssh-with-keys)
- [Client-Side Options](#client-side-options)
- [Disabling Password Authentication](#disabling-password-authentication)
- [Accessing Private GitHub Repositories with SSH](#accessing-private-github-repositories-with-ssh)
- [Conclusion](#conclusion)

---

## Introduction SSH

SSH is the most common way to access remote Linux servers securely, and it can also be used to access private GitHub repositories. This guide will teach you how to use SSH and configure it for both server management and GitHub access.

---

## Core Syntax

To connect to a remote system using SSH, use the `ssh` command:

```bash
ssh remote_host
```

If your remote username is different, specify it like this:

```bash
ssh remote_username@remote_host
```

Exit an SSH session with:

```bash
exit
```

---

## How Does SSH Work?

SSH connects a client program (your machine) to an SSH server (`sshd`) running on a remote host. To start the SSH server on Ubuntu, use:

```bash
sudo systemctl start ssh
```

Ensure the server is running for SSH connections.

---

## How To Configure SSH

On Ubuntu, the main configuration file is located at `/etc/ssh/sshd_config`.

Backup the file before editing:

```bash
sudo cp /etc/ssh/sshd_config{,.bak}
```

Open it for editing:

```bash
sudo nano /etc/ssh/sshd_config
```

### Common Configuration Options

- **Port 22**: Default SSH port. Change it only if needed.
- **HostKey**: Specifies where the global host keys are stored.
- **LogLevel INFO**: Adjust the logging level to troubleshoot SSH issues.
- **PermitRootLogin no**: Disable root login for better security.
- **X11Forwarding yes**: Enable to view a remote system’s GUI on your local machine.

Reload the SSH service after making changes:

```bash
sudo systemctl reload ssh
```

---

## Log Into SSH with Keys

Key-based authentication is faster and more secure than passwords.

### How Does Key-based Authentication Work?

You create a **private key** (kept secret on the client machine) and a **public key** (shared with the server). The server verifies the client by using the public key.

### How To Create SSH Keys

Generate SSH keys with:

```bash
ssh-keygen -t rsa
```

Keys are created at `~/.ssh/id_rsa` (private) and `~/.ssh/id_rsa.pub` (public). Transfer the public key to the server:

```bash
ssh-copy-id remote_host
```

### SSH Into the Server Using the Key

Once the public key is copied to the server, you can log in without needing a password:

```bash
ssh username@remote_host
```

SSH will automatically use your private key (`id_rsa`) to authenticate the connection.

If your private key is in a different location, specify the path with the `-i` option:

```bash
ssh -i /path/to/private_key username@remote_host
```
---

## Client-Side Options

- **Change Port**: If your server uses a non-standard port, specify it with the `-p` flag:
  
  ```bash
  ssh -p port_number remote_host
  ```

- **Run Single Command**: Run a single command remotely without opening a full SSH session:
  
  ```bash
  ssh remote_host command_to_run
  ```

- **X11 Forwarding**: Forward a GUI from the remote server:
  
  ```bash
  ssh -X remote_host
  ```

---

## Disabling Password Authentication

For added security, you can disable password-based login once you’ve configured SSH keys.

1. Open the SSH configuration file:

   ```bash
   sudo nano /etc/ssh/sshd_config
   ```

2. Set the following:

   ```bash
   PasswordAuthentication no
   PubkeyAuthentication yes
   ChallengeResponseAuthentication no
   ```

3. Reload SSH:

   ```bash
   sudo systemctl reload ssh
   ```

---

## Accessing Private GitHub Repositories with SSH

Using SSH to access private GitHub repositories offers more security and eliminates the need to enter your password each time you interact with a repo.

### Step 1: Generate an SSH Key (if not already done)

If you haven’t already generated SSH keys, do so with:

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

- This will generate a public key (`id_rsa.pub`) and a private key (`id_rsa`) in the `~/.ssh` directory.
- When prompted, press Enter to accept the default file location and (optionally) set a passphrase.

### Step 2: Add the SSH Key to the SSH Agent

Start the SSH agent in the background:

```bash
eval "$(ssh-agent -s)"
```

Add your private key to the SSH agent:

```bash
ssh-add ~/.ssh/id_rsa
```

### Step 3: Add the Public Key to Your GitHub Account

1. Copy the contents of your public key to the clipboard:

   ```bash
   cat ~/.ssh/id_rsa.pub
   ```

2. Go to GitHub:
   - Navigate to **Settings** → **SSH and GPG keys**.
   ![alt text](image.png)
   ![alt text](image-1.png)
   - Click **New SSH key**, give it a title, and paste your public key.
   ![alt text](image-2.png)

### Step 4: Clone a Private Repository Using SSH

Now, you can clone your private repository using SSH instead of HTTPS:

```bash
git clone git@github.com:your_username/your_private_repo.git
```

If you need to switch an existing repository from HTTPS to SSH, use:

```bash
git remote set-url origin git@github.com:your_username/your_private_repo.git
```

---

## Conclusion

Mastering SSH is crucial for any system administrator or developer working with cloud infrastructure and Git repositories. With SSH, you can securely access remote systems, manage GitHub repositories, and automate tasks, all while maintaining strong security practices.

---


# NVM (Node Version Manager)
nvm is a version manager for node.js, designed to be installed per-user, and invoked per-shell. nvm works on any POSIX-compliant shell (sh, dash, ksh, zsh, bash), in particular on these platforms: unix, macOS, and windows WSL.

## Table of Contents

- [Features](#features)
- [Example Usage](#example-usage)
- [Installation and Updating](#installation-and-updating)
  - [Using cURL](#using-curl)
  - [Using Wget](#using-wget)
  - [Profile Setup](#profile-setup)
  - [Postpone Using NVM](#postpone-using-nvm)
- [Verify Installation](#verify-installation)
- [Troubleshooting](#troubleshooting)
  - [Linux](#linux)
  - [macOS](#macos)
  - [Common Fixes](#common-fixes)
- [Uninstalling](#uninstalling)
- [Additional Notes](#additional-notes)

---

## Features
- Quickly switch between Node.js versions.
- Easy installation of new Node.js versions.
- Works on POSIX-compliant shells (bash, zsh, etc.) on Linux, macOS, and Windows WSL.
- Install NVM per user and invoke it per shell.

---

## Example Usage

```bash
$ nvm use 16
Now using node v16.9.1 (npm v7.21.1)

$ node -v
v16.9.1

$ nvm use 14
Now using node v14.18.0 (npm v6.14.15)

$ node -v
v14.18.0

$ nvm install 12
Now using node v12.22.6 (npm v6.14.5)

$ node -v
v12.22.6
```

---

## Installation and Updating

To install or update NVM, run the install script using `cURL` or `Wget`:

### Using cURL
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
```

### Using Wget
```bash
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
```

### Profile Setup
The install script will attempt to add the following lines to the correct profile:

```bash
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```

#### Postpone Using NVM
If you want to install NVM without using it immediately, you can run the installation script with the `--no-use` flag:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash --no-use
```

---

## Verify Installation

To check if NVM is installed correctly, run:

```bash
command -v nvm
```

You should see the output `nvm`. Note that `which nvm` will not work since NVM is a sourced shell function, not an executable.

---

## Troubleshooting

### Linux
If `nvm: command not found` occurs after installation, try the following:

- Close and reopen your terminal, then run `command -v nvm`.
- Source your shell's profile file:
  - For Bash: `source ~/.bashrc`
  - For Zsh: `source ~/.zshrc`
  - For Ksh: `. ~/.profile`

### macOS
If you encounter issues on macOS, especially if using Zsh, you may need to manually create a `.zshrc` file:

```bash
touch ~/.zshrc
```

Then re-run the installation script and restart your terminal.

### Common Fixes
- Ensure that the proper profile files are sourced, or add the following snippet manually to `~/.bash_profile`, `~/.zshrc`, or another relevant file:

  ```bash
  export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
  [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
  ```

- Restart your terminal, or open a new terminal tab/window and try again.

---

## Uninstalling

To uninstall NVM, remove the `~/.nvm` directory:

```bash
rm -rf ~/.nvm
```

Also, remove any lines added to your shell profile files (e.g., `~/.bashrc`, `~/.zshrc`).

---

## Additional Notes

- On macOS with Apple Silicon, Node.js provides `arm64` binaries starting from v16.0.0.
- NVM supports customization of the install source, directory, profile, and version via environment variables (e.g., `NVM_DIR`).

For more detailed information, troubleshooting, or advanced usage, visit the [official NVM repository](https://github.com/nvm-sh/nvm).

---
# PM2 Process Management - Quick Start Guide

## Table of Contents
- [Introduction](#introduction-pm2)
- [Installation](#installation)
- [Starting an App](#starting-an-app)
- [Managing Processes](#managing-processes)
- [Checking Status, Logs, and Metrics](#checking-status-logs-and-metrics)
- [Cluster Mode](#cluster-mode)
- [Ecosystem File](#ecosystem-file)
- [Setup Startup Script](#setup-startup-script)
- [Restart Application on Changes](#restart-application-on-changes)
- [Updating PM2](#updating-pm2)
- [PM2 CheatSheet](#pm2-cheatsheet)

---

## Introduction PM2
PM2 is a daemon process manager that helps manage and keep your Node.js and other applications online. It offers an intuitive CLI for managing processes and applications, ensuring they remain stable and running efficiently.

---

## Installation

### Using NPM
To install PM2 globally using NPM:

```bash
$ npm install pm2@latest -g
```

### Using Yarn
Alternatively, install PM2 globally with Yarn:

```bash
$ yarn global add pm2
```

### Using NVM for Node.js Installation
You can install Node.js and NPM using NVM (Node Version Manager) to manage different versions of Node.js.

---

## Starting an App

### Simple App Start
To start, daemonize, and monitor an application, use:

```bash
$ pm2 start app.js
```

### Starting Other Applications
PM2 can handle various application types:

```bash
$ pm2 start bashscript.sh
$ pm2 start python-app.py --watch
$ pm2 start binary-file -- --port 1520
```

### Useful CLI Options
PM2 provides multiple options to customize application management:

- **App name:** `--name <app_name>`
- **Watch for file changes:** `--watch`
- **Memory threshold for reloads:** `--max-memory-restart <200MB>`
- **Log file path:** `--log <log_path>`
- **Pass arguments:** `-- arg1 arg2 arg3`
- **Restart delay:** `--restart-delay <ms>`
- **Prefix logs with timestamp:** `--time`
- **Disable auto-restart:** `--no-autorestart`
- **Cron restart schedule:** `--cron <cron_pattern>`
- **No daemon mode:** `--no-daemon`

---

## Managing Processes

### Restart, Reload, Stop, and Delete
PM2 makes managing applications simple:

```bash
$ pm2 restart app_name
$ pm2 reload app_name
$ pm2 stop app_name
$ pm2 delete app_name
```

Replace `app_name` with:

- `all` to act on all processes
- Process ID (`id`) to act on a specific process

---

## Checking Status, Logs, and Metrics

### List Managed Applications
List the status of all PM2-managed applications:

```bash
$ pm2 [list|ls|status]
```

### Display Logs
To display logs in real time:

```bash
$ pm2 logs
```

To display older logs:

```bash
$ pm2 logs --lines 200
```

### Terminal-Based Dashboard
View a real-time process dashboard directly in your terminal:

```bash
$ pm2 monit
```

### Web Monitoring with pm2.io
For a cross-server diagnostic and monitoring web interface:

```bash
$ pm2 plus
```

---

## Cluster Mode
For Node.js applications, PM2 can act as a load balancer to distribute connections between spawned processes:

```bash
$ pm2 start app.js -i max
```

Learn more about cluster mode [here](https://pm2.keymetrics.io/docs/usage/cluster-mode/).

---

## Ecosystem File

### Generating an Ecosystem File
To manage multiple applications with PM2, create an Ecosystem File:

```bash
$ pm2 ecosystem
```

This will generate `ecosystem.config.js`:

```javascript
module.exports = {
  apps: [{
    name: "app",
    script: "./app.js",
    env: {
      NODE_ENV: "development",
    },
    env_production: {
      NODE_ENV: "production",
    }
  }, {
    name: 'worker',
    script: 'worker.js'
  }]
};
```

### Starting Applications with Ecosystem File
To start applications defined in the ecosystem file:

```bash
$ pm2 start ecosystem.config.js
```

---

## Setup Startup Script
To generate a startup script that ensures PM2-managed processes restart on server boot/reboot:

```bash
$ pm2 startup
```

To freeze the current process list for automatic respawn:

```bash
$ pm2 save
```

---

## Restart Application on Changes
PM2 can automatically restart applications when file changes are detected:

```bash
$ pm2 start app.js --watch --ignore-watch="node_modules"
```

---

## Updating PM2
To update PM2 to the latest version:

```bash
$ npm install pm2@latest -g
```

Then update the in-memory PM2:

```bash
$ pm2 update
```

---

## PM2 CheatSheet

### Fork Mode
- Start an application in fork mode with a custom name:

  ```bash
  pm2 start app.js --name my-api
  ```

### Cluster Mode Commands
- Start maximum processes based on available CPUs:

  ```bash
  pm2 start app.js -i max
  ```
- Scale application processes:

  ```bash
  pm2 scale app +3
  ```

### Listing Processes
- List all processes and their status:

  ```bash
  pm2 list
  ```

### Logs Management
- Display logs in real-time:

  ```bash
  pm2 logs
  ```

### Process Actions
- Stop all processes:

  ```bash
  pm2 stop all
  ```

- Restart all processes:

  ```bash
  pm2 restart all
  ```

- Delete a specific process:

  ```bash
  pm2 delete 0
  ```

### Miscellaneous Commands
- Reset process metadata:

  ```bash
  pm2 reset <process>
  ```

- Update in-memory PM2:

  ```bash
  pm2 updatePM2
  ```

---

Got it! Here's a hands-on tutorial that guides you through cloning a project repository, setting up Node.js using `nvm`, configuring your environment, and managing the application with PM2 using an `ecosystem.config.js` file.

---

# Hands-on Tutorial

## Table of Contents
1. [Clone the Repository](#step-1-clone-the-repository)
2. [Setup NVM and Install Node.js](#step-2-setup-nvm-and-install-nodejs)
3. [Setup the Project Environment](#step-3-setup-the-project-environment)
4. [Install PM2 and Create `ecosystem.config.js`](#step-4-install-pm2-and-create-ecosystemconfigjs)
5. [Manage the Application with PM2](#step-5-manage-the-application-with-pm2)

---

## Step 1: Clone the Repository

First, clone your project repository using SSH.

```bash
git clone git@github.com:<your-username>/<your-repo>.git
cd <your-repo>
```

Make sure you have set up SSH access to GitHub for private repositories. 

---

## Step 2: Setup NVM and Install Node.js

Now, let's install Node.js using `nvm` to manage different versions of Node.js.

1. **Install `nvm`** (Node Version Manager):

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
```

2. **Load `nvm` into your shell**:

```bash
source ~/.bashrc  # Or ~/.zshrc depending on your shell
```

3. **Install the Node.js version required for your project**:

```bash
nvm install 20  # Replace with the required version
```

4. **Verify the Node.js version**:

```bash
node -v  # Ensure it matches the installed version
```

5. **Set the default Node.js version**:

```bash
nvm use 20
```

---

## Step 3: Setup the Project Environment

1. **Install project dependencies**:

Navigate to the project directory and run:

```bash
npm install
```

2. **Set up environment variables**:

Create a `.env` file and configure the necessary environment variables. For example:

```bash
cp .env.example .env  # Copy the example env file
nano .env  # Modify the variables as needed
```

---

## Step 4: Install PM2 and Create `ecosystem.config.js`

1. **Install PM2 globally**:

```bash
npm install pm2@latest -g
```

2. **Create the `ecosystem.config.js` file**:

Run the following command to generate a basic ecosystem file:

```bash
pm2 ecosystem
```

3. **Modify the `ecosystem.config.js` file**:

Here’s an example structure for the `ecosystem.config.js` file:

```javascript
module.exports = {
  apps: [
    {
      name: 'api',               // Application name
      script: 'ts-node src/index.ts',        // Path to the app entry point
      instances: '1',          
      env: {
        NODE_ENV: 'development', // Environment variables for development
      },
      env_production: {
        NODE_ENV: 'production',  // Environment variables for production
      }
    }
  ]
};
```

- **`instances: 'max'`**: This will start one process per CPU core.


---

## Step 5: Manage the Application with PM2

1. **Start the application using the ecosystem file**:

```bash
pm2 start ecosystem.config.js --env production
```

This will start the app with the production environment. You can specify `--env development` for development.

2. **Monitor the application**:

```bash
pm2 monit
```

3. **Check logs**:

```bash
pm2 logs
```

4. **Save the PM2 process list for automatic startup**:

```bash
pm2 save
```

5. **Set up a startup script** to restart PM2 on server reboot:

```bash
pm2 startup
```

---

## Conclusion

With these steps, you’ve successfully cloned a repository, set up Node.js using `nvm`, installed dependencies, configured the project environment, and managed your application using PM2 with an `ecosystem.config.js` file. PM2 provides easy process management and scaling, making your application robust in production.
