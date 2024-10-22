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
3. Configure your system to automatically mount the ISO of the installation disk on the directory /repo. Configure your system to remove this loop-mounted ISO as the only repository that is used for  installation. Do not register your system with subscription-manager, and remove all references to external repositories that may already exist.
4. Create a 500-MiB partition on your second hard disk, and format it with the Ext4 file system. Mount it persistently on the directory /mydata, using the label mydata. 
5. Set default values for new users. A user should get a warning three days before expiration of the current password. Also, new passwords should have a maximum lifetime of 120 days.
6. Create users lori and laura and make them members of the secondary group sales. Ensure that user lori uses UID 2000 and user laura uses UID 2001.
7. Create shared group directories /groups/sales and /groups/data, and make sure the groups meet the following requirements:
    1. Members of the group sales have full access to their directory.
    2. Members of the group data have full access to their directory.
    3. Others has no access to any of the directories.
    4. Alex is general manager, so user alex has read access to all files in both directories and has permissions to delete all files that are created in both directories.
8. Create a 1-GiB swap partition and mount it persistently.
9. Find all files that have the SUID permission set, and write the result to the file /root/suidfiles.
10. Create a 1-GiB LVM volume group. In this volume group, create a 512-MiB swap volume and mount it persistently.
11. Add a 10-GiB disk to your virtual machine. On this disk, create a Stratis pool and volume. Use the name stratisvol for the volume, and mount it persistently on the directory /stratis.
12. Install an HTTP web server and configure it to listen on port 8080.
13. Create a configuration that allows user laura to run all administrative commands using sudo.
14. Create a directory with the name /users and ensure it contains the subdirectories linda and anna. Export this directory by using an NFS server.
15. Create users linda and anna and set their home directories to /home/users/linda and /home/users/anna. Make sure that while these users access their home directory, autofs is used
to mount the NFS shares /users/linda and /users/anna from the same server.