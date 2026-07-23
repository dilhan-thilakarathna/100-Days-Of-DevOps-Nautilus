# Day 007: Linux SSH Authentication

## Objective

The objective of this task is to configure **passwordless SSH authentication** from the user `thor` on the jump host to all application servers in the Stratos Datacenter through their respective sudo users. This setup is a critical foundation for infrastructure automation, allowing background scripts to execute operations across multiple servers securely without requiring manual password entry.

---

## Prerequisites

- Active terminal session on the jump-host logged in as the user `thor`.
- Network connectivity to the three application servers (`stapp01`, `stapp02`, and `stapp03`).
- Passwords for the target application server users (`tony`, `steve`, and `banner`).

---

## Execution Steps

> **Note:** The SSH key pair is generated **only once** on the source machine (the jump host). The generated public key is then distributed to each individual target server.

### 1. Generate the SSH Key Pair

Create a cryptographic key pair (RSA) for the `thor` user on the jump host.

```bash
ssh-keygen -t rsa
```

**Note:**  
When prompted for a file location, press **Enter** to accept the default path:

```text
/home/thor/.ssh/id_rsa
```

When prompted for a passphrase, **leave it blank** and press **Enter** twice.

A blank passphrase allows automation tools and background scripts to authenticate without requiring manual password input.

---

### 2. Distribute the Public Key to App Server 1

Copy the generated public key to **App Server 1** for the user `tony`.

```bash
ssh-copy-id tony@stapp01
```

If prompted:

- Type `yes` to confirm the server's authenticity.
- Enter **tony's password** to complete the installation.

---

### 3. Distribute the Public Key to App Server 2

Repeat the process for **App Server 2** using the user `steve`.

```bash
ssh-copy-id steve@stapp02
```

Enter **steve's password** when prompted.

---

### 4. Distribute the Public Key to App Server 3

Repeat the process for **App Server 3** using the user `banner`.

```bash
ssh-copy-id banner@stapp03
```

Enter **banner's password** when prompted.

---

### 5. Verify the Configuration

Verify that passwordless authentication has been configured successfully by connecting to the application servers.

```bash
ssh tony@stapp01
```

**Expected Behavior**

You should immediately receive a shell prompt similar to:

```text
[tony@stapp01 ~]$
```

No password should be requested.

Exit the session after verification:

```bash
exit
```

> **Note:** Always use `exit` to close the SSH session and return to the `thor@jump-host` terminal before testing the next server or continuing with other tasks.

---

## 📚 Technical Appendix

🔗 **[View Deep-Dive Notes](./deep-dive.md)**