# 📚 Technical Appendix: Day 004

## Linux File Permissions & `chmod`

In Linux, everything is considered a file (even hardware devices and directories). Because of this, mastering how to control access to files is one of the most critical security skills for a Sysadmin or DevOps Engineer.

---

## 🔍 Reading the Permission Block
When you run `ls -l`, you see a 10-character string (e.g., `-rwxr-xr-x`). Here is how to decode it:

1. **Character 1 (File Type):** 
   * `-` means it is a regular file.
   * `d` means it is a directory (folder).
2. **Characters 2-4 (User/Owner):** Permissions for the user who owns the file.
3. **Characters 5-7 (Group):** Permissions for users in the file's designated group.
4. **Characters 8-10 (Others):** Permissions for everyone else on the system.

The permissions themselves are always in the same order: **Read (`r`), Write (`w`), and Execute (`x`)**. If a permission is denied, it is represented by a hyphen (`-`).

---

## 🛠️ The Two Ways to Change Permissions
We use the `chmod` (change mode) command to modify these permissions. There are two standard ways to write this command: **Symbolic** (letters) and **Numeric/Octal** (numbers).

### 1. Symbolic Notation (The Letter Method)
This method is great for making precise, targeted changes without affecting the rest of the permissions.

* **Who to change:** `u` (user), `g` (group), `o` (others), `a` (all)
* **What to do:** `+` (add), `-` (remove), `=` (set exactly to)
* **Which permission:** `r` (read), `w` (write), `x` (execute)

**Examples:**
* `chmod a+rx file.sh` (Adds read and execute for everyone)
* `chmod o-w file.txt` (Removes write permission from 'others')
* `chmod u=rwx file.py` (Sets the owner's permissions to exactly read, write, and execute)

### 2. Numeric/Octal Notation (The Binary Math Method)
This is the method preferred by most senior engineers because it allows you to set all 9 permission bits at once using three numbers. It is based entirely on binary mathematics.

| Permission | Binary State | Math | Decimal Value |
| :--- | :--- | :--- | :--- |
| **Read (r)** | `1 0 0` | 2^2 | **4** |
| **Write (w)** | `0 1 0` | 2^1 | **2** |
| **Execute (x)** | `0 0 1` | 2^0 | **1** |

Because 4, 2, and 1 are powers of two, adding them together creates a mathematically unique sum. The computer reads the final number and knows exactly which permissions are ON or OFF.

**Common Combinations:**
* **7** (4 + 2 + 1) = Read, Write, Execute (`rwx`)
* **6** (4 + 2 + 0) = Read, Write (`rw-`)
* **5** (4 + 0 + 1) = Read, Execute (`r-x`)
* **0** (0 + 0 + 0) = No permissions (`---`)

**Example:**
`chmod 755 script.sh` 
* User gets 7 (`rwx`)
* Group gets 5 (`r-x`)
* Others get 5 (`r-x`)

---

## ⚠️ The "Gotcha": Scripts vs. Compiled Binaries
During Day 4, granting *only* execute permissions (`chmod a+x`) to `xfusioncorp.sh` caused an execution failure. 

**Why?**
There is a fundamental difference in how Linux handles compiled programs (like `ls` or `cat`) versus script files (like Bash or Python scripts).
* A **compiled binary** is already translated into machine code. Linux just needs the Execute (`x`) permission to load it into memory and run it.
* A **script** is just a plain text file. When you tell Linux to execute it, the system must first open the file and **read** the text line-by-line using an interpreter (like `/bin/bash`). 

Therefore, if you grant a script Execute permissions but deny Read permissions, the system cannot see the commands inside to run them. **Scripts must always have both Read and Execute (`+rx` or `5`) permissions to function.**