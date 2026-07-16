---

# 📚 Technical Appendix

## 🔍 Core Utilities: `useradd` vs `chage`

### 1. `useradd -e` (The Creation Phase)

The `-e` flag is the most efficient way to assign an account expiration date **at the time the user is created**.

**Syntax**

```bash
useradd -e YYYY-MM-DD username
```

> **Note:** The expiration date must follow the **ISO 8601** format: `YYYY-MM-DD`.

---

### 2. `chage` (The Management Phase)

The `chage` (**change age**) command is the standard Linux utility used to **view and modify account aging policies** after a user has already been created.

#### Common Options

**Display current account aging information**

```bash
chage -l username
```

**Set or modify an account expiration date**

```bash
chage -E YYYY-MM-DD username
```

---

# 🛡️ Best Practices for Temporary Access

- **Use Individual Accounts**
  - Always create **individual user accounts** (e.g., `kareem`) instead of shared accounts.
  - This ensures **accountability** and **non-repudiation**, making it possible to identify exactly who performed each action in system audit logs.

- **Verify Immediately**
  - After creating a temporary account, always verify the configuration using:

    ```bash
    chage -l username
    ```

  - This confirms that the intended expiration date has been correctly applied before granting access.

---