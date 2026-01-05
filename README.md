# Jenkins Backup and Restore Using ThinBackup

## Overview

how to **back up and restore Jenkins data** using the **ThinBackup plugin** on an AWS EC2–hosted Jenkins server.
It covers plugin installation, backup directory preparation, scheduling, manual backup, and restore procedures.

---

## A. Preliminaries — SSH Access from Windows

1. Open **Git Bash / Git CMD / Command Prompt**
2. Navigate to the directory containing your **.pem key**
3. Connect to the Jenkins EC2 instance:

   ```bash
   ssh -i "Pem.file" ubuntu@ec2-IP.ap-south-1.compute.amazonaws.com
   ```
4. Open a browser and log in to Jenkins:

   ```
   http://<EC2-IP>:8080
   ```

---

## B. Install ThinBackup Plugin

1. Log in to Jenkins as **Admin**
2. Go to:

   ```
   Manage Jenkins → Manage Plugins → Available
   ```
3. Search for **ThinBackup**
4. Select **ThinBackup**
5. Click **Install without restart** (or *Download now and install after restart*)
6. Wait until the message appears:

   ```
   Installation successful: thinBackup
   ```
7. Confirm the plugin is visible under:

   ```
   Manage Jenkins → Manage Plugins → Installed
   ```

---

## C. Locate ThinBackup Configuration

ThinBackup settings are **NOT** on the plugin page.

Navigate to:

```
Manage Jenkins → Configure System → Scroll down → ThinBackup
```

---

## D. Prepare Backup Directory on Jenkins Host

> This must be a **directory**, not a file.

1. SSH into the Jenkins server
2. Switch to root:

   ```bash
   sudo -i
   ```
3. Create the backup directory:

   ```bash
   mkdir -p /var/lib/jenkins/backup
   ```
4. Verify:

   ```bash
   ls -ld /var/lib/jenkins/backup
   ```

---

## E. Configure ThinBackup Settings in Jenkins UI

1. Open:

   ```
   Manage Jenkins → Configure System
   ```
2. Scroll down to the **ThinBackup** section
3. Configure the following fields:

### Backup Configuration

* **Backup directory**

  ```
  /var/lib/jenkins/backup
  ```

* **Full Backup Schedule (Cron)**
  ThinBackup requires **5 cron fields**:

  ```
  minute hour day month day_of_week
  ```

  Examples:

  * Jenkins recommended (load distributed):

    ```
    H 0 * * *
    ```
  * Daily at 02:00 AM:

    ```
    0 2 * * *
    ```

* **Differential Backup Schedule (Optional)**

  ```
  0 */6 * * *
  ```

  (Every 6 hours)

* **Backup Items**

  * Jobs
  * Builds
  * Plugins
  * Jenkins Configuration

* **Exclusions**

  * Workspaces (optional)

* **Number of backups to keep**

  ```
  10
  ```

* **Restore directory (optional)**

  ```
  /var/lib/jenkins/backup/restore
  ```

4. Click **Save**

---

## F. Take Manual Backup

1. Go to:

   ```
   Manage Jenkins → ThinBackup
   ```
2. Click **Backup Now**
3. Verify backup files on the server:

   ```bash
   ls /var/lib/jenkins/backup
   ```

A **full backup** is created containing all selected Jenkins data.

---

## G. Backup Verification Scenario

* A Jenkins job exists and has executed successfully
* The job is **deleted intentionally**
* Backup already exists from the previous step

---

## H. Restore Procedure

1. Navigate to:

   ```
   Manage Jenkins → ThinBackup → Restore
   ```
2. Select the appropriate backup
3. Click **Restore**

---

## I. Restart Jenkins After Restore

After restoration, restart Jenkins to apply changes:

### Option 1: From Server

```bash
sudo systemctl restart jenkins
```

### Option 2: From Browser

```
http://<JENKINS-URL>/restart
```

---

## J. Expected Outcome

* Deleted Jenkins jobs are restored successfully
* Jenkins configuration, plugins, and job history are recovered
* System returns to the previous stable state



Just let me know 👍

