# Linux Administration: Commands and Configuration Guide
This repository provides essential Linux commands and configurations for system management. It includes instructions on system targets, password recovery, job scheduling, and kickstart installation setup.

# Table of Contents

1.[Managing System Targets](#1-managing-system-targets)
- [Switching Between Targets](#a-switching-between-targets)
- [Entering Rescue Mode](#b-entering-rescue-mode)
- [Temporary Boot to Multi-User Target via GRUB](#c-temporary-boot-to-multi-user-target-via-grub)

2.[Breaking Root Password](#2-breaking-root-password)

3.[Scheduling Tasks](#3-scheduling-tasks)
- [One-Time Scheduling Using at](#a-one-time-scheduling-using-at)
- [Recurring Scheduling Using crontab](#b-recurring-sheduling-using-crontab)

4.[Kickstart Installation](#4-kickstart-installation)
- [Configure FTP Server](#a-configure-ftp-server)
- [Modify FTP Configuration](#b-modify-ftp-configuration)
- [Perform Kickstart Installation](#c-perform-kickstart-installation)

# 1. Managing System Targets
## a) Switching Between Targets

Check the default target

![Screenshot from 2025-01-10 23-09-08](https://github.com/user-attachments/assets/ec688a6f-beb7-4916-8161-a6bdaf34d1e0)

    
Temporary Switch to multi-user target (CLI mode)

    systemctl isolate multi-user.target

![Screenshot from 2025-01-10 23-11-55](https://github.com/user-attachments/assets/eab7549f-7b59-4b92-b0b6-b7553667764e)


 Return to the default target (graphical mode) and reboot
  
    init 6

---

Set multi-user target as the default

    systemctl set-default multi-user.target

![Screenshot from 2025-01-10 23-17-30](https://github.com/user-attachments/assets/4f438ee6-85fc-4f74-9049-c357363b3223)

Reboot to verify the new default

![Screenshot from 2025-01-10 23-17-42](https://github.com/user-attachments/assets/ac4ecef4-e03c-40e3-9a3d-bf4c78e27668)

The system will boot into multi-user mode by default.

![Screenshot from 2025-01-10 23-18-20](https://github.com/user-attachments/assets/e14b1fec-b578-419b-bdcf-6c13dd1738be)

---

## b) Entering Rescue Mode

Switch to rescue mode for system recovery:

    systemctl isolate rescue.target


## c) Temporary Boot to Multi-User Target via GRUB

Restart the system and edit the GRUB entry during boot.

![Screenshot from 2025-01-11 00-02-33](https://github.com/user-attachments/assets/046e9aac-3056-48ee-a966-f860ce108e3c)

press e

![Screenshot from 2025-01-10 23-56-25](https://github.com/user-attachments/assets/90e27aad-5adb-4fdc-ba0e-351a78f3c6af)

Add the following to the kernel line:

     systemd.unit=multi-user.target

![Screenshot from 2025-01-10 23-57-55](https://github.com/user-attachments/assets/bfe8f9f3-62f2-4c16-9630-59b07fcfb985)

Press Ctrl + X to boot.

![Screenshot from 2025-01-10 23-58-25](https://github.com/user-attachments/assets/dc1bd6f3-b9f2-4a6e-bf3c-ed71b5de4e85)



# 2. Breaking Root Password

Edit the GRUB boot entry and add:
kotlin

    rd.break

Boot into emergency mode.

Remount sysroot with read-write permissions:

    mount -o rw,remount /sysroot

Switch to the root filesystem:
 
     chroot /sysroot

Reset the root password:

    passwd root

Relabel SELinux context for changes:

    touch /.autorelabel

Exit and reboot the system:

    exit
    
# 3. Scheduling Tasks
## A) One-Time Scheduling Using at

Schedule a task:

    at 3pm

Example: Create a file at a specific time:

    touch /root/Desktop/f5

View scheduled tasks:

    atq

Delete a specific task (e.g., task ID 8):

    atrm 8

Schedule a task to run in 2 minutes:

    at now +2 min

Example:

    touch /root/Desktop/f29

Verify if the task ran successfully:

    journalctl -n 5

## B) Recurring Scheduling Using crontab

Edit crontab:

    crontab -e

Add cron job examples:

Run a task at 2:20 AM on October 15:

    20 2 15 10 * task1

Run a task at 12:00 PM every Monday:

    0 12 * * 0 task1

Run a task at 12:00 PM from 25th to 30th of every month:

    0 12 25-30 * * task1

Run a task every 5 minutes starting at 2:00 AM:

markdown

    */5 2 * * * task
    
Display all cron jobs:

    crontab -l

Delete all cron jobs:

    crontab -r

Edit crontab for a specific user (e.g., tom):

    crontab -e -u tom

# 4. Kickstart Installation

## a) Configure FTP Server

Install and start FTP server:

![Screenshot from 2025-01-10 21-03-41](https://github.com/user-attachments/assets/acbc74cb-f95f-4621-b557-8d7e3ad619c5)


![Screenshot from 2025-01-10 21-02-39](https://github.com/user-attachments/assets/5a35bf98-234c-4dd3-a04d-b317f4687e89)

Prepare the kickstart file:

![Screenshot from 2025-01-10 21-10-39](https://github.com/user-attachments/assets/36b69a76-c0bb-4cb9-8b14-55efc76be386)

Allow FTP through the firewall:

![Screenshot from 2025-01-10 21-12-48](https://github.com/user-attachments/assets/ab08f1fa-0503-44a7-ac3a-12a8ee200b41)


Verify network connectivity:

![Screenshot from 2025-01-10 21-14-46](https://github.com/user-attachments/assets/b3693193-bcd9-4fe4-ab2a-01dd22599371)

![Screenshot from 2025-01-10 21-19-37](https://github.com/user-attachments/assets/766fbfb7-ae34-4f35-878c-6ce385f2b38b)

    
## b) Modify FTP Configuration

Edit the FTP configuration file:

    vim /etc/vsftpd/vsftpd.conf

Update the following lines:

    Line 12: anonymous_enable=YES
    Line 114: listen=YES
    Line 123: Comment out any additional restrictions.

Restart FTP service:

![Screenshot from 2025-01-10 21-17-48](https://github.com/user-attachments/assets/8dd5a143-214c-4371-ade2-3c01eae9da3f)


## c) Perform Kickstart Installation

Boot the new system and press e to edit the boot options.

![Screenshot from 2025-01-10 21-55-23](https://github.com/user-attachments/assets/967dbe58-c9bf-4003-8aa1-128e8d6a98b9)

![Screenshot from 2025-01-10 21-55-50](https://github.com/user-attachments/assets/c52bbf33-a052-4e76-8d4b-67ff9e0dcf50)


Add the following line to the kernel parameters:

![Screenshot from 2025-01-10 22-21-30](https://github.com/user-attachments/assets/5f4d9fc7-8bbe-4f21-a3c0-1b207c4f5e2f)

![Screenshot from 2025-01-10 22-22-19](https://github.com/user-attachments/assets/3ded1764-eb5e-4bff-b2a5-7574f40f9867)

![Screenshot from 2025-01-10 21-59-15](https://github.com/user-attachments/assets/490e4def-fcfb-4af3-850e-1da2266bdbc0)


Start the installation.

