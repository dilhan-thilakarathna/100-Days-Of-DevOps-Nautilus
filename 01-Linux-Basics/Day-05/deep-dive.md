# 📚 Technical Appendix: Day 005

## Learning Objectives

After completing this lab, you will understand:

- What SELinux is and why Linux uses it
- The difference between DAC and MAC
- SELinux operating modes
- Why production servers often run in Permissive mode initially
- How SELinux contexts protect applications
- How to install and configure SELinux packages
- The purpose of each administrative command

# Deep Dive: SELinux Architecture and Real-World Application

## 1. What is SELinux?
SELinux (Security-Enhanced Linux) is an advanced security architecture built directly into the Linux kernel. It acts as a Mandatory Access Control (MAC) system, which is a massive upgrade over the standard Discretionary Access Control (DAC) system that Linux traditionally uses.

* **Standard Linux (DAC):** Security is based on Users, Groups, and Read/Write/Execute permissions (e.g., `chmod 755`). If a user or process has the right file permissions, they can execute the action.
* **SELinux (MAC):** Security is based on strict kernel-level policies and security labels (contexts). Even if standard permissions allow an action, SELinux will block it if the policy rules do not explicitly permit it.

---

## 2. The Three States of SELinux
SELinux operates in one of three distinct modes, controlled via `/etc/selinux/config`:

* **Enforcing:** SELinux is fully active. It actively blocks access and logs any actions that violate its security policies.
* **Permissive:** SELinux is running but does not block anything. It only logs policy violations. This is heavily used by administrators to troubleshoot and test applications before enforcing rules.
* **Disabled:** SELinux is completely turned off and not loaded into the kernel memory.

> **Crucial Rule on Activation:** If SELinux is in a `disabled` state, changing the configuration file to `enforcing` does not activate it immediately. The server **must be rebooted** because the Linux kernel must re-initialize and assign security labels to every file on the hard drive during startup.

---

## 3. The "Install but Disable" Production Strategy
In enterprise environments, it is common to install SELinux packages but configure them to remain `disabled` or `permissive`. 

Because SELinux is notoriously strict, turning it on immediately on a live server will often block legitimate applications from running, causing unexpected downtime. System administrators install the software first, keep it disabled/permissive, and configure the necessary security rules behind the scenes. It is only switched to `enforcing` during a scheduled maintenance window once the policies are verified.

---

## 4. Real-World Scenario: The Nginx Web Server
To understand how SELinux evaluates actions, consider a scenario where developers set up an Nginx web server and place the website files in a custom directory: `/company_data/web/index.html`.

### The 403 Forbidden Error
When SELinux is activated (`enforcing`), users suddenly receive a `403 Forbidden` error when trying to load the website, despite the file having perfect standard Linux permissions.

This happens because of the two-step verification process:
1. **Standard Linux Check:** Nginx asks to read the file. The file has `-rw-r--r--` permissions. Standard Linux allows it.
2. **SELinux Check:** SELinux looks at the security labels. It sees the Nginx process label (`httpd_t`) trying to touch a raw, untrusted directory label (`default_t`). Because the strict policy dictates that web servers can only touch web-specific content, SELinux steps in and **blocks the action**.

### Fixing the Issue with Contexts
To resolve this without disabling SELinux, a system administrator must change the security label of the custom folder to match what SELinux expects for web content:

```bash
# Apply the correct web content security label
chcon -t httpd_sys_content_t /company_data/web/index.html
```

Once the label is updated, SELinux verifies the context and allows Nginx to serve the webpage.

## 5. The Ultimate Benefit of SELinux
The primary purpose of SELinux is damage limitation during a cyberattack.

If a hacker exploits a vulnerability in a web application (like Nginx) and attempts to force the server to read highly sensitive files, like the system password file (/etc/shadow):

Without SELinux: If the web application is compromised, the hacker can leverage its permissions to read the passwords.

With SELinux Active: Even if the application is fully compromised, SELinux detects that the web process label (httpd_t) has absolutely no business touching the password file label (shadow_t). SELinux instantly blocks the read attempt and logs a critical alert, preventing a total system compromise.

---


# Command Reference & Execution Logic

Below is a breakdown of the specific commands utilized to manage SELinux, including the technical reasoning behind each execution.



## Privilege Escalation

```bash
sudo su -
```

### Why it is used

SELinux is a **kernel-level security module**. Standard users do not have the authorization to install packages or modify master configuration files inside the `/etc` directory.

This command:

- Grants full **root (administrator)** privileges.
- Loads the **root user's login environment** using the `-` option.
- Ensures all administrative paths and environment variables are correctly configured before performing system-level tasks.

---

## Package Installation

```bash
yum install -y policycoreutils selinux-policy-targeted
```

### Why it is used

This command installs the essential SELinux management utilities and default security policies.

### Command Breakdown

#### `yum`

- Default package manager for **RHEL/CentOS-based Linux distributions**.
- Downloads and installs software packages together with their dependencies.

#### `policycoreutils`

Provides the core administrative tools required to manage SELinux, including:

- `sestatus`
- `setenforce`
- `getenforce`
- `restorecon`
- `chcon`

#### `selinux-policy-targeted`

Installs the default **Targeted SELinux Policy**, which defines the security rules for common system services such as:

- Apache (`httpd`)
- SSH (`sshd`)
- MySQL/MariaDB
- FTP
- Other standard Linux services

#### `-y`

Automatically answers **"Yes"** to all installation prompts.

This is considered best practice for:

- Automation
- Shell scripting
- Configuration management.

---

## Configuration Automation

```bash
sed -i 's/^SELINUX=.*/SELINUX=disabled/g' /etc/selinux/config
```

### Why it is used

Instead of manually editing the SELinux configuration file with editors such as:

- `vi`
- `vim`
- `nano`

the `sed` (Stream Editor) command performs an automated text replacement directly from the command line.

### Command Breakdown

#### `sed`

A powerful **Stream Editor** used for searching and modifying text.

#### `-i`

Edits the target file **in-place**, meaning the original file is modified directly without creating a temporary output file.

#### `^SELINUX=`

Matches the line that begins exactly with:

```text
SELINUX=
```

The caret (`^`) indicates the beginning of the line.

#### `.*`

A regular expression meaning:

> Match every remaining character on that line.

This allows the command to replace any existing value, such as:

```text
SELINUX=enforcing
```

or

```text
SELINUX=permissive
```

#### `SELINUX=disabled`

Replaces the matched line with:

```text
SELINUX=disabled
```

This configures SELinux to be disabled after the next system reboot.

---

## State Verification

```bash
cat /etc/selinux/config | grep SELINUX=
```

### Why it is used

A fundamental engineering practice is to **verify every configuration change** before ending an administrative session.

### Command Breakdown

#### `cat`

Displays the entire contents of:

```text
/etc/selinux/config
```

#### `|` (Pipe)

Passes the output from one command directly into another command.

#### `grep SELINUX=`

Filters the output and displays only the lines containing:

```text
SELINUX=
```

### Example Output

```text
SELINUX=disabled
SELINUXTYPE=targeted
```

This confirms that the configuration file has been updated successfully.

---

## Security Context Modification (Bonus Scenario)

```bash
chcon -t httpd_sys_content_t /company_data/web/index.html
```

### Why it is used

When SELinux is running in **Enforcing Mode**, standard Linux file permissions (`rwx`) alone are not sufficient.

SELinux also checks the **security context (label)** assigned to every file.

The `chcon` command modifies that security label so that the web server is authorized to access the file.

### Command Breakdown

#### `chcon`

Stands for **Change Context**.

It changes the SELinux security context of a file or directory.

#### `-t`

Specifies the **SELinux Type** to assign.

#### `httpd_sys_content_t`

This SELinux type identifies files as **approved web content**.

Files with this label can be safely read by the Apache HTTP Server (`httpd`).

#### `/company_data/web/index.html`

The target file whose SELinux security label is being modified.

### Result

After applying this context:

- Apache can read the file.
- SELinux no longer blocks access.
- The web server can successfully serve the content while SELinux remains enabled.

---

## Summary

| Command | Purpose |
|----------|---------|
| `sudo su -` | Switch to the root user with a full login environment |
| `yum install -y policycoreutils selinux-policy-targeted` | Install SELinux utilities and default security policies |
| `sed -i 's/^SELINUX=.*/SELINUX=disabled/g' /etc/selinux/config` | Automatically disable SELinux in the configuration file |
| `cat /etc/selinux/config \| grep SELINUX=` | Verify the SELinux configuration |
| `chcon -t httpd_sys_content_t /company_data/web/index.html` | Assign the correct SELinux security context for Apache web content |