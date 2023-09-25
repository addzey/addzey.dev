+++
title = "RHCSA Study Notes"
date = "2023-09-24"
author = "Adam"
tags = ["RHCSA", "RedHat","rhel", "Study Notes"]
keywords = ["RHCSA"]
description = "My study notes for RHCSA certification"
toc = true
+++

# Introduction
You can create a RedHat developer account for free which includes a RHEL subscription for up to 16 devices: https://developers.redhat.com/
 
I'll be updating this as I go through practice tasks before taking the RHCSA exam!  
Exam is booked in for **26/09/23** 

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
Things to keep in mind: `~/.ssh/known_hosts` which is a record of public keys for hosts you have connected to previously
 

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
List the contents of a folder including the permissions: `ls -la` or `ls -la /folder/path` 

Change the permissions of a folder or file: `chmod 770 file` or `chmod u+wr file` or `chmod g-w folder`  
Set the permissions of a folder or file while removing any existing permissions: `chmod u=rw,g=r file`  

Set user owner and group owner of a file: `chown user:group file`  
Set user owner and group owner of a folder and apply recursively: `chown -R user:group folder`  

Set a default filesystem ACL for a group to always have read access to a folder: `setfacl -m d:g:groupname:rx /folder/path`  
List existing filesystem ACL's for a folder: `getfacl /folder/path`  

### Locate, read, and use system documentation including man, info, and files in /usr/share/doc
Update mandb to be able to search for text: `mandb`  
`man -k searchtext`  
`man command`  

`more /usr/share/doc/openssh/README`  
`less /usr/share/doc/lvm2/README`  

## Create simple shell scripts

### Conditionally execute code (use of: if, test, [], etc.)
~~~
#!/bin/bash
# Example bash script to check for an input

if [ -z $1 ]
then
	echo "an input is required"
else
	echo "you have provided the input $1"
fi
~~~

### Use Looping constructs (for, etc.) to process file, command line input

### Process script inputs ($1, $2, etc.)

### Processing output of shell commands within a script  

## Operate running systems
Display memory usage: `free -m`  
Display uptime and load average: `uptime`  
Nice levels for processes: **-20** is highest priority and **19** is lowest priority

### Boot, reboot, and shut down a system normally
Reboot a system: `reboot`  
Shutdown a system: `shutdown -h now`  (Can specify a time delay in the -h switch)
Power off a system: `poweroff`  

### Boot systems into different targets manually
Show the current boot target: `systemctl get-default`  
Switch into a new target manually: `systemctl isolate multi-user.target`  
Set a new default boot target: `systemctl set-default multi-user.target`  

### Interrupt the boot process in order to gain access to a system
Hit the arrow keys during the grub menu to prevent the timeout from ticking down and proceeding with the boot process  
Hit the `e` key to edit the grub boot options  
Find the line specifying the Linux kernel options and append `rd.break`  
Hit `ctrl+x` to continue the boot process with your customizations  
You will be booted into emergency mode working from initramfs (actual system has not booted at all)  
Mount the root volume so we can make our changes: `mount -o remount,rw /sysroot`  
Change the shell environment to use the actual system root: `chroot /sysroot`  
Change the root password: `passwd root`  
Reset/Fix the selinux labels so it's possible to boot cleanly with the new root password: `touch /.autorelabel`  
Hit `ctrl+d` to exit the chroot environment  
Hit `ctrl+d` again to continue the boot process    

### Identify CPU/memory intensive processes and kill processes
Detailed overview of running processes: `ps aux`  
    This will output a lot of text so it's good to use less and step through the output with spacebar: `ps aux | less`  

Overview of running processes and their hierarchical relation: `ps fax`  
List of processes for a specific user: `ps fU adam`  

Display system resource usage: `top`  
    For **CPU%** - **us** = user | **sy** = system | **ni** = nice | **wa** = wait time (IO) | **hi** = hardware interrupts | **si** = software interrupts  
    Press `f` to change display settings and sorting  
    Press `k` to send the kill signal to the top process with "<enter>" or "<PID>" to send the kill signal to a specific process based on PID  
    Press `r` to renice (change the nice value) of a process  

Kill a process: `kill PID` (polite method, process gets the opportunity to shutdown cleanly)  
Kill a process: `kill -9 PID` (forced, can cause data loss for the process that's force killed)  
Kill all instances of a specific processes: `killall processname`  


### Adjust process scheduling
Run a process with lower priority: `nice 15 command`  
Change the priority of a running process: `renice -n -10 PID`  

### Manage tuning profiles
Ensure the **tuned** service is running: `systemctl status tuned`  
Show current tuned profile: `tuned-adm active`  
Show list of all tuned profiles available: `tuned-adm list`  
Set a specific tuned profile: `tuned-adm profile profilename`  

### Locate and interpret system log files and journals
Logs are now handled by **systemd-journald** in binary format but still get passed to **rslogd** which stores the logs in text format in `/var/log`   
View the journal for a specific service: `systemctl status httpd`  

Send specific logs or severity of logs to it's own log file: `vim /etc/rsyslog.conf`  
Create a custom logrotate entry to rotate these logs: `vim /etc/logrotate.d/example`  
~~~
/var/log/example {
    monthly
    rotate 12
}
~~~

### Preserve system journals
Value inside `/etc/systemd/journald.conf` the default will be "Storage=Auto" which will then persist logs if the folder `/var/log/journal` exists  
Make the systemd-journald logs persistent: `mkdir -p /var/log/journal` 

### Start, stop, and check the status of network services
Check the status of a network service: `systemctl status servicename`  
Start a network service: `systemctl start servicename`  
Stop a network service: `systemctl stop servicename`  

### Securely transfer files between systems
There are 3 options all made possible by SSH: `scp` and `sftp` and `rsync`  

**scp** is the most compatible option  
**sftp** creates a regular FTP session using SSH  
**rsync** will now also by default use SSH (no need for the `-e ssh` option)  

Use SCP to transfer some local files to a remote destination: `scp localfile01 localfile02 username@192.168.10.2:/remote/directory`  

Use SFTP to securely transfer files: `sftp username@192.168.10.2`  
Download a file via SFTP: `get filename`  
Upload a file via SFTP: `put filename`  

Download a directory and it's contents using rsync: `rsync -ar username@192.168.10.2:/etc /home/adam/rsyncdemo`  in format ":/remote/directory /local/directory"  

## Configure local storage
List block devices: `lsblk`  
Get UUID and labels of block devices: `blkid`  
The block devices can be found in `/dev/devicename`  

### List, create, delete partitions on MBR and GPT disks
**Use parted for EFI/GPT**: `parted /dev/sdX`  
List partitions with parted: `print`  
Create a GPT partition table with parted: `mklabel`  
Create a new GPT partition with parted: `mkpart`   
Delete a partition with parted:  `rm`  
Set a partition for use by LVM: `set N lvm on`  

**Use fdisk for BIOS/MBR**: `fdisk /dev/sdX`  
List partitions with fdisk: `print`  
Create a MBR partition table with fdisk: `o`  
Create a new partition with fdisk: `n`  
Delete a partition with fdisk: `d`  

### Create and remove physical volumes
Initialize a partition for use with LVM: `pvcreate /dev/sdb4`  
Initialize another partition for use with LVM: `pvcreate /dev/sdb5`  
Initialize a disk for use with LVM: `pvcreate /dev/sdc`  
Remove a physical volume: `pvremove /dev/sdb4`  

### Assign physical volumes to volume groups
Create a volume group from two physical volumes: `vgcreate volumegroup01 /dev/sdb4 /dev/sdb5`  
This will automatically initialize/create the physical volumes if they hadn't already been done like in the step above  

### Create and delete logical volumes
Create a logical volume using 100% of the available space in a volume group: `lvcreate -l100%FREE volumegroup01 -n logicalvolume01`  
Delete a logical volume: `lvremove volumegroup01/logicalvolume01`  
Create a logical volume of a specific size: `lvcreate -L 1.5G volumegroup01 -n logicalvolume02`  

### Configure systems to mount file systems at boot by universally unique ID (UUID) or label
To mount by label when editing /etc/fstab use: `LABEL=labelofpartition`  to specify the device  
To mount by UUID when editing /etc/fstab use: `UUID="000000011-0001-ab00-ba0000001234"`  to specify the device  

** You can also use systemd-mount which is likely to be the best method in future but is not needed for the exam **

### Add new partitions and logical volumes, and swap to a system non-destructively
Format a swap partition: `mkswap /dev/sdX`  
Mount a swap partition: `swapon -a` or `swapon /dev/sdX`  

## Create and configure file systems  

### Create, mount, unmount, and use vfat, ext4, and xfs file systems
Format a partition with XFS: `mkfs.xfs /dev/sdX`  
Format a partition with EXT4: `mkfs.ext4 /dev/sdX`  
Format a partition with VFAT: `mkfs.vfat /dev/sdX` 

Format a LVM logical volume with XFS: `mkfs.xfs /dev/mapper/volumegroup01-logicalvolume02`  

Mount a filesystem: `mount /dev/sdX /mount/location`  
Unmount a filesystem: `umount /mount/location`  

Show existing mounts: `mount` or `findmnt`  

### Mount and unmount network file systems using NFS
Show available exports: `showmount -e 192.168.10.2`  
Mount the available share: `mount 192.168.10.2:/data /mnt/nfs`  
Make the mount persistent by adding it to fstab by adding the below entry:
~~~
192.168.10.2:/data  /mnt/nfs    nfs _netdev 0 0
~~~
I have tested that using "defaults" like other mounts instead of "_netdev" also works  

### Configure autofs
Install required packages: `yum install autofs`  
This will create the files `/etc/auto.master` and other auto.* files  

Auto mount the /data export on /files: `vim /etc/auto.master`  
Add the line:
~~~
/files   /etc/auto.files
~~~

Create the file /etc/auto.files to define the NFS share for auto mounting: `vim /etc/auto.files`  
Add the line:
~~~
data    -rw 192.168.10.2:/data
~~~

Wildcard mounting for example home user directories, inside auto.ldap:
~~~
*   -rw servername:/users/&
~~~

### Extend existing logical volumes
Extend a logical volume by 2GB and resize the filesystem: `lvextend -L +2GB volumegroup01/logicalvolume02 -r`  
Extend a logical volume to use all available free space: `lvextend -l +100%FREE volumegroup01/logicalvolume02`  

### Create and configure set-GID directories for collaboration

### Diagnose and correct file permission problems

## Deploy, configure, and maintain systems

### Schedule tasks using at and cron
Create a user-specific cron job with: `contrab -e` 

Cron file format needs to be as follows:
|---|---|---|---|---|---|
|minute|hour|day of month|month|day of week|command to run|
|0-59|0-23|1-31|1-12|0-7 (0 or 7 is Sunday)|

Hourly, Daily, Weekly and Monthly cron jobs that are generally installed by packages can be found in `/etc/cron.d`  

You can use at to schedule a job to run at a specific time
`at 19:17` followed by entering the command to run in the prompt: `logger send a message to the log` followed by `ctrl+d`  
List jobs in the "at" queue: `atq`  
Remove a job from the "at" queue: `atrm jobnumber`  

Another option is systemd timers which don't look to be an exam objective, you can find some that come preconfigured in `/usr/lib/systemd/system`  
These can be enabled and started like any other systemd unit: `systemctl enable example.timer` and `systemd start example.timer`  

### Start and stop services and configure services to start automatically at boot
Start a service: `systemctl start servicename`  
Stop a service: `systemctl stop servicename`  
Check the status of a service: `systemctl status servicename`  
Set a service to start on boot: `systemctl enable servicename`  
Stop a service from starting on boot: `systemctl disable servicename`  

List systemd unit types: `systemctl -t help`  
List running units: `systemctl list-units`  

Reload systemd units after making changes: `systemctl daemon-reload`  

Edit a systemd service and have it always restart after a 1 minute delay:
`systemctl edit httpd`  
In the text editor that opens, add in
~~~
[Service]
Restart=always
RestartSec=60s
~~~
Reload systemd units: `systemctl daemon-reload`  
Set the service to start at boot and start it now: `systemctl enable --now httpd`
Kill the service: `killall httpd`  
Check that the service has stopped and it's Active status is "Activating" - this should show that it will auto-restart in 60 seconds  
`systemctl status httpd`  

### Configure systems to boot into a specific target automatically

### Configure time service clients
See current time status: `timedatectl status`  
Set the timezone: `timedatectl set-timezone Country/State`  
Time should be synchronized from NTP by default, NTP servers can be set in `/etc/chrony.conf`  

### Install and update software packages from Red Hat Network, a remote repository, or from the local file system
Installing packages from Red Hat network requires registering a subscription which can be done with Subscription Manager  
Register subscription: `subscription-manager register` (provide username and password)  
Connect the subscription: `subscription-manager attach --auto`  

Search for a package: `yum search packagename`  
Find a package that contains a wanted tool: `yum provides setools`  
Show info/description of a package: `yum info packagename`  
Show all available packages: `yum list` and show installed packages: `yum list installed`  
Install a package: `yum install packagename`  
Remove a package: `yum remove packagename`  

Check for newer versions of installed packages: `yum update`  
Check for a newer version of a specific package: `yum update packagename`  

Download the rpm of an available package: `yumdownloader httpd` or `dnf download httpd`  
Check an rpm file for any scripts: `rpm -qp --scripts httpd-2.4.57-5.el9.x86_64.rpm`  

**$$ need to add something for "yum groups" and "yum modules" $$**

Create a **local** repo for installing packages:  
    Create an ISO from the dvd drive: `dd if=/dev/sr0 of=/rhel92.iso bs=1M`  
    Create a mount point: `mkdir /repo`  
    Mount the ISO as **/repo** by editing **/etc/fstab** and adding `/rhel92.iso /repo iso9660 defaults 0 0` followed by `systemctl daemon-reload` and `mount -a`  

Create a yum repo with an entry for the above starting with BaseOS and then AppStream:   

`vim /etc/yum.repos.d/baseos.repo`  
    Enter the contents: 
~~~ 
    [BaseOS]  
    name=BaseOS  
    baseurl=file:///repo/BaseOS 
    gpgcheck=0  
~~~

`vim /etc/yum.repos.d/appstream.repo`  
    Enter the contents: 
~~~ 
    [AppStream]  
    name=AppStream  
    baseurl=file:///repo/AppStream  
    gpgcheck=0  
~~~

### Modify the system bootloader
Edit grub config to have a more verbose boot: `vim /etc/default/grub`  and find "GRUB_CMDLINE_LINUX" then remove 'rhgb quiet' from the options  
Write out a new grub config [BIOS]: `grub2-mkconfig -o /boot/grub2/grubg.cg`  
Write out a new grub config [UEFI]: `grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg`  

## Manage basic networking

General tools for network info: `ip addr` and `ip -s link` and `ip route`  

### Configure IPv4 and IPv6 addresses
Use **NetworkManager CLI** to configure networking:  
    Create static connection with the name "example-static": `nmcli con add type ethernet con-name example-static ifname enp2s0 ip4 192.168.1.100/24 gw4 192.168.1.1`  
    Add DNS to the connection "example-static": `nmcli con mod example-static ipv4.dns “8.8.8.8 8.8.4.4”`  

Use **NetworkManager console GUI** to configure networking: `nmtui`  
    Use `arrow keys, tab, spacebar and enter` to navigate menu options for configuring network settings    

### Configure hostname resolution
Check current configuration: `hostnamectl`  
Set a hostname for the server: `hostnamectl hostname rchsa01`  
Set the FQDN for the server: `hostnamectl hostname rhcsa02.example.com`  

If there's any issues with hostname resolution you can check `/etc/resolv.conf`  which should say this file is managed by NetworkManager
Set DNS for a connection named "example-static": `nmcli con mod example-static ipv4.dns “8.8.8.8 8.8.4.4”` 
Confirm hostname resolution is working using `dig` or `ping hostname.com`  

### Configure network services to start automatically at boot
Start a network service: `systemctl start httpd`  
Set the network service to start automatically at boot: `systemctl enable httpd`  

### Restrict network access using firewall-cmd/firewall
Show current config: `firewall-cmd --list-all`    
Block HTTPS traffic for runtime: `firewall-cmd --remove-service https`  
Block HTTPS traffic and make it persistent: `firewall-cmd --remove-service https --permanent`  
Reload firewall: `firewall-cmd --reload`  

## Manage users and groups

### Create, delete, and modify local user accounts
Create a user with a comment associated with the account: ``useradd -c "this is a new user" notadam``  
Create a new user without a comment: ``useradd notadam2``  

Remove a user: ``userdel notadam2``  
Remove a user and remove their home directory: ``userdel -r notadam``  

Specify files to be created in users home directories by default: **/etc/skel**

### Change passwords and adjust password aging for local user accounts
Change the password for your currently logged in account: ``passwd``  
Change the password for another account (requires root): ``passwd username``  

Set password aging in ``/etc/login.defs``  
Find the variable named **PASS_MAX_DAYS**  
Setting password aging in /etc/login.defs will only apply to newly created accounts  
Set the minimum password age for an existing account: ``chage -m 3 username``  
Set the maximum password age for an existing account: ``chage -M 90 username``  


### Create, delete, and modify local groups and group memberships
Add user to the group "wheel": ``usermod -aG wheel adam``  
Using the -a switch along with the -G switch means it will **append** group memberships rather than replace  

List members of a group: ``lid -g groupname``  
Delete a group: ``groupdel groupname``  
Change the name of a group: ``groupmod -n newgroupname oldgroupname``  

### Configure superuser access
View current config of sudo/superuser access: `visduo /etc/sudoers`  
Members of the group "wheel" should be allowed sudo/superuser access
Add a user to group "wheel": `usermod -aG wheel username`  
You can now run things as superuser/root by using "sudo" in front of the commands: `sudo reboot`  

Grant superuser access only for specific commands: `visduo /etc/sudoers`  
Add the following line to the sudoers file: `username ALL=(root) /bin/ping`  
The above will allow the user to execute "/bin/ping" as root

## Manage security

### Configure firewall settings using firewall-cmd/firewalld
Show current config: `firewall-cmd --list-all`  
List pre-defined services that can be allowed through the firewall: `firewall-cmd --list-services`  
Allow HTTPS traffic for runtime: `firewall-cmd --add-service https`  
Allow HTTPS traffic and make it persistent: `firewall-cmd --add-service https --permanent`  
Reload firewall: `firewall-cmd --reload`  

### Manage default file permissions

### Configure key-based authentication for SSH
Create a public/private key pair for current user: `ssh-keygen`  
Note: The public key can be shared  
Note: The private key must not be shared and must be stored securely

Transfer the public key to destination server: `ssh-copy-id 192.168.10.10`  

You should now be able to login using pubkey authentication as it's enabled by default  

To review current ssh server configuration check: `vim /etc/ssh/sshd_config`  You should see the line `#PubKeyAuthentication yes` it is commented out because it's the default setting  

Some settings that you might like to change in there would be `PasswordAuthentication` and `PermitRootLogin`  disabling both of these is generally a good option for improved security  

To restrict SSH login to specific users, the users can be defined with `AllowUsers username1 username2`  

### Set enforcing and permissive modes for SELinux
Check the current SELinux status: `getenforce`  
Change the SELinux mode to permissive: `setenforce permissive`  
Change the SELinux mode to enforcing: `setenforce enforcing`  

### List and identify SELinux file and process context
SELinux contexts follow the below standard:
**user** specific context (you will see this as _u)  
**role** specific context (you will see this as _r)  
**type** which defines allowed operations (you will see this as _t)  

List directory contents including SELinux file contexts: `ls -laZ`  
List processes including SELinux process contexts: `ps auxZ`  
Find the SELinux process context for httpd: `ps auxZ | grep httpd`  

### Restore default file contexts
Restore defaults as per policy for the entire filesystem: `touch /.autorelabel` followed by rebooting  
Change the SELinux context on folder /web: `semanage-fcontext -a -t httpd_sys_content_t "/web(/.*)?"`  
Restore contexts per policy for a specific folder: `restorecon -R -v /web`  

### Manage SELinux port labels

### Use boolean settings to modify system SELinux settings
List all SELinux boolean options: `getsebool -a`  
Set a SELinux boolean value persistently: `setsebool -P <option> [on/off]`  
For example, allow httpd access to user home directories: `setsebool -P httpd_enable_homedirs on`  

### Diagnose and address routine SELinux policy violations
Check for sealerts in /var/log/messages: `grep sealert /var/log/messages`   
Do the same with journalctl: `journalctl | grep sealert`  
Analyze the audit log and provide a human readable output with suggestions: `sealert -a /var/log/audit/audit.log`  

Search "/var/log/audit.log" for messages with AVC: `grep AVC /var/log/audit/audit.log`  

## Manage containers
Install required tools: `yum install container-tools`  

### Find and retrieve container images from a remote registry
Red Hat container registry requires a login (which you can get for free with a Developer account)
Provide login details: `podman login`  
Search for containers: `podman search nginx`  

### Inspect container images
List local images: `podman images`  
Inspect a remote image: `skopeo inspect docker://registry.fedoraproject.org/fedora:latest`  
Inspect an image that exists locally: `podman inspect imagename`  

### Perform container management using commands such as podman and skopeo
Remove a container: `podman rm containername`  
Remove a container image: `podman rmi imagename`  
View logs from a container: `podman logs containername`  

### Build a container from a Containerfile
Create a directory for your container: `mkdir mycontainer` switch into the directory `cd mycontainer`   
Create a basic script to echo some words: `vim myecho`  
Inside this file:
~~~
#!/bin/bash
# script to echo some words
echo "this is mycontainer!"
~~~
Set it as executable: `chmod 755 myecho`  
Create the Containerfile: `vim Containerfile`  
Inside this file:
~~~
FROM registry.access.redhat.com/rhel9/rhel9-minimal
ADD myecho /usr/local/bin
ENTRYPOINT "/usr/local/bin/myecho"
~~~
Build the container: `buildah build -t mycontainer`  
Run the container: `podman run mycontainer` it should output the text "this is mycontainer!"  

### Perform basic container management such as running, starting, stopping, and listing running containers
Start a container in the background: `podman run -d nginx`  
Start a container with an interactive shell: `podman run -it fedora`  
Stop a container: `podman stop containername`  
Restart a container: `podman restart containername`  
List running containers: `podman ps`  

Create a systemd service for a container: `podman generate systemd --name containername --files`  
Move the resulting .service file to `/home/adam/.config/systemd/user` for example: `mv container-name.service /home/adam/.config/systemd/user`  
Enable session linger for the user running the container: `loginctl enable-linger adam`
Enable the systemd service for the container: `systemctl --user enable container-name.service`  
Start the systemd service to also start the container: `systemctl --user start container-name.service`  
The container will now start on boot

### Run a service inside a container
Run nginx with port forwards from the host: `podman run -d -p 8080:80 nginx`  
Allow the port through the host firewall: `firewall-cmd --add-port=8080/tcp`  
Allow the port through the host firewall again to survive reboot: `firewall-cmd --add-port=8080/tcp --permanent`  

### Configure a container to start automatically as a systemd service
This will first require enabling regular user accounts to stay logged in/start services on boot: `loginctl enable-linger username`  
Create the local folder to storing the systemd user units: `mkdir -p .config/systemd/user`  
Create a systemd unit for a container: `podman generate systemd --name nginx --files`  

### Attach persistent storage to a container
Store web public html persistently outside the container: `podman run -d -p 8080:80 -v /home/adam/public_html:public_html:Z nginx`  