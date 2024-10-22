Asghar Ghori - Sample RHCSA Exam 2

    * Setup a virtual machine RHEL 8 Server for GUI. Add a 10GB disk for the OS and use the default storage partitioning. Add 1 400MB disk. Add a network interface, but do not configure the hostname and network connection.

    * Using the nmcli command, configure a network connection on the primary network device with IP address 192.168.0.242/24, gateway 192.168.0.1, and nameserver 192.168.0.1:
        ```shell
        nmcli con add ifname enp0s3 con-name mycon type ethernet ip4 192.168.0.242/24 gw4 192.168.0.1 ipv4.dns "192.168.0.1"
        # man nmcli-examples can be referred to if you forget format
        nmcli con show mycon | grep ipv4 # confirm
        ```
    
    * Using the hostnamectl command, set the system hostname to rhcsa2.example.com and alias rhcsa2. Make sure that the new hostname is reflected in the command prompt:
        ```shell
        hostnamectl set-hostname rhcsa2.example.com
        hostnamectl set-hostname --static rhcsa2 # not necessary due to format of FQDN
        # the hostname already appears in the command prompt
        ```

    * Create a user account called user70 with UID 7000 and comments "I am user70". Set the maximum allowable inactivity for this user to 30 days:
        ```shell
        useradd -u 7000 -c "I am user70" user70
        chage -I 30 user70
        ```

    * Create a user account called user50 with a non-interactive shell:
        ```shell
        useradd user50 -s /sbin/nologin
        ```

    * Create a file called testfile1 under `/tmp` with ownership and owning group set to root. Configure access ACLs on the file and give user10 read and write access. Test access by logging in as user10 and editing the file:
        ```shell
        useradd user10
        passwd user10 # set password
        touch /tmp/testfile1
        cd /tmp
        setfacl -m u:user10:rw testfile1
        sudo su user10
        vi /tmp/testfile1 # can edit the file
        ```

    * Attach the RHEL 8 ISO image to the VM and mount it persistently to `/mnt/dvdrom`. Define access to both repositories and confirm:
        ```shell
        mkdir /mnt/dvdrom
        lsblk # rom is at /dev/sr0
        mount /dev/sr0 /mnt/dvdrom
        blkid /dev/sr0 >> /etc/fstab
        vi /etc/fstab
        # format line with UUID /mnt/dvdrom iso9660 defaults 0 0
        vi /etc/yum.repos.d/image.repo
        # contents of image.repo
        #####
        #[BaseOS]
        #name=BaseOS
        #baseurl=file:///mnt/dvdrom/BaseOS
        #enabled=1
        #gpgenabled=1
        #gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
        #
        #[AppStream]
        #name=AppStream
        #baseurl=file:///mnt/dvdrom/AppStream
        #enabled=1
        #gpgenabled=1
        #gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
        #####
        yum repolist # confirm
        ```

    * Create a logical volume called lv1 of size equal to 10 LEs in vg1 volume group (create vg1 with PE size 8MB in a partition on the 400MB disk). Initialise the logical volume with XFS file system type and mount it on `/mnt/lvfs1`. Create a file called lv1file1 in the mount point. Set the file system to automatically mount at each system reboot:
        ```shell
        parted /dev/sdb
        mklabel msdos
        mkpart
        # enter primary
        # enter xfs
        # enter 0
        # enter 100MB
        vgcreate vg1 -s 8MB /dev/sdb1
        lvcreate --name lv1 -l 10 vg1 /dev/sdb1
        mkfs.xfs /dev/mapper/vg1-lv1
        mkdir /mnt/lvfs1
        vi /etc/fstab
        # add line for /dev/mapper/vg1-lv1 /mnt/lvfs1 xfs defaults 0 0
        mount -a
        df -h  # confirm
        touch /mnt/lvfs1/hi
        ```

    * Add a group called group20 and change group membership on `/mnt/lvfs1` to group20. Set read/write/execute permissions on `/mnt/lvfs1` for the owner and group members, and no permissions for others:
        ```shell
        groupadd group20
        chgrp group20 -R /mnt/lvfs1
        chmod 770 -R /mnt/lvfs1
        ```

    * Extend the file system in the logical volume lv1 by 64MB without unmounting it and without losing any data:
        ```shell
        lvextend -L +64MB vg1/lv1 /dev/sdb1
        # realised that the partition of 100MB isn't enough
        parted /dev/sdb
        resizepart
        # expand partition 1 to 200MB
        pvresize /dev/sdb1
        lvextend -L +64MB vg1/lv1 /dev/sdb1
        ```

    * Create a swap partition of size 85MB on the 400MB disk. Use its UUID and ensure it is activated after every system reboot:
        ```shell
        parted /dev/sdb
        mkpart
        # enter primary
        # enter linux-swap
        # enter 200MB
        # enter 285MB
        mkswap /dev/sdb2
        vi /etc/fstab
        # add line for UUID swap swap defaults 0 0
        swapon -a
        ```

    * Create a disk partition of size 100MB on the 400MB disk and format it with Ext4 file system structures. Assign label stdlabel to the file system. Mount the file system on `/mnt/stdfs1` persistently using the label. Create file stdfile1 in the mount point:
        ```shell
        parted /dev/sdb
        mkpart
        # enter primary
        # enter ext4
        # enter 290MB
        # enter 390MB
        mkfs.ext4 -L stdlabel /dev/sdb3
        mkdir /mnt/stdfs1
        vi /etc/fstab
        # add line for UUID /mnt/stdfs1 ext4 defaults 0 0
        touch /mnt/stdfs1/hi
        ```

    * Use tar and gzip to create a compressed archive of the `/usr/local` directory. Store the archive under `/tmp` using a filename of your choice:
        ```shell
        tar -czvf /tmp/local.tar.gz /usr/local
        ```

    * Create a directory `/direct01` and apply SELinux contexts for `/root`:
        ```shell
        mkdir /direct01
        ll -Z
        # direct01 has unconfined_u:object_r:default_t:s0
        # root has system_u:object_r:admin_home_t:s0
        semanage fcontext -a -t admin_home_t -s system_u "/direct01(/.*)?" 
        restorecon -R -v /direct01
        ll -Zrt # confirm
        ```

    * Set up a cron job for user70 to search for core files in the `/var` directory and copy them to the directory `/tmp/coredir1`. This job should run every Monday at 1:20 a.m:
        ```shell
        mkdir /tmp/coredir1
        crontab -u user70 -e
        20 1 * * Mon find /var -name core -type f exec cp '{}' /tmp/coredir1 \;
        crontab -u user70 -l # confirm
        ```

    * Search for all files in the entire directory structure that have been modified in the past 30 days and save the file listing in the `/var/tmp/modfiles.txt` file:
        ```shell
        find / -mtime -30 >> /var/tmp/modfiles.txt
        ```

    * Modify the bootloader program and set the default autoboot timer value to 2 seconds:
        ```shell
        vi /etc/default/grub
        # set GRUB_TIMEOUT=2
        grub2-mkconfig -o /boot/grub2/grub.cfg
        ```

    * Determine the recommended tuning profile for the system and apply it:
        ```shell
        tuned-adm recommend
        # virtual-guest is returned
        tuned-adm active
        # virtual-guest is returned
        # no change required
        ```

    * Configure Chrony to synchronise system time with the hardware clock:
        ```shell
        systemctl status chronyd.service
        vi /etc/chrony.conf
        # everything looks alright
        ```

    * Install package group called "Development Tools", and capture its information in `/tmp/systemtools.out` file:
        ```shell
        yum grouplist # view available groups
        yum groupinstall "Development Tools" -y >> /tmp/systemtools.out
        ```

    * Lock user account user70. Use regular expressions to capture the line that shows the lock and store the output in file `/tmp/user70.lock`:
        ```shell
        usermod -L user70
        grep user70 /etc/shadow >> /tmp/user70.lock # observe !
        ```