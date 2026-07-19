# Day 005: SELinux Installation and Configuration

## Objective
Following a security audit by the xFusionCorp Industries security team, the objective of this task is to enhance server security capabilities by installing SELinux packages on App Server 2 (`stapp02`). Additionally, the task requires configuring the SELinux state to be permanently `disabled` upon the next scheduled maintenance reboot.

## Prerequisites
* Active SSH connection to the jump-host.
* Credentials for App Server 2 (`stapp02`).
* Root privileges (`sudo`) on the target server.

## Execution Steps

### 1. Connect to Target Server
Establish an SSH connection to App Server 2 from the jump-host:
```bash
ssh steve@stapp02