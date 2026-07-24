# Day 008: Ansible Installation and Configuration

## Objective

The objective of this task is to configure the jump host as an **Ansible Control Node** by installing a specific version of Ansible. This setup transitions the infrastructure from manual server administration to **Infrastructure as Code (IaC)** and centralized configuration management, allowing automated tasks to be executed across all servers simultaneously.

---

## Prerequisites

- Active terminal session on the jump-host logged in as the user `thor`.
- Sudo (administrator) privileges on the jump-host to perform system-wide package installations.
- The Python 3 package installer (`pip3`) available on the system.

---

## Execution Steps

> **Note:** By default, running a standard `pip3 install` only installs the software for the current local user. Using `sudo` ensures the binary is installed globally (for example, in `/usr/local/bin/`), allowing all users on the system to execute Ansible commands.

### 1. Install Ansible Globally

Install the required version of Ansible (`4.10.0`) system-wide using Python's package manager.

```bash
sudo pip3 install ansible==4.10.0
```

**Note:**

If prompted, enter **thor's password** to authorize the system-wide installation.

The installation process may take a minute or two while `pip3` downloads and installs Ansible along with its required Python dependencies.

---

### 2. Verify the Installation

Verify that Ansible has been installed successfully, the correct version is available, and the executable is accessible from the system's global `PATH`.

```bash
ansible --version
```

**Expected Behavior**

You should receive output similar to:

```text
ansible [core 2.11.x]
...
```

> **Note:** Although you install **Ansible 4.10.0**, it is built on top of **ansible-core 2.11.x**, which is why the version output references the core engine.

If the command executes successfully without displaying a **command not found** error, the global installation has been completed successfully.

---

## 📚 Technical Appendix

🔗 **[View Deep-Dive Notes](./deep-dive.md)**