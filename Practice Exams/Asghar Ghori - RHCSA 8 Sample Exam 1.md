Asghar Ghori - Sample RHCSA Exam 1

    * Setup a virtual machine RHEL 8 Server for GUI. Add a 10GB disk for the OS and use the default storage partitioning. Add 2 300MB disks. Add a network interface, but do not configure the hostname and network connection.

    * Assuming the root user password is lost, reboot the system and reset the root user password to root1234:
        ```shell
        # ctrl + e after reboot
        # add rd.break after Linux line
        # ctrl + d
        mount -o remount, rw /sysroot
        chroot /sysroot
        passwd
        # change password to root12345
        touch /.autorelabel
        exit
        reboot
        ```

    * Using a manual method (i.e. create/modify files by hand), configure a network connection on the primary network device with IP address 192.168.0.241/24, gateway 192.168.0.1, and nameserver 192.168.0.1:
        ```shell
        vi /etc/sysconfig/network-scripts/ifcfg-enp0s3
        systemctl restart NetworkManager.service
        # add line IPADDR=192.168.0.241
        # add line GATEWAY=192.168.0.1
        # add line DNS=192.168.0.1
        # add line PREFIX=24
        # change BOOTPROTO from dhcp to none
        ifup enp0s3
        nmcli con show # validate
        ```

    * Using a manual method (modify file by hand), set the system hostname to rhcsa1.example.com and alias rhcsa1. Make sure the new hostname is reflected in the command prompt:
        ```shell
        vi /etc/hostname
        # replace line with rhcsa1.example.com
        vi /etc/hosts
        # add rhcsa1.example.com and rhcsa1 to first line
        systemctl restart NetworkManager.service
        vi ~/.bashrc
        # add line export PS1 = <($hostname)>
        ```

    * Set the default boot target to multi-user:
        ```shell
        systemctl set-default multi-user.target
        ```

    * Set SELinux to permissive mode:
        ```shell
        setenforce permissive
        sestatus # confirm
        vi /etc/selinux/config
        # change line SELINUX=permissive for permanence
        ```

    * Perform a case-insensitive search for all lines in the `/usr/share/dict/linux.words` file that begin with the pattern "essential". Redirect the output to `/tmp/pattern.txt`. Make sure that empty lines are omitted:
        ```shell
        grep '^essential' /usr/share/dict/linux.words > /tmp/pattern.txt
        ```

    * Change the primary command prompt for the root user to display the hostname, username, and current working directory information in that order. Update the per-user initialisation file for permanence:
        ```shell
        vi /root/.bashrc
        # add line export PS1 = '<$(whoami) on $(hostname) in $(pwd)>'$
        ```

    * Create user accounts called user10, user20, and user30. Set their passwords to Temp1234. Make accounts for user10 and user30 to expire on December 31, 2021:
        ```shell
        useradd user10
        useradd user20
        useradd user30
        passwd user10 # enter password
        passwd user20 # enter password
        passwd user30 # enter password
        chage -E 2021-12-31 user10
        chage -E 2021-12-31 user30
        chage -l user10 # confirm
        ```

    * Create a group called group10 and add users user20 and user30 as secondary members:
        ```shell
        groupadd group10
        usermod -aG group10 user20
        usermod -aG group10 user30
        cat /etc/group | grep "group10" # confirm
        ```

    * Create a user account called user40 with UID 2929. Set the password to user1234:
        ```shell
        useradd -u 2929 user40
        passwd user40 # enter password
        ```

    * Create a directory called dir1 under `/tmp` with ownership and owning groups set to root. Configure default ACLs on the directory and give user user10 read, write, and execute permissions:
        ```shell
        mkdir /tmp/dir1
        cd /tmp
        # tmp already has ownership with root
        setfacl -m u:user10:rwx dir1
        ```

    * Attach the RHEL 8 ISO image to the VM and mount it persistently to `/mnt/cdrom`. Define access to both repositories and confirm:
        ```shell
        # add ISO to the virtualbox optical drive
        mkdir /mnt/cdrom
        mount /dev/sr0 /mnt/cdrom
        vi /etc/yum.repos.d/image.repo
        blkid /dev/sr0 >> /etc/fstab
        vi /etc/fstab
        # format line with UUID /mnt/cdrom iso9660 defaults 0 0
        # contents of image.repo
        #####
        #[BaseOS]
        #name=BaseOS
        #baseurl=file:///mnt/cdrom/BaseOS
        #enabled=1
        #gpgenabled=1
        #gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
        #
        #[AppStream]
        #name=AppStream
        #baseurl=file:///mnt/cdrom/AppStream
        #enabled=1
        #gpgenabled=1
        #gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
        #####
        yum repolist # confirm
        ```

    * Create a logical volume called lvol1 of size 300MB in vgtest volume group. Mount the Ext4 file system persistently to `/mnt/mnt1`:
        ```shell
        mkdir /mnt/mnt1
        # /dev/sdb is already 300MB so don't need to worry about partitioning
        vgcreate vgtest /dev/sdb
        lvcreate --name lvol1 -L 296MB vgtest
        lsblk # confirm
        mkfs.ext4 /dev/mapper/vgtest-lvol1
        vi /etc/fstab
        # add line
        # /dev/mapper/vgtest-lvol1 /mnt/mnt1 ext4 defaults 0 0
        mount -a
        lsblk # confirm
        ```

    * Change group membership on `/mnt/mnt1` to group10. Set read/write/execute permissions on `/mnt/mnt1` for group members, and revoke all permissions for public:
        ```shell
        chgrp group10 /mnt/mnt1
        chmod 770 /mnt/mnt1
        ```

    * Create a logical volume called lvswap of size 300MB in the vgtest volume group. Initialise the logical volume for swap use. Use the UUID and place an entry for persistence:
        ```shell
        # /dev/sdc is already 300MB so don't need to worry about partitioning
        vgcreate vgswap /dev/sdc
        lvcreate --name lvswap -L 296MB vgswap /dev/sdc
        mkswap /dev/mapper-vgswap-lvswap # UUID returned
        blkid /dev/sdc >> /etc/fstab
        # organise new line so that it has UUID= swp swap defaults 0 0
        swapon -a
        lsblk # confirm
        ```

    * Use tar and bzip2 to create a compressed archive of the `/etc/sysconfig` directory. Store the archive under `/tmp` as etc.tar.bz2:
        ```shell
        tar -cvzf /tmp/etc.tar.bz2 /etc/sysconfig
        ```

    * Create a directory hierarchy `/dir1/dir2/dir3/dir4`, and apply SELinux contexts for `/etc` on it recursively:
        ```shell
        mkdir -p /dir1/dir2/dir3/dir4
        ll -Z 
        # etc shown as system_u:object_r:etc_t:s0
        # dir1 shown as unconfined_u:object_r:default_t:s0
        semanage fcontext -a -t etc_t "/dir1(/.*)?"
        restorecon -R -v /dir1
        ll -Z # confirm
        ```

    * Enable access to the atd service for user20 and deny for user30:
        ```shell
        echo "user30" >> /etc/at.deny
        # just don't create at.allow
        ```

    * Add a custom message "This is the RHCSA sample exam on $(date) by $LOGNAME" to the `/var/log/messages` file as the root user. Use regular expression to confirm the message entry to the log file:
        ```shell
        logger "This is the RHCSA sample exam on $(date) by $LOGNAME"
        grep "This is the" /var/log/messages
        ```

    * Allow user20 to use sudo without being prompted for their password:
        ```shell
        usermod -aG wheel user20
        # still prompts for password, could change the wheel group behaviour or add new line to sudoers
        visudo
        # add line at end user20 ALL=(ALL) NOPASSWD: ALL
        ```