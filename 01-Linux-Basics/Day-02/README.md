# Day 002: Temporary User Setup with Expiry

**Difficulty:** 🟢 Beginner \| **Category:** Linux Security & Identity
Management \| **Status:** ✅ Complete\
**Author:** Dilhan Thilakarathna

---

## 📝 The Challenge

To maintain the principle of least privilege, external accounts (such as
auditors or contractors) must be time-bound. The system administration
team at xFusionCorp Industries requested the creation of a new user
account named `kareem` on **App Server 3**. To ensure compliance and
security, this account must have a strictly enforced account expiration
date of `2027-04-15`.

### 🏗️ Environment Details

-   **Jump Server:** `Thor`
-   **Target Server:** App Server 3 (`stapp03`)
-   **Target User:** `kareem`
-   **Expiry Date:** `2027-04-15`

---

## 🚀 Execution Steps

### 1. Establish Secure Connection (SSH)

``` bash
ssh banner@stapp03
```

### 2. Provision User with Expiry

Using administrative privileges, create the user with an expiration
date:

``` bash
sudo useradd -e 2027-04-15 kareem
```

### 3. Verify the Configuration

``` bash
sudo chage -l kareem
```

**Confirmed Output:**

``` text
Account expires : Apr 15, 2027
```

---

## 🔧 Troubleshooting & Verification

**Verify account aging/expiry**

``` bash
sudo chage -l username
```

**Modify expiry date after creation**

``` bash
sudo chage -E YYYY-MM-DD username
```

**Check user attributes**

``` bash
id username
```

---

## 🧠 Engineering Logic: The Threat of Dormant Accounts

In enterprise environments, third-party contractors and auditors are
frequently granted temporary server access. A common security failure is
**Dormant Account Accumulation**---when a contractor completes their
work, but their account remains active indefinitely.

Malicious actors frequently target these forgotten accounts because they
are rarely monitored. By enforcing the `-e` (expire) flag during user
creation, security becomes automated and fail-safe. Once the expiration
date is reached, the Linux system automatically disables the account,
preventing future logins even if the credentials have been compromised.

---
## 📚 Technical Appendix


🔗 **[View Deep-Dive Notes](./deep-dive.md)**