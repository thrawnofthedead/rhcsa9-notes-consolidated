### RHCSA Exercises

1. Recovery and Practise Tasks

    * Recover the system and fix repositories:
        ```shell
        # press e at grub menu
        rd.break # add to line starting with "linux16"
        # Replace line containing "BAD" with "x86_64"
        mount -o remount, rw /sysroot
        chroot /sysroot
        passwd
        touch /.autorelabel
        # reboot
        # reboot - will occur automaticaly after relabel (you can now login)
        grub2-mkconfig -o /boot/grub2/grub.cfg # fix grub config
        yum repolist all
        # change files in /etc/yum.repos.d to enable repository
        yum update -y
        # reboot
        ```

    * Add 3 new users alice, bob and charles. Create a marketing group and add these users to the group. Create a directory `/marketing` and change the owner to alice and group to marketing. Set permissions so that members of the marketing group can share documents in the directory but nobody else can see them. Give charles read-only permission. Create an empty file in the directory: 
        ```shell
        useradd alice
        useradd bob
        useradd charles
        groupadd marketing
        mkdir /marketing
        usermod -aG marketing alice
        usermod -aG marketing bob
        usermod -aG marketing charles
        chgrp marketing marketing # may require restart to take effect
        chmod 770 marketing
        setfacl -m u:charles:r marketing
        setfacl -m g:marketing:-wx marketing
        touch file
        ```

    * Set the system time zone and configure the system to use NTP:
        ```shell
        yum install chrony
        systemctl enable chronyd.service
        systemctl start chronyd.service
        timedatectl set-timezone Australia/Sydney
        timedatectl set-ntp true
        ```

    * Install and enable the GNOME desktop:
        ```shell
        yum grouplist
        yum groupinstall "GNOME Desktop" -y
        systemtctl set-default graphical.target
        reboot
        ```

    * Configure the system to be an NFS client:
        ```shell
        yum install nfs-utils
        ```

    * Configure password aging for charles so his password expires in 60 days:
        ```shell
        chage -M 60 charles
        chage -l charles # to confirm result
        ```

    * Lock bobs account:
        ```shell
        passwd -l bob
        passwd --status bob # to confirm result
        ```

    * Find all setuid files on the system and save the list to `/testresults/setuid.list`:
        ```shell
        find / -perm /4000 > setuid.list
        ```

    * Set the system FQDN to *centos.local* and alias to *centos*:
        ```shell
        hostnamectl set-hostname centos --pretty
        hostnamectl set-hostname centos.local
        hostname -s # to confirm result
        hostname # to confirm result
        ```

    * As charles, create a once-off job that creates a file called `/testresults/bob` containing the text "Hello World. This is Charles." in 2 days later:
        ```shell
        vi hello.sh
        # contents of hello.sh
        #####
        #!/bin/bash
        # echo "Hello World. This is Charles." > bob
        #####
        chmod 755 hello.sh
        usermod charles -U -e -- "" # for some reason the account was locked
        at now + 2 days -f /testresults/bob/hello.sh
        cd /var/spool/at # can check directory as root to confirm
        atq # check queued job as charles
        # atrm 1 # can remove the job using this command
        ```

    * As alice, create a periodic job that appends the current date to the file `/testresults/alice` every 5 minutes every Sunday and Wednesday between the hours of 3am and 4am. Remove the ability of bob to create cron jobs:
        ```shell
        echo "bob" >> /etc/at.deny
        sudo -i -u alice
        vi addDate.sh
        # contents of hello.sh
        #####
        ##!/bin/bash
        #date >> alice
        #####
        /testresults/alice/addDate.sh
        crontab -e
        # */5 03,04 * * sun,wed /testresults/alice/addDate.sh
        crontab -l # view crontab
        # crontab -r can remove the job using this command
        ```

    * Set the system SELinux mode to permissive:
        ```shell
        setstatus # confirm current mode is not permissive
        vi /etc/selinux/config # Update to permissive
        reboot
        setstatus # confirm current mode is permissive
        ```

    * Create a firewall rule to drop all traffic from 10.10.10.*:
        ```shell
        firewall-cmd --zone=drop --add-source 10.10.10.0/24
        firewall-cmd --list-all --zone=drop # confirm rule is added
        firewall-cmd --permanent --add-source 10.10.10.0/24
        reboot
        firewall-cmd --list-all --zone=drop # confirm rule remains
        ```

1. Linux Academy - Using SSH, Redirection, and Permissions in Linux

    * Enable SSH to connect without a password from the dev user on server1 to the dev user on server2:
        ```shell
        ssh dev@3.85.167.210
        ssh-keygen # created in /home/dev/.ssh
        ssh-copy-id 34.204.14.34
        ```    

    * Copy all tar files from `/home/dev/` on server1 to `/home/dev/` on server2, and extract them making sure the output is redirected to `/home/dev/tar-output.log`:
        ```shell
        scp *.tar* dev@34.204.14.34:/home/dev
        tar xfz deploy_script.tar.gz > tar-output.log
        tar xfz deploy_content.tar.gz >> tar-output.log
        ```
    
    * Set the umask so that new files are only readable and writeable by the owner:
        ```shell
        umask 0066 # default is 0666, subtract 0066 to get 0600
        ```

    * Verify the `/home/dev/deploy.sh` script is executable and run it:
        ```shell
        chmod 711 deploy.sh
        ./deploy.sh
        ```

1. Linux Academy - Storage Management

    * Create a 2GB GPT Partition:
        ```shell
        lsblk # observe nvme1n1 disk
        sudo gdisk /dev/nvme1n1
        # enter n for new partition
        # accept default partition number
        # accept default starting sector
        # for the ending sector, enter +2G to create a 2GB partition
        # accept default partition type
        # enter w to write the partition information
        # enter y to proceed
        lsblk # observe nvme1n1 now has partition
        partprobe # inform OS of partition change
        ```

    * Create a 2GB MBR Partition:
        ```shell
        lsblk # observe nvme2n1 disk
        sudo fdisk /dev/nvme2n1
        # enter n for new partition
        # accept default partition type
        # accept default partition number
        # accept default first sector
        # for the ending sector, enter +2G to create a 2GB partition
        # enter w to write the partition information
        ```

    * Format the GPT Partition with XFS and mount the device persistently:
        ```shell
        sudo mkfs.xfs /dev/nvme1n1p1
        sudo blkid # observe nvme1n1p1 UUID
        vi /etc/fstab
        # add a line with the new UUID and specify /mnt/gptxfs
        mkdir /mnt/gptxfs
        sudo mount -a
        mount # confirm that it's mounted
        ```

    * Format the MBR Partition with ext4 and mount the device persistently:
        ```shell
        sudo mkfs.ext4 /dev/nvme2n1p1
        mkdir /mnt/mbrext4
        mount /dev/nvme2n1p1 /mnt/mbrext4
        mount # confirm that it's mounted
        ```

1. Linux Academy - Working with LVM Storage

    * Create Physical Devices:
        ```shell
        lsblk # observe disks xvdf and xvdg
        pvcreate /dev/xvdf /dev/xvdg
        ```

    * Create Volume Group:
        ```shell
        vgcreate RHCSA /dev/xvdf /dev/xvdg
        vgdisplay # view details
        ```

    * Create a Logical Volume:
        ```shell
        lvcreate -n pinehead -L 3G RHCSA
        lvdisplay # or lvs, to view details
        ```

    * Format the LV as XFS and mount it persistently at `/mnt/lvol`:
        ```shell
        fdisk -l # get path for lv
        mkfs.xfs /dev/mapper/RHCSA-pinehead
        mkdir /mnt/lvol
        blkid # copy UUID for /dev/mapper/RHCSA-pinehead
        echo "UUID=76747796-dc33-4a99-8f33-58a4db9a2b59" >> /etc/fstab
        # add the path /mnt/vol and copy the other columns
        mount -a
        mount # confirm that it's mounted
        ```

    * Grow the mount point by 200MB:
        ```shell
        lvextend -L +200M /dev/RHCSA/pinehead
        ```

1. Linux Academy - Network File Systems

    * Set up a SAMBA share:
        ```shell
        # on the server
        yum install samba -y
        vi /etc/samba/smb.conf
        # add the below block
        #####
        #[share]
        #    browsable = yes
        #    path = /smb
        #    writeable = yes
        #####
        useradd shareuser
        smbpasswd -a shareuser # enter password
        mkdir /smb
        systemctl start smb
        chmod 777 /smb
        
        # on the client
        mkdir /mnt/smb
        yum install cifs-utils -y
        # on the server hostname -I shows private IP
        mount -t cifs //10.0.1.100/share /mnt/smb -o username=shareuser,password= # private ip used
        ```

    * Set up the NFS share:
        ```shell
        # on the server
        yum install nfs-utils -y
        mkdir /nfs
        echo "/nfs *(rw)" >> /etc/exports
        chmod 777 /nfs
        exportfs -a
        systemctl start {rpcbind,nfs-server,rpc-statd,nfs-idmapd}

        # on the client
        yum install nfs-utils -y
        mkdir /mnt/nfs
        showmount -e 10.0.1.101 # private ip used
        systemctl start rpcbind
        mount -t nfs 10.0.1.101:/nfs /mnt/nfs
        ```

1. Linux Academy - Maintaining Linux Systems

    * Schedule a job to update the server midnight tonight:
        ```shell
        echo "dnf update -y" > update.sh
        chmod +x update.sh
        at midnight -f update.sh
        atq # to verify that job is scheduled
        ```

    * Modify the NTP pools:
        ```shell
        vi /etc/chrony.conf
        # modify the pool directive at the top of the file
        ```

    * Modify GRUB to boot a different kernel:
        ```shell
        grubby --info=ALL # list installed kernels
        grubby --set-default-index=1
        grubby --default-index # verify it worked
        ```
	* Modify GRUB to show suppressed boot information
	  ```shell
	  grubby --remove-args="rhgb quiet" --update-kernel=ALL #remove suppression 
```
1. Linux Academy - Managing Users in Linux

    * Create the superhero group:
        ```shell
        groupadd superhero
        ```

    * Add user accounts for Tony Stark, Diana Prince, and Carol Danvers and add them to the superhero group:
        ```shell
        useradd tstark -G superhero
        useradd cdanvers -G superhero
        useradd dprince -G superhero
        ```

    * Replace the primary group of Tony Stark with the wheel group:
        ```shell
        usermod tstark -ag wheel
        grep wheel /etc/group # to verify
        ```

    * Lock the account of Diana Prince:
        ```shell
        usermod -L dprince 
        chage dprince -E 0
        ```

1. Linux Academy - SELinux Learning Activity

    * Fix the SELinux permission on `/opt/website`:
        ```shell
        cd /var/www # the default root directory for a web server
        ls -Z # observe permission on html folder
        semanage fcontext -a -t httpd_sys_content_t '/opt/website(/.*)'
        restorecon /opt/website
        ```

    * Deploy the website and test:
        ```shell
        mv /root/index.html /opt/website
        curl localhost/index.html # receive connection refused response
        systemctl start httpd # need to start the service
        setenforce 0 # set to permissive to allow for now
        ```

    * Resolve the error when attempting to access `/opt/website`:
        ```shell
        ll -Z # notice website has admin_home_t
        restorecon /opt/website/index.html
        ```

1. Linux Academy - Setting up VDO

    * Install VDO and ensure the service is running:
        ```shell
        dnf install vdo -y
        systemctl start vdo && systemctl enable vdo
        ```

    * Setup a 100G VM storage volume:
        ```shell
        vdo create --name=ContainerStorage --device=/dev/nvme1n1 --vdoLogicalSize=100G --sparseIndex=disabled
        # spareIndex set to meet requirement of dense index deduplication
        mkfs.xfs -K /dev/mapper/ContainerStorage
        mkdir /mnt/containers
        mount /dev/mapper/ContainerStorage /mnt/containers
        vi /etc/fstab # add line /dev/mapper/ContainerStorage /mnt/containers xfs defaults,_netdev,x-systemd.device-timeout=0,x-systemd.requires=vdo.service 0 0
        ```

    * Setup a 60G website storage volume:
        ```shell
        vdo create --name=WebsiteStorage --device=/dev/nvme2n1 --vdoLogicalSize=60G --deduplication=disabled
        # deduplication set to meet requirement of no deduplication
        mkfs.xfs -K /dev/mapper/WebsiteStorage
        mkdir /mnt/website
        mount /dev/mapper/WebsiteFiles /mnt/website
        vi /etc/fstab # add line for /dev/mapper/WebsiteStorage /mnt/website xfs defaults,_netdev,x-systemd.device-timeout=0,x-systemd.requires=vdo.service 0 0
        ```

1. Linux Academy - Final Practise Exam

    * Start the guest VM:
        ```shell
        # use a VNC viewer connect to IP:5901
        virsh list --all
        virsh start --centos7.0
        # we already have the VM installed, we just needed to start it (so we don't need virt-install)
        dnf install virt-viewer -y
        virt-viewer centos7.0 # virt-manager can also be used
        # now we are connected to the virtual machine
        # send key Ctrl+Alt+Del when prompted for password, as we don't know it
        # press e on GRUB screen
        # add rd.break on the linux16 line
        # now at the emergency console
        mount -o remount, rw /sysroot
        chroot /sysroot
        passwd
        touch /.autorelabel
        reboot -f # needs -f to work for some reason
        # it will restart when it completes relabelling
        ```

    * Create three users (Derek, Tom, and Kenny) that belong to the instructors group. Prevent Tom's user from accessing a shell, and make his account expire 10 day from now:
        ```shell
        groupadd instructors
        useradd derek -G instructors
        useradd tom -s /sbin/nologin -G instructors
        useradd kenny -G instructors
        chage tom -E 2020-10-14
        chage -l tom # to check
        cat /etc/group | grep instructors # to check
        ```

    * Download and configure apache to serve index.html from `/var/web` and access it from the host machine:
        ```shell
        # there is some setup first to establish connectivity/repo
        nmcli device # eth0 shown as disconnected
        nmcli connection up eth0
        vi /etc/yum.repos.d/centos7.repo
        # contents of centos.repo
        #####
        #[centos7]
        #name = centos
        #baseurl = http://mirror.centos.org/centos/7/os/x86_64/
        #enabled = 1
        #gpgcheck = 1
        #gpgkey = file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
        #####
        yum repolist # confirm
        yum install httpd -y
        systemctl start httpd.service
        mkdir /var/web
        vi /etc/httpd/conf/httpd.conf
        # change DocumentRoot to "/var/web"
        # change Directory tag to "/var/web"
        # change Directory tag to "/var/web/html"
        echo "Hello world" > /var/web/index.html
        systemctl start httpd.service
        ip a s # note the first inet address for eth0 # from the guest VM
        curl http://192.168.122.213/ # from the host 
        # note that no route to host returned
        firewall-cmd --list-services # notice no http service
        firewall-cmd --add-service=http --permanent
        firewall-cmd --reload
        firewall-cmd --list-services # confirm http service
        curl http://192.168.122.255/ # from the host 
        # note that 403 error is returned
        # ll -Z comparision between /var/web and /var/www shows that the SELinux type of index.html should be httpd_sys_context_t and not var_t
        yum provides \*/semanage # suggests policycoreutils-python
        yum install policycoreutils-python -y
        semanage fcontext -a -t httpd_sys_content_t "/var/web(/.*)?"
        restorecon -R -v /var/web
        curl http://192.168.122.255/ # from the host - success!
        ```

    * Configure umask to ensure all files created by any user cannot be accessed by the "other" users:
        ```shell
        umask 0026 # also reflect change in /etc/profile and /etc/bashrc
        # default for files is 0666 so will be 0640 after mask
        ```

    * Find all files in `/etc` (not including subdirectories) that are older than 720 days, and output a list to `/root/oldfiles`:
        ```shell
        find /etc -maxdepth 1 -mtime +720 > /root/oldfiles 
        ```

    * Find all log messages in `/var/log/messages` that contain "ACPI", and export them to a file called `/root/logs`. Then archive all of `/var/log` and save it to `/tmp/log_archive.tgz`:
        ```shell
        grep "ACPI" /var/log/messages > /root/logs
        tar -czf /tmp/log_archive.tgz /var/log/ # note f flag must be last!
        ```

    * Modify the GRUB timeout and make it 1 second instead of 5 seconds:
        ```shell
        find / -iname grub.cfgreboot
        # /etc/grub.d, /etc/default/grub and grub2-mkconfig referred to in /boot/grub2/grub.cfg
        vi /etc/default/grub # change GRUB_TIMEOUT to 1
        grub2-mkconfig -o /boot/grub2/grub.cfg
        reboot # confirm timeout now 1 second
        ```
	* Set a password for GRUB
	```shell
	grub2-setpassword # sets the password for editing GRUB - user is the user that runs the command
	# This is stored in /etc/grub.d/01_users - delete this file if you ever forget the password 
```
    * Create a daily cron job at 4:27PM for the Derek user that runs `cat /etc/redhat-release` and redirects the output to `/home/derek/release`:
        ```shell
        cd /home/derek
        vi script.sh
        # contents of script.sh
        #####
        ##!/bin/sh
        #cat /etc/redhat-release > /home/derek/release
        #####
        chmod +x script.sh
        crontab -u derek -e
        # contents of crontab
        #####
        #27 16 * * * /home/derek/script.sh
        #####
        crontab -u derek -l # confirm
        ```

    * Configure `time.nist.gov` as the only NTP Server:
        ```shell
        vi /etc/chrony.conf
        # replace lines at the top with server time.nist.gov
        ```

    * Create an 800M swap partition on the `vdb` disk and use the UUID to ensure that it is persistent:
        ```shell
        fdisk -l # note that we have one MBR partitions
        fdisk /dev/vdb
        # select n
        # select p
        # select default
        # select default
        # enter +800M
        # select w
        partprobe
        lsblk # confirm creation
        mkswap /dev/vdb1
        vi /etc/fstab
        # add line containing UUID and swap for the next 2 columns
        swapon -a
        swap # confirm swap is available
        ```

    * Create a new logical volume (LV-A) with a size of 30 extents that belongs to the volume group VG-A (with a PE size of 32M). After creating the volume, configure the server to mount it persistently on `/mnt`:
        ```shell
        # observe through fdisk -l and df -h that /dev/vdc is available with no file system
        yum provides pvcreate # lvm2 identified
        yum install lvm2 -y
        pvcreate /dev/vdc
        vgcreate VG-A /dev/vdc -s 32M
        lvcreate -n LV-A -l 30 VG-A
        mkfs.xfs /dev/VG-A/LV-A
        # note in directory /dev/mapper the name is VG--A-LV--A
        # add an entry to /etc/fstab at /dev/mapper/VG--A-LV--A and /mnt (note that you can mount without the UUID here)
        mount -a
        df -h # verify that LV-A is mounted
        ```

    * On the host, not the guest VM, utilise ldap.linuxacademy.com for SSO, and configure AutoFS to mount user's home directories on login. Make sure to use Kerberos:
        ```shell
        # this objective is no longer required in RHCSA 8
        ```

    * Change the hostname of the guest to "RHCSA":
        ```shell
        hostnamectl set-hostname rhcsa
        ```

1. Asghar Ghori - Exercise 3-1: Create Compressed Archives

    * Create tar files compressed with gzip and bzip2 and extract them:
        ```shell
        # gzip
        tar -czf home.tar.gz /home
        tar -tf /home.tar.gz # list files
        tar -xf home.tar.gz

        # bzip
        tar -cjf home.tar.bz2 /home
        tar -xf home.tar.bz2 -C /tmp
        ```

1. Asghar Ghori - Exercise 3-2: Create and Manage Hard Links

    * Create an empty file *hard1* under */tmp* and display its attributes. Create hard links *hard2* and *hard3*. Edit *hard2* and observe the attributes. Remove *hard1* and *hard3* and list the attributes again:
        ```shell
        touch hard1
        ln hard1 hard2
        ln hard1 hard3
        ll -i
        # observe link count is 3 and same inode number
        echo "hi" > hard2
        # observe file size increased to the same value for all files
        rm hard1
        rm hard3
        # observe link count is 1
        ```

1. Asghar Ghori - Exercise 3-3: Create and Manage Soft Links

    * Create an empty file *soft1* under `/root` pointing to `/tmp/hard2`. Edit *soft1* and list the attributes after editing. Remove *hard2* and then list *soft1*:
        ```shell
        ln -s /tmp/hard2 soft1
        ll -i
        # observe soft1 and hard2 have the same inode number
        echo "hi" >> soft1
        # observe file size increased
        cd /root
        ll -i 
        # observe the soft link is now broken
        ```

1. Asghar Ghori - Exercise 4-1: Modify Permission Bits Using Symbolic Form

    * Create a file *permfile1* with read permissions for owner, group and other. Add an execute bit for the owner and a write bit for group and public. Revoke the write bit from public and assign read, write, and execute bits to the three user categories at the same time. Revoke write from the owning group and write, and execute bits from public:
        ```shell
        touch permfile1
        chmod 444 permfile1
        chmod -v u+x,g+w,o+w permfile1
        chmod -v o-w,a=rwx permfile1
        chmod -v g-w,o-wx permfile1
        ```

1. Asghar Ghori - Exercise 4-2: Modify Permission Bits Using Octal Form

    * Create a file *permfile2* with read permissions for owner, group and other. Add an execute bit for the owner and a write bit for group and public. Revoke the write bit from public and assign read, write, and execute permissions to the three user categories at the same time:
        ```shell
        touch permfile2
        chmod 444 permfile2
        chmod -v 566 permfile2
        chmod -v 564 permfile2
        chmod -v 777 permfile2
        ```

1. Asghar Ghori - Exercise 4-3: Test the Effect of setuid Bit on Executable Files

    * As root, remove the setuid bit from `/usr/bin/su`. Observe the behaviour for another user attempting to switch into root, and then add the setuid bit back:
        ```shell
        chmod -v u-s /usr/bin/su
        # users now receive authentication failure when attempting to switch
        chmod -v u+s /usr/bin/su
        ```

1. Asghar Ghori - Exercise 4-4: Test the Effect of setgid Bit on Executable Files

    * As root, remove the setgid bit from `/usr/bin/write`. Observe the behaviour when another user attempts to run this command, and then add the setgid bit back:
        ```shell
        chmod -v g-s /usr/bin/write
        # Other users can no longer write to root
        chmod -v g+s /usr/bin/write
        ```

1. Asghar Ghori - Exercise 4-5: Set up Shared Directory for Group Collaboration

    * Create users *user100* and *user200*. Create a group *sgrp* with GID 9999 and add *user100* and *user200* to this group. Create a directory `/sdir` with ownership and owning groups belong to *root* and *sgrp*, and set the setgid bit on */sdir* and test:
        ```shell
        groupadd sgrp -g 9999
        useradd user100 -G sgrp 
        useradd user200 -G sgrp 
        mkdir /sdir
        chown root:sgrp sdir
        chmod g+s,g+w sdir
        # as user100
        cd /sdir
        touch file
        # owning group is sgrp and not user100 due to setgid bit
        # as user200
        vi file
        # user200 can also read and write
        ```

1. Asghar Ghori - Exercise 4-6: Test the Effect of Sticky Bit

    * Create a file under `/tmp` as *user100* and try to delete it as *user200*. Unset the sticky bit on `/tmp` and try to erase the file again. Restore the sticky bit on `/tmp`:
        ```shell
        # as user100
        touch /tmp/myfile
        # as user200
        rm /tmp/myfile
        # cannot remove file: Operation not permitted
        # as root
        chmod -v o-t tmp
        # as user200
        rm /tmp/myfile
        # file can now be removed
        # as root
        chmod -v o+t tmp
        ```

1. Asghar Ghori - Exercise 4-7: Identify, Apply, and Erase Access ACLs

    * Create a file *acluser* as *user100* in `/tmp` and check if there are any ACL settings on the file. Apply access ACLs on the file for *user100* for read and write access. Add *user200* to the file for full permissions. Remove all access ACLs from the file:
        ```shell
        # as user100
        touch /tmp/acluser
        cd /tmp
        getfacl acluser
        # no ACLs on the file
        setfacl -m u:user100:rw,u:user200:rwx acluser
        getfacl acluser
        # ACLs have been added
        setfacl -x user100,user200 acluser
        getfacl acluser
        # ACLs have been removed
        ```

1. Asghar Ghori - Exercise 4-8: Apply, Identify, and Erase Default ACLs

    * Create a directory *projects* as *user100* under `/tmp`. Set the default ACLs on the directory for *user100* and *user200* to give them full permissions. Create a subdirectory *prjdir1* and a file *prjfile1* under *projects* and observe the effects of default ACLs on them. Delete the default entries:
        ```shell
        # as user100
        cd /tmp
        mkdir projects
        getfacl projects
        # No default ACLs for user100 and user200
        setfacl -dm u:user100:rwx,u:user200:rwx projects
        getfacl projects
        # Default ACLs added for user100 and user200
        mkdir projects/prjdir1
        getfacl prjdir1
        # Default ACLs inherited
        touch prjdir1/prjfile1
        getfacl prjfile1
        # Default ACLs inherited
        setfacl -k projects
        ```

1. Asghar Ghori - Exercise 5-1: Create a User Account with Default Attributes

    * Create *user300* with the default attributes in the *useradd* and *login.defs* files. Assign this user a password and show the line entries from all 4 authentication files:
        ```shell
        useradd user300
        passwd user300
        grep user300 /etc/passwd /etc/shadow /etc/group /etc/gshadow
        ```


1. Asghar Ghori - Exercise 5-2: Create a User Account with Custom Values

    * Create *user300* with the default attributes in the *useradd* and *login.defs* files. Assign this user a password and show the line entries from all 4 authentication files:
        ```shell
        useradd user300
        passwd user300
        grep user300 /etc/passwd /etc/shadow /etc/group /etc/gshadow
        ```

1. Asghar Ghori - Exercise 5-3: Modify and Delete a User Account

    * For *user200* change the login name to *user200new*, UID to 2000, home directory to `/home/user200new`, and login shell to `/sbin/nologin`. Display the line entry for *user2new* from the *passwd* for validation. Remove this user and confirm the deletion:
        ```shell
        usermod -l user200new -m -d /home/user200new -s /sbin/nologin -u 2000 user200
        grep user200new /etc/passwd # confirm updated values
        userdel -r user200new
        grep user200new /etc/passwd # confirm user200new deleted
        ```

1. Asghar Ghori - Exercise 5-4: Create a User Account with No-Login Access

    * Create an account *user400* with default attributes but with a non-interactive shell. Assign this user the nologin shell to prevent them from signing in. Display the new line entry frmo the *passwd* file and test the account:
        ```shell
        useradd user400 -s /sbin/nologin
        passwd user400 # change password
        grep user400 /etc/passwd
        sudo -i -u user400 # This account is currently not available
        ```

1. Asghar Ghori - Exercise 6-1: Set and Confirm Password Aging with chage

    * Configure password ageing for user100 using the *chage* command. Set the mindays to 7, maxdays to 28, and warndays to 5. Verify the new settings. Rerun the command and set account expiry to January 31, 2020:
        ```shell
        chage -m 7 -M 28 -W 5 user100
        chage -l user100
        chage -E 2021-01-31 user100
        chage -l
        ```

1. Asghar Ghori - Exercise 6-2: Set and Confirm Password Aging with passwd

    * Configure password aging for *user100* using the *passwd* command. Set the mindays to 10, maxdays to 90, and warndays to 14, and verify the new settings. Set the number of inactivity days to 5 and ensure that the user is forced to change their password upon next login:
        ```shell
        passwd -n 10 -x 90 -w 14 user100
        passwd -S user100 # view status
        passwd -i 5 user100
        passwd -e user100
        passwd -S user100
        ```

1. Asghar Ghori - Exercise 6-3: Lock and Unlock a User Account with usermod and passwd

    * Disable the ability of user100 to log in using the *usermod* and *passwd* commands. Verify the change and then reverse it:
        ```shell
        grep user100 /etc/shadow # confirm account not locked by absence of "!" in password
        passwd -l user100 # usermod -L also works
        grep user100 /etc/shadow
        passwd -u user100 # usermod -U also works
        ```

1. Asghar Ghori - Exercise 6-4: Create a Group and Add Members

    * Create a group called *linuxadm* with GID 5000 and another group called *dba* sharing the GID 5000. Add *user100* as a secondary member to group *linxadm*:
        ```shell
        groupadd -g 5000 linuxadm
        groupadd -o -g 5000 dba # note need -o to share GID
        usermod -G linuxadm user100
        grep user100 /etc/group # confirm user added to group
        ```

1. Asghar Ghori - Exercise 6-5: Modify and Delete a Group Account

    * Change the *linuxadm* group name to *sysadm* and the GID to 6000. Modify the primary group for user100 to *sysadm*. Remove the *sysadm* group and confirm:
        ```shell
        groupmod -n sysadm -g 6000 linuxadm
        usermod -g sysadm user100
        groupdel sysadm # can't remove while user100 has as primary group
        ```

1. Asghar Ghori - Exercise 6-6: Modify File Owner and Owning Group

    * Create a file *file10* and a directory *dir10* as *user200* under `/tmp`, and then change the ownership for *file10* to *user100* and the owning group to *dba* in 2 separate transactions. Apply ownership on *file10* to *user200* and owning group to *user100* at the same time. Change the 2 attributes on the directory to *user200:dba* recursively:
        ```shell
        # as user200
        mkdir /tmp/dir10
        touch /tmp/file10
        sudo chown user100 /tmp/file10         
        sudo chgrp dba /tmp/file10
        sudo chown user200:user100 /tmp/file10
        sudo chown -R user200:user100 /tmp/dir10
        ```

1. Asghar Ghori - Exercise 7-1: Modify Primary Command Prompt

    * Customise the primary shell prompt to display the information enclosed within the quotes "\<username on hostname in pwd\>:" using variable and command substitution. Edit the `~/.profile`file for *user100* and define the new value in there for permanence:
        ```shell
        export PS1="< $LOGNAME on $(hostname) in \$PWD>"
        # add to ~/.profile for user100
        ```

1. Asghar Ghori - Exercise 8-1: Submit, View, List, and Remove an at Job

    * Submit a job as *user100* to run the *date* command at 11:30pm on March 31, 2021, and have the output and any error messages generated redirected to `/tmp/date.out`. List the submitted job and then remove it:
        ```shell
        # as user100
        at 11:30pm 03/31/2021
        # enter "date &> /tmp/date.out"
        atq # view job in queue
        at -c 1 # view job details
        atrm 1 # remove job
        ```

1. Asghar Ghori - Exercise 8-2: Add, List, and Remove a Cron Job

    * Assume all users are currently denied access to cron. Submit a cron job as *user100* to echo "Hello, this is a cron test.". Schedule this command to execute at every fifth minute past the hour between 10:00 am and 11:00 am on the fifth and twentieth of every month. Have the output redirected to `/tmp/hello.out`. List the cron entry and then remove it:
        ```shell
        # as root
        echo "user100" > /etc/cron.allow
        # ensure cron.deny is empty
        # as user100
        crontab
        # */5 10,11 5,20 * * echo "Hello, this is a cron test." >> /tmp/hello.out
        crontab -e # list
        crontab -l # remove
        ```

1. Asghar Ghori - Exercise 9-1: Perform Package Management Tasks Using rpm

    * Verify the integrity and authenticity of a package called *dcraw* located in the `/mnt/AppStream/Packages` directory on the installation image and then install it. Display basic information about the package, show files it contains, list documentation files, verify the package attributes and remove the package: 
        ```shell
        ls -l /mnt/AppStream/Packages/dcraw*
        rpmkeys -K /mnt/AppStream/Packages/dcraw-9.27.0-9.e18.x86_64.rpm # check integrity
        sudo rpm -ivh /mnt/AppStream/Packages/dcraw-9.27.0-9.e18.x86_64.rpm # -i is install, -v is verbose and -h is hash
        rpm -qi dcraw # -q is query and -i is install
        rpm -qd dcraw # -q is query and -d is docfiles
        rpm -Vv dcraw # -V is verify and -v is verbose
        sudo rpm -ve # -v is verbose and -e is erase
        ```

1. Asghar Ghori - Exercise 10-1: Configure Access to Pre-Built ISO Repositories

    * Access the repositories that are available on the RHEL 8 image. Create a definition file for the repositories and confirm:
        ```shell
        df -h # after mounting optical drive in VirtualBox
        vi /etc/yum.repos.d/centos.local
        # contents of centos.local
        #####
        #[BaseOS]
        #name=BaseOS
        #baseurl=file:///run/media/$name/BaseOS
        #gpgcheck=0
        #
        #[AppStream]
        #name=AppStream
        #baseurl=file:///run/media/$name/AppStream
        #gpgcheck=0
        #####
        dnf repolist # confirm new repos are added
        ```

1. Asghar Ghori - Exercise 10-2: Manipulate Individual Packages

    * Determine if the *cifs-utils* package is installed and if it is available for installation. Display its information before installing it. Install the package and display its information again. Remove the package along with its dependencies and confirm the removal:
        ```shell
        dnf config-manager --disable AppStream
        dnf config-manager --disable BaseOS
        dnf list installed | greps cifs-utils # confirm not installed
        dnf info cifs-utils # display information
        dnf install cifs-utils -y
        dnf info cifs-utils # Repository now says @System
        dnf remove cifs-utils -y
        ```

1. Asghar Ghori - Exercise 10-3: Manipulate Package Groups

    * Perform management operations on a package group called *system tools*. Determine if this group is already installed and if it is available for installation. List the packages it contains and install it. Remove the group along with its dependencies and confirm the removal:
        ```shell
        dnf group list # shows System Tools as an available group
        dnf group info "System Tools"
        dnf group install "System Tools" -y
        dnf group list "System Tools" # shows installed
        dnf group remove "System Tools" -y
        ```

1. Asghar Ghori - Exercise 10-4: Manipulate Modules

    * Perform management operations on a module called *postgresql*. Determine if this module is already installed and if it is available for installation. Show its information and install the default profile for stream 10. Remove the module profile along with any dependencies and confirm its removal:
        ```shell
        dnf module list "postgresql" # no [i] tag shown so not installed
        dnf module info postgresql:10 # note there are multiple streams
        sudo dnf module install --profile postgresql:10 -y
        dnf module list "postgresql" # [i] tag shown so it's installed
        sudo dnf module remove postgresql:10 -y
        ```

1. Asghar Ghori - Exercise 10-5: Install a Module from an Alternative Stream

    * Downgrade a module to a lower version. Remove the stream *perl* 5.26 and confirm its removal. Manually enable the stream *perl* 5.24 and confirm its new status. Install the new version of the module and display its information:
        ```shell
        dnf module list perl # 5.26 shown as installed
        dnf module remove perl -y
        dnf module reset perl # make no version enabled
        dnf module install perl:5.26/minimal --allowerasing
        dnf module list perl # confirm module installed
        ```

1. Asghar Ghori - Exercise 11-1: Reset the root User Password

    * Terminate the boot process at an early stage to access a debug shell to reset the root password:
        ```shell
        # add rd.break affter "rhgb quiet" to reboot into debug shell
        mount -o remount, rw /sysroot
        chroot /sysroot
        passwd # change password
        touch /.autorelabel
        ```

1. Asghar Ghori - Exercise 11-2: Download and Install a New Kernel

    * Download the latest available kernel packages from the Red Hat Customer Portal and install them:
        ```shell
        uname -r # view kernel version
        rpm -qa | grep "kernel"
        # find versions on access.redhat website, download and move to /tmp
        sudo dnf install /tmp/kernel* -y
        ```

1. Asghar Ghori - Exercise 12-1: Manage Tuning Profiles

    * Install the *tuned* service, start it and enable it for auto-restart upon reboot. Display all available profiles and the current active profile. Switch to one of the available profiles and confirm. Determine the recommended profile for the system and switch to it. Deactive tuning and reactivate it:
        ```shell
        sudo systemctl status tuned-adm # already installed and enabled
        sudo tuned-adm profile # active profile is virtual-guest
        sudo tuned-adm profile desktop # switch to desktop profile
        sudo tuned-adm profile recommend # virtual-guest is recommended
        sudo tuned-adm off # turn off profile
        ```

1. Asghar Ghori - Exercise 13-1: Add Required Storage to server2

    * Add 4x250MB, 1x4GB, and 2x1GB disks:
        ```shell
        # in virtual box add a VDI disk to the SATA controller
        lsblk # added disks shown as sdb, sdc, sdd
        ```

1. Asghar Ghori - Exercise 13-2: Create an MBR Partition

    * Assign partition type "msdos" to `/dev/sdb` for using it as an MBR disk. Create and confirm a 100MB primary partition on the disk:
        ```shell
        parted /dev/sdb print # first line shows unrecognised disk label
        parted /dev/sdb mklabel msdos
        parted /dev/sdb mkpart primary 1m 101m
        parted /dev/sdb print # confirm added partition
        ```

1. Asghar Ghori - Exercise 13-3: Delete an MBR Partition

    * Delete the *sdb1* partition that was created in Exercise 13-2 above:
        ```shell
        parted /dev/sdb rm 1
        parted /dev/sdb print # confirm deletion
        ```

1. Asghar Ghori - Exercise 13-4: Create a GPT Partition

    * Assign partition type "gpt" to `/dev/sdc` for using it as a GPT disk. Create and confirm a 200MB partition on the disk:
        ```shell
        gdisk /dev/sdc
        # enter n for new
        # enter default partition number
        # enter default first sector
        # enter +200MB for last sector
        # enter default file system type
        # enter default hex code
        # enter w to write
        lsblk # can see sdc1 partition with 200M
        ```

1. Asghar Ghori - Exercise 13-5: Delete a GPT Partition

    * Delete the *sdc1* partition that was created in Exercise 13-4 above:
        ```shell
        gdisk /dev/sdc
        # enter d for delete
        # enter w to write
        lsblk # can see no partitions under sdc
        ```

1. Asghar Ghori - Exercise 13-6: Install Software and Activate VDO

    * Install the VDO software packages, start the VDO services, and mark it for autostart on subsequent reboots:
        ```shell
        dnf install vdo kmod-kvdo -y
        systemctl start vdo.service & systemctl enable vdo.service
        ```

1. Asghar Ghori - Exercise 13-7: Create a VDO Volume

    * Create a volume called *vdo-vol1* of logical size 16GB on the `/dev/sdc` disk (the actual size of `/dev/sdc` is 4GB). List the volume and display its status information. Show the activation status of the compression and de-duplication features:
        ```shell
        wipefs -a /dev/sdc # couldn't create without doing this first
        vdo create --name vdo-vol1 --device /dev/sdc --vdoLogicalSize 16G --vdoSlabSize 128
        # VDO instance 0 volume is ready at /dev/mapper/vdo-vol1
        lsblk # confirm vdo-vol1 added below sdc
        vdo list # returns vdo-vol1
        vdo status --name vdo-vol1 # shows status
        vdo status --name vdo-vol1 | grep -i "compression" # enabled
        vdo status --name vdo-vol1 | grep -i "deduplication" # enabled
        ```

1. Asghar Ghori - Exercise 13-8: Delete a VDO Volume

    * Delete the *vdo-vol1* volume that was created in Exercise 13-7 above and confirm the removal:
        ```shell
        vdo remove --name vdo-vol1
        vdo list # confirm removal
        ```

1. Asghar Ghori - Exercise 14-1: Create a Physical Volume and Volume Group

    * Initialise one partition *sdd1* (90MB) and one disk *sdb* (250MB) for use in LVM. Create a volume group called *vgbook* and add both physical volumes to it. Use the PE size of 16MB and list and display the volume group and the physical volumes:
        ```shell
        parted /dev/sdd mklabel msdos
        parted /dev/sdd mkpart primary 1m 91m
        parted /dev/sdd set 1 lvm on
        pvcreate /dev/sdd1 /dev/sdb
        vgcreate -vs 16 vgbook /dev/sdd1 /dev/sdb
        vgs vgbook # list information about vgbook
        vgdisplay -v vbook # list detailed information about vgbook
        pvs # list information about pvs
        ```

1. Asghar Ghori - Exercise 14-2: Create Logical Volumes

    * Create two logical volumes, *lvol0* and *lvbook1*, in the *vgbook* volume group. Use 120MB for *lvol0* and 192MB for *lvbook1*. Display the details of the volume group and the logical volumes:
        ```shell
        lvcreate -vL 120M vgbook
        lvcreate -vL 192M -n lvbook1 vgbook
        lvs # display information
        vgdisplay -v vgbook # display detailed information about volume group
        ```

1. Asghar Ghori - Exercise 14-3: Extend a Volume Group and a Logical Volume

    * Add another partition *sdd2* of size 158MB to *vgbook* to increase the pool of allocatable space. Initialise the new partition prior to adding it to the volume group. Increase the size of *lvbook1* to 336MB. Display the basic information for the physical volumes, volume group, and logical volume:
        ```shell
        parted mkpart /dev/sdd primary 90 250
        parted /dev/sdd set 2 lvm on
        parted /dev/sdd print # confirm new partition added
        vgextend vgbook /dev/sdd2
        pvs # display information
        vgs # display information
        lvextend vgbook/lvbook1 -L +144M
        lvs # display information
        ```

1. Asghar Ghori - Exercise 14-4: Rename, Reduce, Extend, and Remove Logical Volumes

    * Rename *lvol0* to *lvbook2*. Decrease the size of *lvbook2* to 50MB using the *lvreduce* command and then add 32MB with the *lvresize* command. Remove both logical volumes. Display the summary for the volume groups, logical volumes, and physical volumes:
        ```shell
        lvrename vgbook/lvol0 vgbook/lvbook2
        lvreduce vgbook/lvbook2 -L 50M
        lvextend vgbook/lvbook2 -L +32M
        lvremove vgbook/lvbook1
        lvremove vgbook/lvbook2
        pvs # display information
        vgs # display information
        lvs # display information
        ```

1. Asghar Ghori - Exercise 14-5: Reduce and Remove a Volume Group

    * Reduce *vgbook* by removing the *sdd1* and *sdd2* physical volumes from it, then remove the volume group. Confirm the deletion of the volume group and the logical volumes at the end:
        ```shell
        vgreduce vgbook /dev/sdd1 /dev/sdd2
        vgremove vgbook
        vgs # confirm removals
        pvs # can be used to show output of vgreduce
        ```

1. Asghar Ghori - Exercise 14-5: Reduce and Remove a Volume Group

    * Reduce *vgbook* by removing the *sdd1* and *sdd2* physical volumes from it, then remove the volume group. Confirm the deletion of the volume group and the logical volumes at the end:
        ```shell
        vgreduce vgbook /dev/sdd1 /dev/sdd2
        vgremove vgbook
        vgs # confirm removals
        pvs # can be used to show output of vgreduce
        ```

1. Asghar Ghori - Exercise 14-6: Uninitialise Physical Volumes

    * Uninitialise all three physical volumes - *sdd1*, *sdd2*, and *sdb* - by deleting the LVM structural information from them. Use the *pvs* command for confirmation. Remove the partitions from the *sdd* disk and verify that all disks are now in their original raw state:
        ```shell
        pvremove /dev/sdd1 /dev/sdd2 /dev/sdb
        pvs
        parted /dev/sdd
        # enter print to view partitions
        # enter rm 1
        # enter rm 2
        ```

1. Asghar Ghori - Exercise 14-7: Install Software and Activate Stratis

    * Install the Stratis software packages, start the Stratis service, and mark it for autostart on subsequent system reboots:
        ```shell
        dnf install stratis-cli -y
        systemctl start stratisd.service & systemctl enable stratisd.service
        ```

1. Asghar Ghori - Exercise 14-8: Create and Confirm a Pool and File System

    * Create a Stratis pool and a file system in it. Display information about the pool, file system, and device used:
        ```shell
        stratis pool create mypool /dev/sdd
        stratis pool list # confirm stratis pool created
        stratis filesystem create mypool myfs
        stratis filesystem list # confirm filesystem created, get device path
        mkdir /myfs1
        mount /stratis/mypool/myfs /myfs1
        ```

1. Asghar Ghori - Exercise 14-9: Expand and Rename a Pool and File System

    * Expand the Stratis pool *mypool* using the *sdd* disk. Rename the pool and the file system it contains:
        ```shell
        stratis pool add-data mypool /dev/sdd
        stratis pool rename mypool mynewpool
        stratis pool list # confirm changes
        ```

1. Asghar Ghori - Exercise 14-10: Destroy a File System and Pool

    * Destroy the Stratis file system and the pool that was created, expanded, and renamed in the above exercises. Verify the deletion with appropriate commands:
        ```shell
        umount /bookfs1
        stratis filesystem destroy mynewpool myfs
        stratis filesystem list # confirm deletion
        stratis pool destroy mynewpool
        stratis pool list # confirm deletion
        ```

1. Asghar Ghori - Exercise 15-1: Create and Mount Ext4, VFAT, and XFS File Systems in Partitions

    * Create 2x100MB partitions on the `/dev/sdb` disk, initialise them separately with the Ext4 and VFAT file system types, define them for persistence using their UUIDs, create mount points called `/ext4fs` and `/vfatfs1`, attach them to the directory structure, and verify their availability and usage. Use the disk `/dev/sdc` and repeat the above procedure to establish an XFS file system in it and mount it on `/xfsfs1`:
        ```shell
        parted /dev/sdb
        # enter mklabel 
        # enter msdos 
        # enter mkpart 
        # enter primary
        # enter ext4
        # enter start as 0
        # enter end as 100MB
        # enter print to verify
        parted /dev/sdb mkpart primary 101MB 201MB
        # file system entered during partition created is different
        lsblk # verify partitions
        mkfs.ext4 /dev/sdb1
        mkfs.vfat /dev/sdb2
        parted /dev/sdc
        # enter mklabel 
        # enter msdos 
        # enter mkpart
        # enter primary
        # enter xfs
        # enter start as 0
        # enter end as 100MB
        mkfs.xfs /dev/sdc1
        mkdir /ext4fs /vfatfs1 /xfsfs1
        lsblk -f # get UUID for each file system
        vi /etc/fstab
        # add entries using UUIDs with defaults and file system name
        df -hT # view file systems and mount points
        ```

1. Asghar Ghori - Exercise 15-2: Create and Mount XFS File System in VDO Volume

    * Create a VDO volume called *vdo1* of logical size 16GB on the *sdc* disk (actual size 4GB). Initialise the volume with the XFS file system type, define it for persistence using its device files, create a mount point called `/xfsvdo1`, attach it to the directory structure, and verify its availability and usage:
        ```shell
        wipefs -a /dev/sdc
        vdo create --device /dev/sdc --vdoLogicalSize 16G --name vdo1 --vdoSlabSize 128
        vdo list # list the vdo
        lsblk /dev/sdc # show information about disk
        mkdir /xfsvdo1
        vdo status # get vdo path
        mkfs.xfs /dev/mapper/vdo1
        vi /etc/fstab
        # copy example from man vdo create
        mount -a
        df -hT # view file systems and mount points
        ```

1. Asghar Ghori - Exercise 15-3: Create and Mount Ext4 and XFS File Systems in LVM Logical Volumes

    * Create a volume group called *vgfs* comprised of a 160MB physical volume created in a partition on the `/dev/sdd` disk. The PE size for the volume group should be set at 16MB. Create 2 logical volumes called *ext4vol* and *xfsvol* of sizes 80MB each and initialise them with the Ext4 and XFS file system types. Ensure that both file systems are persistently defined using their logical volume device filenames. Create mount points */ext4fs2* and */xfsfs2*, mount the file systems, and verify their availability and usage:
        ```shell
        vgcreate vgfs /dev/sdd --physicalextentsize 16MB
        lvcreate vgfs --name ext4vol -L 80MB
        lvcreate vgfs --name xfsvol -L 80MB
        mkfs.ext4 /dev/vgfs/ext4vol
        mkfs.xfs /dev/vgfs/xfsvol
        blkid # copy UUID for /dev/mapper/vgfs-ext4vol and /dev/mapper/vgfs-xfsvol
        vi /etc/fstab
        # add lines with copied UUID
        mount -a
        df -hT # confirm added
        ```

1. Asghar Ghori - Exercise 15-4: Resize Ext4 and XFS File Systems in LVM Logical Volumes

    * Grow the size of the *vgfs* volume group that was created above by adding the whole *sdc* disk to it. Extend the *ext4vol* logical volume along with the file system it contains by 40MB using 2 separate commands. Extend the *xfsvol* logical volume along with the file system it contains by 40MB using a single command:
        ```shell
        vdo remove --name vdo1 # need to use this disk
        vgextend vgfs /dev/sdc
        lvextend -L +80 /dev/vgfs/ext4vol
        fsadm resize /dev/vgfs/ext4vol
        lvextend -L +80 /dev/vgfs/xfsvol
        fsadm resize /dev/vgfs/xfsvol
        lvresize -r -L +40 /dev/vgfs/xfsvol # -r resizes file system
        lvs # confirm resizing
        ```

1. Asghar Ghori - Exercise 15-5: Create, Mount, and Expand XFS File System in Stratis Volume

    * Create a Stratis pool called *strpool* and a file system *strfs2* by reusing the 1GB *sdc* disk. Display information about the pool, file system, and device used. Expand the pool to include another 1GB disk *sdh* and confirm:
        ```shell
        stratis pool create strpool /dev/sdc
        stratis filesystem create strpool strfs2
        stratis pool list # view created stratis pool
        stratis filesystem list # view created filesystem
        stratis pool add-data strpool /dev/sdd
        stratis blockdev list strpool # list block devices in pool
        mkdir /strfs2
        lsblk /stratis/strpool/strfs2 -o UUID
        vi /etc/fstab
        # add line
        # UUID=2913810d-baed-4544-aced-a6a2c21191fe /strfs2 xfs x-systemd.requires=stratisd.service 0 0
        ```


1. Asghar Ghori - Exercise 15-6: Create and Activate Swap in Partition and Logical Volume

    * Create 1 swap area in a new 40MB partition called *sdc3* using the *mkswap* command. Create another swap area in a 140MB logical volume called *swapvol* in *vgfs*. Add their entries to the `/etc/fstab` file for persistence. Use the UUID and priority 1 for the partition swap and the device file and priority 2 for the logical volume swap. Activate them and use appropriate tools to validate the activation:
        ```shell
        parted /dev/sdc
        # enter mklabel msdos
        # enter mkpart primary 0 40
        parted /dev/sdd
        # enter mklabel msdos
        # enter mkpart primary 0 140
        mkswap -L sdc3 /dev/sdc 40
        vgcreate vgfs /dev/sdd1
        lvcreate vgfs --name swapvol -L 132
        mkswap swapvol /dev/sdd1
        mkswap /dev/vgfs/swapvol
        lsblk -f # get UUID
        vi /etc/fstab
        # add 2 lines, e.g. first line
        # UUID=WzDb5Y-QMtj-fYeo-iW0f-sj8I-ShRu-EWRIcp swap swap pri=2 0 0
        mount -a
        ```

1. Asghar Ghori - Exercise 16-1: Export Share on NFS Server

    * Create a directory called `/common` and export it to *server1* in read/write mode. Ensure that NFS traffic is allowed through the firewall. Confirm the export:
        ```shell
        dnf install nfs-utils -y
        mkdir /common
        firewall-cmd --permanent --add-service=nfs
        firewall-cmd --reload
        systemctl start nfs-server.service & systemctl enable nfs-server.service
        echo "/nfs *(rw)" >> /etc/exports
        exportfs -av
        ```

1. Asghar Ghori - Exercise 16-2: Mount Share on NFS Client

    * Mount the `/common` share exported above. Create a mount point called `/local`, mount the remote share manually, and confirm the mount. Add the remote share to the file system table for persistence. Remount the share and confirm the mount. Create a test file in the mount point and confirm the file creation on the NFS server:
        ```shell
        dnf install cifs-utils -y
        mkdir /local
        chmod 755 local
        mount 10.0.2.15:/common /local
        vi /etc/fstab
        # add line
        # 10.0.2.15:/common /local nfs _netdev 0 0
        mount -a
        touch /local/test # confirm that it appears on server in common
        ```

1. Asghar Ghori - Exercise 16-3: Access NFS Share Using Direct Map

    * Configure a direct map to automount the NFS share `/common` that is available from *server2*. Install the relevant software, create a local mount point `/autodir`, and set up AutoFS maps to support the automatic mounting. Note that `/common` is already mounted on the `/local` mount point on *server1* via *fstab*. Ensure there is no conflict in configuration or functionality between the 2:
        ```shell
        dnf install autofs -y
        mkdir /autodir
        vi /etc/auto.master
        # add line
        #/- /etc/auto.master.d/auto.dir
        vi /etc/auto.master.d/auto.dir
        # add line
        #/autodir 172.25.1.4:/common
        systemctl restart autofs
        ```

1. Asghar Ghori - Exercise 16-4: Access NFS Share Using Indirect Map

    * Configure an indirect map to automount the NFS share `/common` that is available from *server2*. Install the relevant software and set up AutoFS maps to support the automatic mounting. Observe that the specified mount point "autoindir" is created automatically under `/misc`. Note that `/common` is already mounted on the `/local` mount point on *server1* via *fstab*. Ensure there is no conflict in configuration or functionality between the 2:
        ```shell
        dnf install autofs -y
        grep /misc /etc/auto.master # confirm entry is there
        vi /etc/auto.misc
        # add line
        #autoindir 172.25.1.4:/common
        systemctl restart autofs
        ```

1. Asghar Ghori - Exercise 16-5: Automount User Home Directories Using Indirect Map

    * On *server1* (NFS server), create a user account called *user30* with UID 3000. Add the `/home` directory to the list of NFS shares so that it becomes available for remote mount. On *server2* (NFS client), create a user account called *user30* with UID 3000, base directory `/nfshome`, and no user home directory. Create an umbrella mount point called `/nfshome` for mounting the user home directory from the NFS server. Install the relevent software and establish an indirect map to automount the remote home directory of *user30* under `/nfshome`. Observe that the home directory of *user30* is automounted under `/nfshome` when you sign in as *user30*:
        ```shell
        # on server 1 (NFS server)
        useradd -u 3000 user30
        echo password1 | passwd --stdin user30
        vi /etc/exports
        # add line
        #/home *(rw)
        exportfs -avr

        # on server 2 (NFS client)
        dnf install autofs -y        
        useradd user30 -u 3000 -Mb /nfshome
        echo password1 | passwd --stdin user30
        mkdir /nfshome
        vi /etc/auto.master
        # add line
        #/nfshome /etc/auto.master.d/auto.home
        vi /etc/auto.master.d/auto.home
        # add line
        #* -rw &:/home&
        systemctl enable autofs.service & systemctl start autofs.service
        sudo su - user30
        # confirm home directory is mounted
        ```

1. Asghar Ghori - Exercise 17.1: Change System Hostname

    * Change the hostnames of *server1* to *server10.example.com* and *server2* to *server20.example.com* by editing a file and restarting the corresponding service daemon and using a command respectively:
        ```shell
        # on server 1
        vi /etc/hostname
        # change line to server10.example.com
        systemctl restart systemd-hostnamed
        
        # on server 2
        hostnamectl set-hostname server20.example.com
        ```

1. Asghar Ghori - Exercise 17.2: Add Network Devices to server10 and server20

    * Add one network interface to *server10* and one to *server20* using VirtualBox:
        ```shell
        # A NAT Network has already been created and attached to both servers in VirtualBox to allow them to have seperate IP addresses (note that the MAC addressed had to be changed)
        # Add a second Internal Network adapter named intnet to each server
        nmcli conn show # observe enp0s8 added as a connection
        ```

1. Asghar Ghori - Exercise 17.3: Configure New Network Connection Manually

    * Create a connection profile for the new network interface on *server10* using a text editing tool. Assign the IP 172.10.10.110/24 with gateway 172.10.10.1 and set it to autoactivate at system reboots. Deactivate and reactive this interface at the command prompt:
        ```shell
        vi /etc/sysconfig/network-scripts/ifcfg-enp0s8
        # add contents of file
        #TYPE=Ethernet
        #BOOTPROTO=static
        #IPV4_FAILURE_FATAL=no
        #IPV6INIT=no
        #NAME=enp0s8
        #DEVICE=enp0s8
        #ONBOOT=yes
        #IPADDR=172.10.10.110
        #PREFIX=24
        #GATEWAY=172.10.10.1
        ifdown enp0s8
        ifup enp0s8
        ip a # verify activation
        ```

1. Asghar Ghori - Exercise 17.4: Configure New Network Connection Using nmcli

    * Create a connection profile using the *nmcli* command for the new network interface enp0s8 that was added to *server20*. Assign the IP 172.10.10.120/24 with gateway 172.10.10.1, and set it to autoactivate at system reboot. Deactivate and reactivate this interface at the command prompt:
        ```shell
        nmcli dev status # show devices with enp0s8 disconnected
        nmcli con add type Ethernet ifname enp0s8 con-name enp0s8 ip4 172.10.10.120/24 gw4 172.10.10.1
        nmcli conn show # verify connection added
        nmcli con down enp0s8
        nmcli con up enp0s8
        ip a # confirm ip address is as specified
        ```

1. Asghar Ghori - Exercise 17.5: Update Hosts Table and Test Connectivity

    * Update the `/etc/hosts` file on both *server10* and *server20*. Add the IP addresses assigned to both connections and map them to hostnames *server10*, *server10s8*, *server20*, and *server20s8* appropriately. Test connectivity from *server10* to *server20* to and from *server10s8* to *server20s8* using their IP addresses and then their hostnames:
        ```shell
        ## on server20
        vi /etc/hosts
        # add lines
        #172.10.10.120 server20.example.com server20
        #172.10.10.120 server20s8.example.com server20s8
        #192.168.0.110 server10.example.com server10
        #192.168.0.110 server10s8.example.com server10s8

        ## on server10
        vi /etc/hosts
        # add lines
        #172.10.10.120 server20.example.com server20
        #172.10.10.120 server20s8.example.com server20s8
        #192.168.0.110 server10.example.com server10
        #192.168.0.110 server10s8.example.com server10s8
        ping server10 # confirm host name resolves
        ```

1. Asghar Ghori - Exercise 18.1: Configure NTP Client

    * Install the Chrony software package and activate the service without making any changes to the default configuration. Validate the binding and operation:
        ```shell
        dnf install chrony -y
        vi /etc/chrony.conf # view default configuration
        systemctl start chronyd.service & systemctl enable chronyd.service
        chronyc sources # view time sources
        chronyc tracking # view clock performance
        ```

1. Asghar Ghori - Exercise 19.1: Access RHEL System from Another RHEL System

    * Issue the *ssh* command as *user1* on *server10* to log in to *server20*. Run appropriate commands on *server20* for validation. Log off and return to the originating system:
        ```shell
        # on server 10
        ssh user1@server20
        whoami
        pwd
        hostname # check some basic information
        # ctrl + D to logout
        ```

1. Asghar Ghori - Exercise 19.2: Access RHEL System from Windows

    * Use a program called PuTTY to access *server20* using its IP address and as *user1*. Run appropriate commands on *server20* for validation. Log off to terminate the session:
        ```shell
        # as above but using the server20 IP address in PuTTy
        ```

1. Asghar Ghori - Exercise 19.3: Generate, Distribute, and Use SSH Keys

    * Generate a password-less ssh key pair using RSA for *user1* on *server10*. Display the private and public file contents. Distribute the public key to *server20* and attempt to log on to *server20* from *server10*. Show the log file message for the login attempt:
        ```shell
        # on server10
        ssh-keygen
        # press enter to select default file names and no password
        ssh-copy-id server20
        ssh server20 # confirm you can login

        # on server20
        vi /var/log/secure # view login event
        ```

1. Asghar Ghori - Exercise 20.1: Add Services and Ports, and Manage Zones

    * Determine the current active zone. Add and activate a permanent rule to allow HTTP traffic on port 80, and then add a runtime rule for traffic intended for TCP port 443. Add a permanent rule to the *internal* zone for TCP port range 5901 to 5910. Confirm the changes and display the contents of the affected zone files. Switch the default zone to the *internal* zone and activate it:
        ```shell
        # on server10
        firewall-cmd --get-active-zones # returns public with enp0s8 interface
        firewall-cmd --add-service=http --permanent
        firewall-cmd --add-service=https
        firewall-cmd --add-port=80/tcp --permanent
        firewall-cmd --add-port=443/tcp
        firewall-cmd --zone=internal --add-port=5901-5910/tcp --permanent
        firewall-cmd --reload
        firewall-cmd --list-services # confirm result
        firewall-cmd --list-ports # confirm result
        vi /etc/firewalld/zones/public.xml # view configuration
        vi /etc/firewalld/zones/internal.xml # view configuration
        firewall-cmd --set-default-zone=internal
        firewall-cmd --reload
        firewall-cmd --get-active-zones # returns internal with enp0s8 interface
        ```

1. Asghar Ghori - Exercise 20.2: Remove Services and Ports, and Manage Zones

    * Remove the 2 permanent rules added above. Switch back to the *public* zone as the default zone, and confirm the changes:
        ```shell
        firewall-cmd --set-default-zone=public
        firewall-cmd --remove-service=http --permanent
        firewall-cmd --remove-port=80/tcp --permanent
        firewall-cmd --reload
        firewall-cmd --list-services # confirm result
        firewall-cmd --list-ports # confirm result
        ```

1. Asghar Ghori - Exercise 20.3: Test the Effect of Firewall Rule

    * Remove the *sshd* service rule from the runtime configuration on *server10*, and try to access the server from *server20* using the *ssh* command:
        ```shell
        # on server10
        firewall-cmd --remove-service=ssh --permanent
        firewall-cmd --reload
        
        # on server20
        ssh user1@server10
        # no route to host message displayed

        # on server10
        firewall-cmd --add-service=ssh --permanent
        firewall-cmd --reload
        
        # on server20
        ssh user1@server10
        # success
        ```

1. Asghar Ghori - Exercise 21.1: Modify SELinux File Context

    * Create a directory *sedir1* under `/tmp` and a file *sefile1* under *sedir1*. Check the context on the directory and file. Change the SELinux user and type to user_u and public_content_t on both and verify:
        ```shell
        mkdir /tmp/sedir1
        touch /tmp/sedir1/sefile1
        cd /tmp/sedir1
        ll -Z # unconfined_u:object_r:user_tmp_t:s0 shown
        chcon -u user_u -R sedir1
        chcon -t public_content_t -R sedir1
        ```

1. Asghar Ghori - Exercise 21.2: Add and Apply File Context

    * Add the current context on *sedir1* to the SELinux policy database to ensure a relabeling will not reset it to its previous value. Next, you will change the context on the directory to some random values. Restore the default context from the policy database back to the directory recursively:
        ```shell
        semanage fcontext -a -t public_content_t -s user_u '/tmp/sedir1(/.*)?'
        cat /etc/selinux/targeted/contexts/files/file_contexts.local # view recently added policies
        restorecon -Rv sedir1 # any chcon changes are reverted with this
        ```

1. Asghar Ghori - Exercise 21.3: Add and Delete Network Ports

    * Add a non-standard port 8010 to the SELinux policy database for the *httpd* service and confirm the addition. Remove the port from the policy and verify the deletion:
        ```shell
        semanage port -a -t http_port_t -p tcp 8010
        semanage port -l | grep http # list all port settings
        semanage port -d -t http_port_t -p tcp 8010
        semanage port -l | grep http
        ```

1. Asghar Ghori - Exercise 21.4: Copy Files with and without Context

    * Create a file called *sefile2* under `/tmp` and display its context. Copy this file to the `/etc/default` directory, and observe the change in the context. Remove *sefile2* from `/etc/default`, and copy it again to the same destination, ensuring that the target file receives the source file's context:
        ```shell
        cd /tmp
        touch sefile2
        ll -Zrt # sefile2 context is unconfined_u:object_r:user_tmp_t:s0
        cp sefile2 /etc/default
        cd /etc/default
        ll -Zrt # sefile2 context is unconfined_u:object_r:etc_t:s0
        rm /etc/default/sefile2
        cp /tmp/sefile2 /etc/default/sefile2 --preserve=context
        ll -Zrt # sefile2 context is unconfined_u:object_r:user_tmp_t:s0
        ```

1. Asghar Ghori - Exercise 21.5: View and Toggle SELinux Boolean Values

    * Display the current state of the Boolean nfs_export_all_rw. Toggle its value temporarily, and reboot the system. Flip its value persistently after the system has been back up:
        ```shell
        getsebool nfs_export_all_rw # nfs_export_all_rw --> on
        sestatus -b | grep nfs_export_all_rw # also works
        setsebool nfs_export_all_rw_off
        reboot
        setsebool nfs_export_all_rw_off -P
        ```

1. Prince Bajaj - Managing Containers

    * Download the Apache web server container image (httpd 2.4) and inspect the container image. Check the exposed ports in the container image configuration:
        ```shell
        # as root
        usermod user1 -aG wheel
        cat /etc/groups | grep wheel # confirm
        
        # as user1
        podman search httpd # get connection refused
        # this was because your VM was setup as an Internal Network and not a NAT network so it couldn't access the internet
        # see result registry.access.redhat.com/rhscl/httpd-24-rhel7
        skopeo inspect --creds name:password docker://registry.access.redhat.com/rhscl/httpd-24-rhel7
        podman pull registry.access.redhat.com/rhscl/httpd-24-rhel7
        podman inspect registry.access.redhat.com/rhscl/httpd-24-rhel7
        # exposed ports shown as 8080 and 8443
        ```

    * Run the httpd container in the background. Assign the name *myweb* to the container, verify that the container is running, stop the container and verify that it has stopped, and delete the container and the container image:
        ```shell
        podman run --name myweb -d registry.access.redhat.com/rhscl/httpd-24-rhel7
        podman ps # view running containers
        podman stop myweb
        podman ps # view running containers
        podman rm myweb
        podman rmi registry.access.redhat.com/rhscl/httpd-24-rhel7
        ```

    * Pull the Apache web server container image (httpd 2.4) and run the container with the name *webserver*. Configure *webserver* to display content "Welcome to container-based web server". Use port 3333 on the host machine to receive http requests. Start a bash shell in the container to verify the configuration:
        ```shell
        # as root
        dnf install httpd -y
        vi /var/www/html/index.html
        # add row "Welcome to container-based web server"

        # as user1
        podman search httpd
        podman pull registry.access.redhat.com/rhscl/httpd-24-rhel7
        podman inspect registry.access.redhat.com/rhscl/httpd-24-rhel7 # shows 8080 in exposedPorts, and /opt/rh/httpd24/root/var/www is shown as HTTPD_DATA_ORIG_PATH 
        podman run -d=true -p 3333:8080 --name=webserver -v /var/www/html:/opt/rh/httpd24/root/var/www/html registry.access.redhat.com/rhscl/httpd-24-rhel7
        curl http://localhost:3333 # success!
                
        # to go into the container and (for e.g.) check the SELinux context
        podman exec -it webserver /bin/bash
        cd /opt/rh/httpd24/root/var/www/html
        ls -ldZ

        # you can also just go to /var/www/html/index.html in the container and change it there
        ```

    * Configure the system to start the *webserver* container at boot as a systemd service. Start/enable the systemd service to make sure the container will start at book, and reboot the system to verify if the container is running as expected:
        ```shell
        # as root
        podman pull registry.access.redhat.com/rhscl/httpd-24-rhel7
        vi /var/www/html/index
        # add row "Welcome to container-based web server"
        podman run -d=true -p 3333:8080/tcp --name=webserver -v /var/www/html:/opt/rh/httpd24/root/var/www/html registry.access.redhat.com/rhscl/httpd-24-rhel7
        cd /etc/systemd/system
        podman generate systemd webserver >> httpd-container.service
        systemctl daemon-reload
        systemctl enable httpd-container.service --now
        reboot
        systemctl status httpd-container.service
        curl http://localhost:3333 # success

        # this can also be done as a non-root user
        podman pull registry.access.redhat.com/rhscl/httpd-24-rhel7
        sudo vi /var/www/html/index.html
        # add row "Welcome to container-based web server"
        sudo setsebool -P container_manage_cgroup true
        podman run -d=true -p 3333:8080/tcp --name=webserver -v /var/www/html:/opt/rh/httpd24/root/var/www/html registry.access.redhat.com/rhscl/httpd-24-rhel7
        podman generate systemd webserver > /home/jr/.config/systemd/user/httpd-container.service
        cd /home/jr/.config/systemd/user
        sudo semanage fcontext -a -t systemd_unit_file_t httpd-container.service
        sudo restorecon httpd-container.service
        systemctl enable --user httpd-container.service --now
        ```

    * Pull the *mariadb* image to your system and run it publishing the exposed port. Set the root password for the mariadb service as *mysql*. Verify if you can login as root from local host:
        ```shell
        # as user1
        sudo dnf install mysql -y
        podman search mariadb
        podman pull docker.io/library/mariadb
        podman inspect docker.io/library/mariadb # ExposedPorts 3306 
        podman run --name mariadb -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=mysql docker.io/library/mariadb
        podman inspect mariadb # IPAddress is 10.88.0.22
        mysql -h 10.88.0.22 -u root -p
        ```

1. Linux Hint - Bash Script Examples

    * Create a hello world script:
        ```shell
        !#/bin/bash
        echo "Hello World!"
        exit
        ```

    * Create a script that uses a while loop to count to 5:
        ```shell
        !#/bin/bash
        count=0
        while [ $count -le 5 ]
        do
            echo "$count"
            count = $(($count + 1))
        done
        exit
        ```

    * Note the formatting requirements. For example, there can be no space between the equals and the variable names, there must be a space between the "]" and the condition, and there must be 2 sets of round brackets in the variable incrementation.

    * Create a script that uses a for loop to count to 5:
        ```shell
        !#/bin/bash
        count=5
        for ((i=1; i<=$count; i++))
        do
            echo "$i"
        done
        exit
        ```

    * Create a script that uses a for loop to count to 5 printing whether the number is even or odd:
        ```shell
        !#/bin/bash
        count=5
        for ((i=1; i<=$count; i++))
        do
            if [ $(($i%2)) -eq 0 ]
            then
                echo "$i is even"
            else
                echo "$i is odd"
            fi
        done
        exit
        ```

    * Create a script that uses a for loop to count to a user defined number printing whether the number is even or odd:
        ```shell
        !#/bin/bash
        echo "Enter a number: "
        read count
        for ((i=1; i<=$count; i++))
        do
            if [ $(($i%2)) -eq 0 ]
            then
                echo "$i is even"
            else
                echo "$i is odd"
            fi
        done
        exit
        ```

    * Create a script that uses a function to multiply 2 numbers together:
        ```shell
        !#/bin/bash
        Rectangle_Area() {
            area=$(($1 * $2))
            echo "Area is: $area"
        }
        
        Rectangle_Area 10 20
        exit
        ```

    * Create a script that uses the output of another command to make a decision:
        ```shell
        !#/bin/bash
        ping -c 1 $1 > /dev/null 2>&1
        if [ $? -eq 0 ]
        then
            echo "Connectivity to $1 established"
        else
            echo "Connectivity to $1 unavailable"
        fi
        exit
        ```