# linux-admin-guide
Linux Administration: Commands and Configuration Guide
This repository provides essential Linux commands and configurations for system management. It includes instructions on system targets, password recovery, job scheduling, and kickstart installation setup.

# Table of Contents

1.[Managing System Targets](#1-managing-system-targets)
- [Switching Between Targets](#switching-between-targets)
- [Entering Rescue Mode](#entering-rescue-mode)
- [Temporary Boot to Multi-User Target via GRUB](#temporary-boot-to-multi-user-target-via-grub)

2.[Breaking Root Password](#breaking-root-password)

3.[Scheduling Tasks](#3-scheduling-tasks)
- [One-Time Scheduling Using at](#one-time-scheduling-using-at)
- [Recurring Scheduling Using crontab](#recurring-sheduling-using-crontab)

4.[Kickstart Installation](#4-kickstart-installation)
- [Configure FTP Server](#configure-ftp-server)
- [Modify FTP Configuration](#modify-ftp-configuration)
- [Perform Kickstart Installation](#perform-kickstart-installation)

# 1. Managing System Targets
## a) Switching Between Targets

Check the default target

    systemctl get-default
    
Switch to multi-user target (CLI mode)

    systemctl isolate multi-user.target

 Return to the default target (graphical mode) and reboot
  
    init 6

Set multi-user target as the default

    systemctl set-default multi-user.target

Reboot to verify the new default

The system will boot into multi-user mode by default.

## b) Entering Rescue Mode

Switch to rescue mode for system recovery:

    systemctl isolate rescue.target


## c) Temporary Boot to Multi-User Target via GRUB

Restart the system and edit the GRUB entry during boot.

Add the following to the kernel line:

     systemd.unit=multi-user.target
     
Press Ctrl + X to boot.

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

    yum install vsftpd
    systemctl start vsftpd.service
    systemctl status vsftpd.service

Prepare the kickstart file:

    cd /root
    cp -rvf anaconda-ks.cfg /var/ftp/pub/
    mv /var/ftp/pub/anaconda-ks.cfg /var/ftp/pub/new.cfg

Allow FTP through the firewall:

    firewall-cmd --add-service=ftp --permanent
    firewall-cmd --reload

Verify network connectivity:

    nmcli con up ens160
    
## b) Modify FTP Configuration

Edit the FTP configuration file:

    vim /etc/vsftpd/vsftpd.conf

Update the following lines:

    Line 12: anonymous_enable=YES
    Line 114: listen=YES
    Line 123: Comment out any additional restrictions.

Restart FTP service:

     systemctl restart vsftpd.service

## c) Perform Kickstart Installation

Boot the new system and press Tab to edit the boot options.

Add the following line to the kernel parameters:

perl
           
           inst.ks=ftp://<server-IP>/pub/new.cfg

Start the installation.

