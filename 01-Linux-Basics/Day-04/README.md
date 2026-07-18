# Day 004: Managing Linux File Permissions

**Difficulty:** 🟢 Beginner | **Category:** Linux Security & Administration | **Status:** ✅ Complete
**Author:** Dilhan Thilakarathna

---

## 📝 The Challenge
In a bid to automate backup processes, the xFusionCorp Industries sysadmin team developed a new bash script named `xfusioncorp.sh`. While the script was distributed to the necessary servers, it lacked the required executable permissions on App Server 2 within the Stratos Datacenter. 

The task was to grant execute permissions to the script and ensure that all users on the system had the capability to run it successfully.

### 🏗️ Environment Details
* **Jump Host:** `thor`
* **Target Server:** `stapp02` (App Server 2)
* **Target File:** `/tmp/xfusioncorp.sh`
* **Assigned User:** `steve`

---

## 🚀 Execution Steps

### 1. Connect to the Target Server
Securely shell into App Server 2 using the assigned credentials:
```bash
ssh steve@stapp02
```
### 2. Inspect Current File Permissions
Before applying modifications, inspect the current state of the file:

```bash
sudo ls -l /tmp/xfusioncorp.sh
```
Output: `---------- 1 root root 40 Jul 18 10:28 /tmp/xfusioncorp.sh` (Zero permissions assigned).

### 3. Grant Read and Execute Permissions
To allow all users to execute a bash script, they must be granted both Read (`r`) and Execute (`x`) privileges. Using symbolic notation:

```bash
sudo chmod a+rx /tmp/xfusioncorp.sh
```
(Note: Alternatively, numeric/octal notation `sudo chmod 755 /tmp/xfusioncorp.sh` or `sudo chmod 555 /tmp/xfusioncorp.sh` achieves the same operational goal).

### 4. Verify the Modification
Confirm the permissions were successfully applied across all user categories (Owner, Group, Others):

```bash
sudo ls -l /tmp/xfusioncorp.sh
```
Output: `-r-xr-xr-x 1 root root 40 Jul 18 10:28 /tmp/xfusioncorp.sh`

## 🧠 Engineering Logic: chmod and Script Execution
In Linux, file permissions dictate security boundaries. By default, newly created files do not possess execution rights (`x`) to prevent malicious or accidental code execution. The administrator must explicitly declare a file as an executable program using the `chmod` utility.

The "Gotcha" (Read vs. Execute for Scripts):
Unlike compiled binary programs, a Bash script is a plain text file containing a sequence of commands. When a user attempts to execute a script, the Linux system must physically open and read the contents line-by-line to execute them. If a file is only granted Execute (`x`) permissions but denied Read (`r`) permissions, the execution will fail because the system cannot see the commands inside. Therefore, adding `+rx` (or octal `5`) is mandatory for scripts.

---
## 📚 Technical Appendix


🔗 **[View Deep-Dive Notes](./deep-dive.md)**