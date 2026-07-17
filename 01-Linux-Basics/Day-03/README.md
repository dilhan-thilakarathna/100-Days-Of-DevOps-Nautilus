# 📅 Day 003: Disabling Root SSH Access

**Difficulty:** 🟢 Beginner | **Category:** Linux Security & Hardening | **Status:** ✅ Complete
**Author:** Dilhan Thilakarathna

---

## 📝 The Challenge
Following a comprehensive security audit at xFusionCorp Industries, the security team has mandated that direct SSH access for the `root` user must be disabled on all application servers in the Stratos Datacenter. This protocol reduces the risk of automated brute-force attacks targeting the most privileged account on the system.

### 🏗️ Environment Details
* **Jump Server:** `thor`
* **Target Servers:** `stapp01`, `stapp02`, `stapp03`
* **Configuration File:** `/etc/ssh/sshd_config`

---

## 🚀 Execution Steps

### 1. Establish Secure Connection
Access the target server from the Jump Host:
```bash
ssh [username]@[server-name]
```

### 2. Modify SSH Configuration
Open the SSH daemon configuration file with root privileges:

```bash
sudo vi /etc/ssh/sshd_config
```
Locate the PermitRootLogin directive. Change it to:

```bash
PermitRootLogin no
```

### 3. Verify Syntax (Pre-Flight Check)
Before restarting, always check the configuration for syntax errors to avoid locking yourself out:

```bash
sudo sshd -t
```

### 4. Restart the SSH Service
Apply the changes by restarting the SSH daemon:

```bash
sudo systemctl restart sshd
```

### 5. Verification
Confirm the service is running correctly:

```bash
sudo systemctl status sshd
```
---

## 🧠 Engineering Logic: Why restrict Root?

The `root` account is the primary target for attackers because it provides absolute control over the operating system. By setting `PermitRootLogin no`, you force attackers to:

**Obscure the target:** Attackers must guess a valid standard username (which is significantly harder than simply trying "root").

**Escalate privileges:** Attackers must escalate privileges using `sudo` after a successful login, which requires knowing the user's password.

This creates a necessary barrier that protects the system from unauthorized elevated access.
---
---
## 📚 Technical Appendix


🔗 **[View Deep-Dive Notes](./deep-dive.md)**