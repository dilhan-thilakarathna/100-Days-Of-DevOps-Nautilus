# 📚 Technical Appendix: Day 007

## Learning Objectives

After completing this lab, you will understand:

- What SSH key-based authentication is and why it is crucial for infrastructure automation
- The distinct differences and roles of private keys and public keys
- How to generate cryptographic key pairs using `ssh-keygen`
- How to automate public key distribution using `ssh-copy-id`
- The security architecture of a Jump Host (Bastion Host)
- How Linux manages authorized keys and secure file permissions

---

# Deep Dive: Public Key Authentication and Infrastructure Security

## 1. What is SSH Key Authentication?

SSH (Secure Shell) key authentication is a cryptographic method used to verify a user's identity when connecting to a remote Linux server. Instead of relying on a password that a human must remember and type, SSH uses a pair of mathematically related cryptographic keys.

- **Password Authentication:** Requires manual password entry, is susceptible to brute-force attacks, and prevents unattended automation.
- **SSH Key Authentication:** Provides seamless, highly secure authentication that enables automation tools, scripts, CI/CD pipelines, and configuration management systems (such as Ansible) to securely access remote servers without human intervention.

---

## 2. The Lock and Key Analogy

SSH authentication is best understood as a lock-and-key mechanism based on **asymmetric cryptography**.

### The Private Key (`id_rsa`) — The Master Key

- Represents your digital identity.
- Remains only on the source machine.
- Must **never** be copied, shared, or transmitted.
- In this lab, it remains securely stored on the Jump Host.

### The Public Key (`id_rsa.pub`) — The Padlock

- Safe to distribute freely.
- Installed on every remote server that should trust the client.
- Stored inside the remote user's:

```text
~/.ssh/authorized_keys
```

During authentication, the SSH server verifies that the connecting client possesses the matching private key without ever transmitting that private key across the network.

---

## 3. The Automation Exception: Blank Passphrases

Normally, SSH private keys should be protected with a **passphrase**.

This creates a second layer of security in case the private key file is stolen.

However, infrastructure automation presents a unique exception.

Automation tools such as:

- Ansible
- Jenkins
- GitHub Actions
- CI/CD Pipelines
- Scheduled Cron Jobs

cannot stop and wait for a human to type a passphrase.

For this reason, automation keys are commonly generated **without a passphrase**.

> **Important:** Because the private key itself is no longer encrypted, protecting the Jump Host becomes absolutely critical.

---

## 4. Real-World Scenario: The Jump Host Architecture

A **Jump Host** (also called a **Bastion Host**) acts as the secure gateway into an organization's private infrastructure.

### The Security Problem

Imagine a Jump Host that stores passwordless SSH keys for every production server.

If an attacker compromises that Jump Host, they immediately gain trusted access to every connected server.

### The Defense-in-Depth Solution

Professional cloud environments mitigate this risk using multiple security layers.

#### Network Isolation

Application servers reside in **private subnets** without direct Internet access.

Only the Jump Host has a public-facing IP address.

#### Strict Firewall Rules

Only trusted corporate VPN addresses are allowed to reach the Jump Host.

All other inbound traffic is denied.

#### Password Elimination

Password authentication is completely disabled.

Only trusted SSH keys are accepted.

#### Key Rotation

Organizations periodically regenerate SSH keys and replace old ones to minimize the impact of stolen credentials.

---

## Command Reference & Execution Logic

Below is a breakdown of the specific commands utilized to configure passwordless SSH authentication, including the technical reasoning behind each execution.

---

## Key Pair Generation

```bash
ssh-keygen -t rsa
```

### Why it is used

This command generates the cryptographic key pair required for SSH authentication.

By accepting the default prompts, Linux stores the keys inside the user's hidden SSH directory:

```text
~/.ssh/
```

The private key is saved as:

```text
id_rsa
```

The public key is saved as:

```text
id_rsa.pub
```

A blank passphrase is intentionally used to support unattended automation.

### Command Breakdown

#### `ssh-keygen`

The standard Linux utility used to create, manage, and convert SSH authentication keys.

#### `-t`

Specifies the cryptographic algorithm to use.

#### `rsa`

Generates an RSA public/private key pair.

RSA remains one of the most widely recognized public-key cryptography algorithms.

---

## Key Distribution

```bash
ssh-copy-id tony@stapp01
```

### Why it is used

Manually configuring SSH authentication involves several tedious and error-prone steps:

- Creating the `.ssh` directory
- Setting directory permissions (`700`)
- Creating the `authorized_keys` file
- Setting file permissions (`600`)
- Copying the public key correctly

The `ssh-copy-id` utility automates this entire process safely.

### Command Breakdown

#### `ssh-copy-id`

Copies the local public key to the remote server and appends it to:

```text
~/.ssh/authorized_keys
```

It also creates missing directories and applies the correct permissions automatically.

#### `tony@stapp01`

The destination server.

Format:

```text
user@hostname
```

During the initial execution, the remote user's password is required one final time to authorize installation of the SSH key.

---

## Connection Verification

```bash
ssh tony@stapp01
```

### Why it is used

This command verifies that passwordless authentication has been configured successfully.

If everything is configured correctly:

- SSH negotiates authentication using the RSA keys.
- No password prompt appears.
- The user immediately receives a shell prompt.

### Command Breakdown

#### `ssh`

The Secure Shell client used to establish encrypted remote connections.

#### `tony@stapp01`

Instructs SSH to connect as user `tony` on host `stapp01`.

---

## Session Termination

```bash
exit
```

### Why it is used

Terminates the active SSH session and returns the user to the previous terminal (the Jump Host).

This is considered good operational practice when managing multiple remote servers.

---

## Summary

| Command | Purpose |
|----------|---------|
| `ssh-keygen -t rsa` | Generate a new RSA public/private key pair |
| `ssh-copy-id user@host` | Securely install the local public key on a remote server |
| `ssh user@host` | Connect to a remote server and verify passwordless authentication |
| `exit` | Close the current SSH session and return to the previous terminal |

---

## Key Takeaways

- SSH keys provide a significantly more secure alternative to password authentication.
- The **private key (`id_rsa`)** must always remain secret.
- The **public key (`id_rsa.pub`)** is safe to distribute to trusted servers.
- Passwordless SSH keys (blank passphrases) are essential for infrastructure automation.
- `ssh-copy-id` automates secure public key installation while correctly configuring Linux permissions.
- Jump Hosts should be heavily secured because they store trusted authentication credentials for the entire infrastructure.