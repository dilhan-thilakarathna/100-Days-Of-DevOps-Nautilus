# 📅 Day 001: Linux User Setup with Non-Interactive Shell

**Difficulty:** 🟢 Beginner | **Category:** Linux Security | **Status:** ✅ Complete
**Author:** Dilhan Thilakarathna 

---

## 📝 The Challenge
Project Nautilus Marine requires a secure backup solution. The system administration team at xFusionCorp Industries requested the creation of a new service account named `anita` on **App Server 1**. Because this account will be used exclusively by an automated backup agent tool, it must be secured with a non-interactive shell to prevent unauthorized human access.

### 🏗️ Environment Details
* **Jump Server:** `Thor`
* **Target Server:** App Server 1 (`stapp01`)
* **Target User:** `tony`

---

## 🚀 Execution Steps

### 1. Establish Secure Connection (SSH)
Initiated a secure tunnel from the `Thor` jumphost to the target application server using the provided credentials.

```bash
ssh tony@stapp01
```

### 2. Implement Least Privilege User Creation
Using administrative privileges, I created the user. The `-s` flag was utilized to explicitly define the shell path, assigning `/sbin/nologin` instead of a standard interactive shell like `/bin/bash`.

```bash
sudo useradd -s /sbin/nologin anita
```

### 3. Verify the Configuration
To ensure the user was created and the shell trap was successfully set, I audited the system's password file.

```bash
grep anita /etc/passwd
```
**Confirmed Output:** `anita:x:1001:1001::/home/anita:/sbin/nologin`

---

## 🔧 Troubleshooting & Verification
When managing users across Linux systems, these commands are vital for rapid auditing:
* **Check existing users:** `cat /etc/passwd | grep username`
* **List all service accounts with trapped shells:** `grep nologin /etc/passwd`
* **Verify user UID/GID details:** `id username`

---

## 🧠 Engineering Logic: Why `/sbin/nologin`?
Service accounts are designed for software and automated background tasks, not human interaction. If a malicious actor manages to compromise the credentials or SSH keys of a service account, the `/sbin/nologin` shell acts as an immediate trapdoor. 

Instead of dropping the attacker into an interactive terminal where they can execute commands and pivot through the network, the system immediately terminates the connection. This fundamental server hardening technique significantly minimizes the attack surface of the infrastructure and ensures the principle of least privilege is maintained.

---
## 📚 Technical Appendix


🔗 **[View Deep-Dive Notes](./deep-dive.md)**