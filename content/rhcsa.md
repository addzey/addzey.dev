+++
title = "RHCSA Study Notes"
date = "2023-05-28"
author = "Adam"
tags = ["RHCSA", "Red Hat","rhel", "Study Notes"]
keywords = ["RHCSA"]
description = "My study notes for RHCSA certification"
toc = true
+++

# Introduction
 I'll be updating this as I go through practice tasks before taking the RHCSA exam!  
 Update: Exam is booked in for 05/09/23

## Understand and use essential tools

### Access a shell prompt and issue commands with correct syntax
I would say this objective is well covered throughout, so I wont add any explanations or examples here

### Use input-output redirection (>, >>, |, 2>, etc.)
STDIN (user) **->** CMD **->** STDOUT (console/screen)  

Read a file as standard input = "**<**" which changes the flow to 'CMD **->** FILE **->** STDOUT' with the file replacing STDIN  

Send the standard output to a file = "**>**" which changes the flow to 'STDIN **->** CMD **->** file' with the file now replacing STDOUT  
`ls > lsresult`

Redirect STDERR (error messages) = "**2>**" which sends any error messages to the defined location  
`ls jibberish 2> errors`  
`ls jibberish 2> /dev/null`  

Send the standard output to a file but this time append the file = "**>>**" which changes the flow to 'STDIN **->** CMD **->** file' with the file now replacing STDOUT and the STDOUT will append rather than write/overwrite  
`ls /etc >> lsetcresultappend`
`ls /etc >> lsetcresultappend`

Piping = "**|**" will use the result of the previous command as the STDINPUT of another command  
`ls -l /etc | wc`


### Use grep and regular expressions to analyze text
Find the text "ssh" in the output of 'ps aux': `ps aux | grep ssh`  
Recursively search for the text "root" in /etc": `grep -R root /etc`  
Find the text "adam" in /etc/shadow but case-insensitive: `grep -i Adam /etc/shadow`  

### Access remote systems using SSH
Connect to a remote system using SSH: `ssh username@10.0.0.1`  
Things to keep in mind: `~/.ssh/known_hosts` which is a record of public keys for hosts you have connected to previously and `~/.ssh/id_rsa.pub` which is the public key for the host you're currently connected to  
 

### Log in and switch users in multiuser targets
Change virtual terminal: `chvt 4` Can also be done with CTL+ALT+F[1-6] when using console or phsyical logins  
Use "w" command to see all logged in users: `w`  
Use "su" to change user to root: `su -`  
Use "sudo" to run commands as root: `sudo command -options`  

### Archive, compress, unpack, and uncompress files using tar, star, gzip, and bzip2
Create an XZ compressed archive of the home folder: `tar cJvf homefolder.tar.xz /home`  
Extract a compressed archive into a folder: `tar xvf homefolder.tar.xz -C /tmp/archive`

### Create and edit text files
Replace all occurrences of a word in a text file: `sed -i s/originalword/replacementword/g /etc/service.conf`  
Using vim is out of scope for these study notes, check out [https://www.linuxfoundation.org/blog/blog/classic-sysadmin-vim-101-a-beginners-guide-to-vim](https://www.linuxfoundation.org/blog/blog/classic-sysadmin-vim-101-a-beginners-guide-to-vim)


### Create, delete, copy, and move files and directories
Create a directory: `mkdir directoryname`  
Create a sub directory in directory that doesn't already exist: `mkdir -p /tmp/newdir/newdir2`  
Copy all files starting with the letter 'a', 'b' or 'c' from /etc into /tmp/files: `cp /etc/[a-c]* /tmp/files`  
Move all files starting with the letters 'a' or 'b' to a subfolder: `mv [a-b]* /tmp/files/folder1`  
Move all files starting with the letter 'c' into a sub folder: `mv c* /tmp/files/folder2`  
Copy all files smaller than 1KB into a sub folder: `find -size -1000c -exec cp {} /tmp/files/folder3 \;`  

### Create hard and soft links
Consider hard links as similar to an additional copy of a file  
Consider soft/symbolic links as similar to a shortcut to a file  

Hard link `ln /etc/hosts /root/hardlinkhosts`  
Soft link `ln -s /etc/hosts /root/symboliclinkhosts`

### List, set, and change standard ugo/rwx permissions

### Locate, read, and use system documentation including man, info, and files in /usr/share/doc
Update mandb to be able to search for text: `mandb`  
`man -k searchtext`  
`man command`  

`more /usr/share/doc/openssh/README`  
`less /usr/share/doc/lvm2/README`  

## Create simple shell scripts

### Conditionally execute code (use of: if, test, [], etc.)

### Use Looping constructs (for, etc.) to process file, command line input

### Process script inputs ($1, $2, etc.)

### Processing output of shell commands within a script
  

## Operate running systems

### Boot, reboot, and shut down a system normally

### Boot systems into different targets manually

### Interrupt the boot process in order to gain access to a system

### Identify CPU/memory intensive processes and kill processes

### Adjust process scheduling

### Manage tuning profiles

### Locate and interpret system log files and journals

### Preserve system journals

### Start, stop, and check the status of network services

### Securely transfer files between systems


## Configure local storage

### List, create, delete partitions on MBR and GPT disks

### Create and remove physical volumes

### Assign physical volumes to volume groups

### Create and delete logical volumes

### Configure systems to mount file systems at boot by universally unique ID (UUID) or label

### Add new partitions and logical volumes, and swap to a system non-destructively


## Create and configure file systems  

### Create, mount, unmount, and use vfat, ext4, and xfs file systems

### Mount and unmount network file systems using NFS

### Configure autofs

### Extend existing logical volumes

### Create and configure set-GID directories for collaboration

### Diagnose and correct file permission problems

## Deploy, configure, and maintain systems

### Schedule tasks using at and cron

### Start and stop services and configure services to start automatically at boot

### Configure systems to boot into a specific target automatically

### Configure time service clients

### Install and update software packages from Red Hat Network, a remote repository, or from the local file system

### Modify the system bootloader

## Manage basic networking

### Configure IPv4 and IPv6 addresses

### Configure hostname resolution

### Configure network services to start automatically at boot

### Restrict network access using firewall-cmd/firewall

## Manage users and groups

### Create, delete, and modify local user accounts

### Change passwords and adjust password aging for local user accounts

### Create, delete, and modify local groups and group memberships

### Configure superuser access

## Manage security

### Configure firewall settings using firewall-cmd/firewalld

### Manage default file permissions

### Configure key-based authentication for SSH

### Set enforcing and permissive modes for SELinux

### List and identify SELinux file and process context

### Restore default file contexts

### Manage SELinux port labels

### Use boolean settings to modify system SELinux settings

### Diagnose and address routine SELinux policy violations


## Manage containers

### Find and retrieve container images from a remote registry

### Inspect container images

### Perform container management using commands such as podman and skopeo

### Build a container from a Containerfile

### Perform basic container management such as running, starting, stopping, and listing running containers

### Run a service inside a container

### Configure a container to start automatically as a systemd service

### Attach persistent storage to a container


