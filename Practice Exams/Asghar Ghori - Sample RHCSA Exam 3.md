1. Asghar Ghori - Sample RHCSA Exam 3
    
    - Build 2 virtual machines with RHEL 8 Server for GUI. Add a 10GB disk for the OS and use the default storage partitioning. Add 1 4GB disk to VM1 and 2 1GB disks to VM2. Assign a network interface, but do not configure the hostname and network connection.
        
    - The VirtualBox Network CIDR for the NAT network is 192.168.0.0/24.
        
    - On VM1, set the system hostname to rhcsa3.example.com and alias rhcsa3 using the hostnamectl command. Make sure that the new hostname is reflected in the command prompt:
        
        ```shell
        hostnamectl set-hostname rhcsa3.example.com
        ```
        
    - On rhcsa3, configure a network connection on the primary network device with IP address 192.168.0.243/24, gateway 192.168.0.1, and nameserver 192.168.0.1 using the nmcli command:
        
        ```shell
        nmcli con add type ethernet ifname enp0s3 con-name mycon ip4 192.168.0.243/24 gw4 192.168.0.1 ipv4.dns 192.168.0.1
        ```
        
    - On VM2, set the system hostname to rhcsa4.example.com and alias rhcsa4 using a manual method (modify file by hand). Make sure that the new hostname is reflected in the command prompt:
        
        ```shell
        vi /etc/hostname
        # change to rhcsa4.example.com
        ```
        
    - On rhcsa4, configure a network connection on the primary network device with IP address 192.168.0.244/24, gateway 192.168.0.1, and nameserver 192.168.0.1 using a manual method (create/modify files by hand):
        
        ```shell
        vi /etc/sysconfig/network-scripts/ifcfg-enp0s3
        #TYPE=Ethernet
        #BOOTPROTO=static
        #DEFROUTE=yes
        #IPV4_FAILURE_FATAL=no
        #IPV4INIT=no
        #NAME=mycon
        #DEVICE=enp0s3
        #ONBOOT=yes
        #IPADDR=192.168.0.243
        #PREFIX=24
        #GATEWAY=192.168.0.1
        #DNS1=192.168.0.1
        ifup enp0s3
        nmcli con edit enp0s3 # play around with print ipv4 etc. to confirm settings
        ```
        
    - Run "ping -c2 rhcsa4" on rhcsa3. Run "ping -c2 rhcsa3" on rhcsa4. You should see 0% loss in both outputs:
        
        ```shell
        # on rhcsa3
        vi /etc/hosts
        # add line 192.168.0.244 rhcsa4
        ping rhcsa3 # confirm
        
        # on rhcsa4
        vi /etc/hosts
        # add line 192.168.0.243 rhcsa3
        ping rhcsa4 # confirm
        ```
        
    - On rhcsa3 and rhcsa4, attach the RHEL 8 ISO image to the VM and mount it persistently to `/mnt/cdrom`. Define access to both repositories and confirm:
        
        ```shell
        # attach disks in VirtualBox
        # on rhcsa3 and rhcsa4
        mkdir /mnt/cdrom
        mount /dev/sr0 /mnt/cdrom
        blkid # get UUID
        vi /etc/fstab
        # add line with UUID /mnt/cdrom iso9660 defaults 0 0
        mount -a # confirm
        vi /etc/yum.repos.d/image.repo
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
        
    - On rhcsa3, add HTTP port 8300/tcp to the SELinux policy database:
        
        ```shell
        semange port -l | grep http # 8300 not in list for http_port_t
        semanage port -a -t http_port_t -p tcp 8300
        ```
        
    - On rhcsa3, create VDO volume vdo1 on the 4GB disk with logical size 16GB and mounted with Ext4 structures on `/mnt/vdo1`:
        
        ```shell
        TBC
        ```
        
    - Configure NFS service on rhcsa3 and share `/rh_share3` with rhcsa4. Configure AutoFS direct map on rhcsa4 to mount `/rh_share3` on `/mnt/rh_share4`. User user80 (create on both systems) should be able to create files under the share on the NFS server and under the mount point on the NFS client:
        
        ```shell
        # on rhcsa3
        mkdir /rh_share3
        chmod 777 rh_share3
        useradd user80
        passwd user80
        # enter Temp1234
        dnf install cifs-utils -y
        systemctl enable nfs-server.service --now
        firewall-cmd --add-service=nfs --permanent
        firewall-cmd --reload
        vi /etc/exports
        # add line rh_share3 rhcsa4(rw)
        exportfs -av
        
        # on rhcsa4
        useradd user80
        passwd user80
        # enter Temp1234
        mkdir /mnt/rh_share4
        chmod 777 rh_share4
        # mount rhcsa3:/rh_share3 /mnt/nfs
        # mount | grep nfs # get details for /etc/fstab
        # vi /etc/fstab
        # add line rhcsa3:/rh_share3 /mnt/rh_share4 nfs4 _netdev 0 0
        # above not required with AutoFS
        dnf install autofs -y
        vi /etc/auto.master
        # add line /mnt/rh_rhcsa3 /etc/auto.master.d/auto.home
        vi /etc/auto.master.d/auto.home
        # add line * -rw rhcsa3:/rh_share3
        ```
        
    - Configure NFS service on rhcsa4 and share the home directory for user user60 (create on both systems) with rhcsa3. Configure AutoFS indirect map on rhcsa3 to automatically mount the home directory under `/nfsdir` when user60 logs on to rhcsa3:
        
        ```shell
        # on rhcsa3
        useradd user60
        passwd user60
        # enter Temp1234
        dnf install autofs -y
        mkdir /nfsdir
        vi /etc/auto.master
        # add line for /nfsdir /etc/auto.master.d/auto.home
        vi /etc/auto.master.d/auto.home
        # add line for * -rw rhcsa4:/home/user60
        systemctl enable autofs.service --now
        
        # on rhcsa4
        useradd user60
        passwd user60
        # enter Temp1234
        vi /etc/exports
        # add line for /home rhcsa3(rw)
        exportfs -va    
        ```
        
    - On rhcsa4, create Stratis pool pool1 and volume str1 on a 1GB disk, and mount it to `/mnt/str1`:
        
        ```shell
        dnf provides stratis
        dnf install stratis-cli -y
        systemctl enable stratisd.service --now
        stratis pool create pool1 /dev/sdc
        stratis filesystem create pool1 vol1
        mkdir /mnt/str1
        mount /stratis/pool1/vol1 /mnt/str1
        blkid # get information for /etc/fstab
        vi /etc/fstab
        # add line for UUID /mnt/str1 xfs defaults 0 0    
        ```
        
    - On rhcsa4, expand Stratis pool pool1 using the other 1GB disk. Confirm that `/mnt/str1` sees the storage expansion:
        
        ```shell
        stratis pool add-data pool1 /dev/sdb
        stratis blockdev # extra disk visible
        ```
        
    - On rhcsa3, create a group called group30 with GID 3000, and add user60 and user80 to this group. Create a directory called `/sdata`, enable setgid bit on it, and add write permission bit for the group. Set ownership and owning group to root and group30. Create a file called file1 under `/sdata` as user user60 and modify the file as user80 successfully:
        
        ```shell
        TBC
        ```
        
    - On rhcsa3, create directory `/dir1` with full permissions for everyone. Disallow non-owners to remove files. Test by creating file `/tmp/dir1/stkfile1` as user60 and removing it as user80:
        
        ```shell
        TBC
        ```
        
    - On rhcsa3, search for all manual pages for the description containing the keyword "password" and redirect the output to file `/tmp/man.out`:
        
        ```shell
        man -k password >> /tmp.man.out
        # or potentially man -wK "password" if relying on the description is not enough
        ```
        
    - On rhcsa3, create file lnfile1 under `/tmp` and create one hard link `/tmp/lnfile2` and one soft link `/boot/file1`. Edit lnfile1 using the links and confirm:
        
        ```shell
        cd /tmp
        touch lnfile1
        ln lnfile1 lnfile2
        ln -s /boot/file1 lnfile1
        ```
        
    - On rhcsa3, install module postgresql version 9.6:
        
        ```shell
        dnf module list postgresql # stream 10 shown as default
        dnf module install postgresql:9.6
        dnf module list # stream 9.6 shown as installed
        ```
        
    - On rhcsa3, add the http service to the "external" firewalld zone persistently:
        
        ```shell
        firewall-cmd --zone=external --add-service=http --permanent
        ```
        
    - On rhcsa3, set SELinux type shadow_t on a new file testfile1 in `/usr` and ensure that the context is not affected by a SELinux relabelling:
        
        ```shell
        cd /usr
        touch /usr/testfile1
        ll -Zrt # type shown as unconfined_u:object_r:usr_t:s0
        semange fcontext -a -t /usr/testfile1
        restorecon -R -v /usr/testfile1
        ```
        
    - Configure password-less ssh access for user60 from rhcsa3 to rhcsa4:
        
        ```shell
        sudo su - user60
        ssh-keygen # do not provide a password
        ssh-copy-id rhcsa4 # enter user60 pasword on rhcsa4
        ```