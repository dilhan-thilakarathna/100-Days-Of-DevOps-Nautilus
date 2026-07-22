# Day 005: SELinux Installation and Configuration

## Objective
Following a security audit by the xFusionCorp Industries security team, the objective of this task is to enhance server security capabilities by installing SELinux packages on App Server 2 (`stapp02`). Additionally, the task requires configuring the SELinux state to be permanently `disabled` upon the next scheduled maintenance reboot.

## Prerequisites
* Active SSH connection to the jump-host.
* Credentials for App Server 2 (`stapp02`).
* Root privileges (`sudo`) on the target server.

## Execution Steps

### 1. Connect to Target Server
Establish an SSH connection to App Server 2 from the jump-host:
```bash
ssh steve@stapp02
```

### 2. Escalate Privileges
Switch to the root user to allow package installation and kernel-level configuration changes:

```bash
sudo su -
```

### 3. Install SELinux Packages
Install the core utilities and targeted policies required for SELinux operations using the yum package manager:

```bash
yum install -y policycoreutils selinux-policy-targeted
```

### 4. Permanently Disable SELinux
#### Option A: The Manual Route (vi)

```bash
vi /etc/selinux/config
```
Find the line that says `SELINUX=enforcing` (or `SELINUX=permissive`), change it to `SELINUX=disabled`, then save and exit (Esc, `:wq`, Enter).

#### Option B: The Automation Route (sed)
Editing configuration files dynamically is a core automation competency. You can use the Stream Editor (sed) to instantly search for the SELINUX= line and replace it with disabled without ever opening a text editor.

```bash
sed -i 's/^SELINUX=.*/SELINUX=disabled/g' /etc/selinux/config
```

### 5. Verify Configuration
Read the configuration file to confirm the modification was successfully applied:

```bash
cat /etc/selinux/config | grep SELINUX=
```

Expected Output: `SELINUX=disabled`

Note: Per the security team's instructions, no manual reboot was executed (reboot), and the live runtime status was intentionally left unmodified (setenforce 0 was not used). The server will naturally inherit the disabled state during its scheduled maintenance window tonight.