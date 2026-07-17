# 📚 Technical Appendix: Day 003

## 🔍 Understanding the SSH Configuration Files
A common pitfall for new engineers is confusing the two primary SSH configuration files located in `/etc/ssh/`.

*   `/etc/ssh/ssh_config`: This is the **Client** configuration file. It dictates how *your* server behaves when making outgoing connections to other servers.
*   `/etc/ssh/sshd_config`: This is the **Daemon (Server)** configuration file. It dictates how your server handles *incoming* connections. (The 'd' stands for daemon). **This is the file you harden.**

## 🛡️ Deep Dive: `PermitRootLogin` Options
While our task required setting this to `no`, it is important to understand the other states this directive can hold in a production environment.

*   `yes`: Allows root to log in using a password or SSH key. (Highly insecure, rarely used in production).
*   `no`: Completely blocks root from logging in via SSH, regardless of authentication method. (Most secure).
*   `prohibit-password` (or `without-password`): A very common middle-ground in enterprise DevOps. This blocks root login via password but allows root login if the user has a valid **SSH Key**. This is often used to allow automation tools (like Ansible or Jenkins) to configure the server securely without exposing the root password to brute-force attacks.

## 🛠️ The Importance of `sshd -t` (Test Mode)
The command `sudo sshd -t` is a lifesaver. 

*   The `-t` flag stands for **Test mode**.
*   It reads the configuration file and checks for syntax errors, invalid options, or missing keys.
*   If you make a typo (e.g., typing `PermitRootLogin nno`) and restart the service without testing, the SSH daemon will crash. If you are logged out, you are permanently locked out of the server until you have physical (or console) access to it.
*   **Rule of Thumb:** Never run `systemctl restart sshd` without running `sshd -t` first.

## ⚙️ Service Management: `systemctl`
Modern Linux distributions (like CentOS 7+, Ubuntu 16.04+, and RedHat) use `systemd` as their init system—the first process that runs and manages all other processes.

*   `systemctl` is the command-line tool used to interact with `systemd`.
*   `restart sshd`: Stops the SSH process and starts it again, forcing it to read the newly modified `sshd_config` file from the disk.
*   `reload sshd`: An alternative to `restart`. It tells the service to reload its configuration files without dropping existing connections. In production, `reload` is often preferred over `restart` because it won't disconnect active users.