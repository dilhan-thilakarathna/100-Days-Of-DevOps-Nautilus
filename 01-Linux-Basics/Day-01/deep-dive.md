# 📚 Technical Appendix: Day 001

## Learning Objectives

After completing this lab, you will understand:

- What a Linux shell is and how it works
- The difference between interactive and non-interactive shells
- The purpose of `/sbin/nologin` and `/bin/false`
- The Principle of Least Privilege (PoLP)
- How non-interactive shells improve server security
- The structure of the `/etc/passwd` file
- How to create a system account with a non-interactive shell

---

## Deep Dive: System Accounts and Non-Interactive Shells

### 1. The Anatomy of a Shell in Linux

In Linux, a **shell** is the program that accepts user commands and passes them to the operating system for execution.

- **Interactive Shells (`/bin/bash`, `/bin/zsh`)**
  - Designed for human users.
  - Display a command prompt (`$`).
  - Allow users to navigate the filesystem and execute commands.

- **Non-Interactive Shells (`/sbin/nologin`, `/bin/false`)**
  - Designed primarily for service and system accounts.
  - Do not provide a command prompt.
  - Immediately reject login attempts and terminate the session.

---

### 2. `/sbin/nologin` vs. `/bin/false`

When securing service accounts, administrators commonly choose between two non-interactive shells.

#### `/bin/false`

- Immediately exits.
- Returns a non-zero (failure) exit status.
- Displays no message to the user.
- Commonly used when absolutely no login should ever succeed.

#### `/sbin/nologin`

- Rejects interactive logins.
- Displays a friendly message before closing the session.
- Default message:

```text
This account is currently not available.
```

Administrators can customize this message by editing:

```text
/etc/nologin.txt
```

Because it provides feedback to legitimate users, `/sbin/nologin` is generally preferred for service accounts.

---

### 3. The Principle of Least Privilege (PoLP)

Using a non-interactive shell is a practical implementation of the **Principle of Least Privilege (PoLP)**.

For example:

A backup account such as **`anita`** only needs permission to:

- Read backup directories
- Transfer backup files
- Execute automated background jobs

It does **not** require the ability to:

- Browse directories
- Execute arbitrary commands
- Start an interactive shell session

Removing interactive shell access grants only the permissions necessary for the account's intended purpose, reducing the system's attack surface.

---

### 4. Real-World Scenario: The Compromised Backup Agent

Suppose **xFusionCorp Industries** uses a backup service that authenticates with the **`anita`** account using an SSH key every night.

### Attack Scenario

A developer accidentally commits the private SSH key to a public GitHub repository.

An attacker downloads the key and attempts to connect:

```bash
ssh -i stolen_key.pem anita@appserver.xfusioncorp.com
```

### Outcome

#### If `anita` uses `/bin/bash`

The attacker successfully receives a shell prompt and may:

- Read sensitive configuration files
- Install malware
- Pivot to other internal servers
- Attempt privilege escalation

#### If `anita` uses `/sbin/nologin`

Authentication succeeds, but Linux immediately launches:

```text
/sbin/nologin
```

The attacker receives:

```text
This account is currently not available.
```

The connection is immediately terminated, preventing interactive access.

---

### 5. Understanding the `/etc/passwd` File

To verify the account configuration, inspect the `/etc/passwd` file.

Example entry:

```text
anita:x:1001:1001::/home/anita:/sbin/nologin
```

Each field has a specific purpose.

| Field | Value | Description |
|--------|-------|-------------|
| Username | `anita` | Login name |
| Password | `x` | Password hash is stored securely in `/etc/shadow` |
| UID | `1001` | Unique User ID |
| GID | `1001` | Primary Group ID |
| GECOS | *(blank)* | Optional user information |
| Home Directory | `/home/anita` | User's home directory |
| Login Shell | `/sbin/nologin` | Default shell executed during login |

---

## Command Reference & Execution Logic

Below is a breakdown of the command used in this lab and the reasoning behind its execution.

---

### Create a User with a Non-Interactive Shell

```bash
sudo useradd -s /sbin/nologin anita
```

#### Why it is used

This command creates a new user account named **`anita`** and assigns **`/sbin/nologin`** as the default login shell.

#### Command Breakdown

##### `sudo`

Executes the command with administrative privileges.

##### `useradd`

Creates a new Linux user account.

##### `-s`

Specifies the user's default login shell.

##### `/sbin/nologin`

Assigns a non-interactive shell that immediately rejects login attempts while allowing the account to be used by background services and automated processes.

##### `anita`

The username being created.

#### Result

After execution:

- The `anita` account is created.
- Interactive logins are disabled.
- Background services can still use the account where appropriate.
- The account follows the Principle of Least Privilege by preventing unnecessary shell access.

---

## Summary

| Command | Purpose |
|---------|---------|
| `sudo useradd -s /sbin/nologin anita` | Creates a user with a non-interactive login shell to prevent interactive access while allowing service operations. |

---

## Key Takeaways

- Linux uses shells to provide command-line access.
- Interactive shells are intended for human users.
- Non-interactive shells protect service accounts from direct logins.
- `/sbin/nologin` provides a user-friendly login rejection message.
- `/bin/false` immediately exits without displaying a message.
- Service accounts should follow the Principle of Least Privilege.
- The `/etc/passwd` file stores account configuration, including the user's default shell.