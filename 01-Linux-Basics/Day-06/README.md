# Day 006: Automating Tasks with Cron Jobs

## Objective
The objective of this task is to establish time-based automation across three application servers in the Stratos Data Center. To ensure consistent background execution of automated tasks without human intervention, this requires installing the **cronie** package, starting the **crond** daemon, and configuring a recurring cron job that writes a specific output every five minutes.

## Prerequisites

- Active SSH connection to the jump-host.
- Credentials for the three application servers (`stapp01`, `stapp02`, and `stapp03`).
- Root privileges (`sudo`) on the target servers.

## Execution Steps

> **Note:** These steps must be repeated on all three application servers. In a production environment, this process would typically be automated using a configuration management tool such as **Ansible**.

### 1. Connect to the Target Server

Establish an SSH connection to one of the application servers (example: App Server 1).

```bash
ssh tony@stapp01
```

---

### 2. Escalate Privileges

Switch to the root user to perform package installation and service management.

```bash
sudo su -
```

---

### 3. Install the Cron Package

Install the **cronie** package, which provides the cron daemon and scheduling utilities.

```bash
yum install -y cronie
```

---

### 4. Start and Enable the Cron Service

Start the cron daemon immediately and configure it to start automatically whenever the server boots.

```bash
systemctl start crond
systemctl enable crond
```

---

### 5. Schedule the Cron Job

Open the root user's crontab.

```bash
crontab -e
```

Add the following entry:

```cron
*/5 * * * * echo "hello" > /tmp/cron_text
```

This schedules the command to run **every five minutes**, writing the text **"hello"** into `/tmp/cron_text`.

Save and exit the editor.

---

### 6. Verify Configuration

Verify that the cron job has been successfully added.

```bash
crontab -l
```

Expected Output:

```cron
*/5 * * * * echo "hello" > /tmp/cron_text
```

Wait until the next five-minute interval, then verify that the scheduled task executed successfully.

```bash
cat /tmp/cron_text
```

Expected Output:

```text
hello
```

> **Note:** The cron daemon executes jobs according to the system clock. If the output file does not exist immediately after creating the job, wait until the next scheduled five-minute interval before verifying.

---

## 📚 Technical Appendix

🔗 **[View Deep-Dive Notes](./deep-dive.md)**