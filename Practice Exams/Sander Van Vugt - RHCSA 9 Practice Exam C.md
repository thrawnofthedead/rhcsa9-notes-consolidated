# RHCSA Practice Exam B
## General Notes
Here are some tips to ensure your exam starts with a clean
environment:
* You do not need external servers or resources.
* Do not register or connect to external repositories.
* Install a new VM according to the instructions in each practice exam.
* No sample solutions are provided for these practice exams.
* On the real exam, you need to be able to verify the solutions for yourself as well.
* You should be able to complete each exam within two hours.
* After applying these tips, you’re ready to get started. Good luck!

## Exam 
1. Install a RHEL 9 virtual machine that meets the following requirements:
    1. 2 GB of RAM
    2. 20 GB of disk space using default partitioning
    3. One additional 20-GB disk that does not have any partitions installed
    4. Server with GUI installation pattern
2. Create user student with password password, and user root with password password.
3. Configure your system to automatically mount the ISO of the installation disk on the directory /repo. Configure your system to remove this loop-mounted ISO as the only repository that is used for installation. Do not register your system with subscription-manager, and remove all references to external repositories that may already exist.
4. Reboot your server. Assume that you don’t know the root password, and use the appropriate mode to enter a root shell that doesn’t require a password. Set the root password to mypassword.
5. Set default values for new users. Make sure that any new user password has a length of at least six characters and must be used for at least three days before it can be reset.
6. Create users linda and anna and make them members of the group sales as a secondary group membership. Also, create users serene and alex and make them members of the group account as a secondary group.
7. Configure an SSH server that meets the following requirements:
    1. User root is allowed to connect through SSH.
    2. The server offers services on port 2022.
8. Create shared group directories /groups/sales and /groups/account, and make sure these groups meet the following requirements:
1. Members of the group sales have full access to their directory.
2. Members of the group account have full access to their directory.
3. Users have permissions to delete only their own files, but Alex is the general manager, so user alex has access to delete all users’ files.
9. Create a 4-GiB volume group, using a physical extent size of 2 MiB. In this volume group, create a 1-GiB logical volume with the name myfiles, format it with the Ext3 file system, and mount it persistently on /myfiles.
10. Create a group sysadmins. Make users linda and anna members of this group and ensure that all members of this group can run all administrative commands using sudo. 
11. Optimize your server with the appropriate profile that optimizes throughput.
12. Add a new disk to your virtual machine with a size of 10 GiB. On this disk, create a LVM logical volume with a size of 5 GiB, configure it as swap, and mount it persistently.
13. Create a directory /users/ and in this directory create the directories user1 through user5 using one command.
14. Configure a web server to use the nondefault document root /webfiles. In this directory, create a file index.html that has the contents hello world and then test that it works.
15. Configure your system to automatically start a mariadb container. This container should expose its services at port 3306 and use the directory /var/mariadb-container on the host for persistent storage of files it writes to the /var directory.
16. Configure your system such that the container created in step 15 is automatically started as a Systemd user container.