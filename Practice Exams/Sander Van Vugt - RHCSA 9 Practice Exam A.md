# RHCSA Practice Exam A
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
1. Install a RHEL 9 virtual machine that meets the following
requirements:
    1. 2 GB of RAM
    2. 20 GB of disk space using default partitioning
    3. One additional 20-GB disk that does not have any partitions installed
    4. Server with GUI installation pattern
2. Create user student with password password, and user root with password password.
3. Configure your system to automatically mount the ISO of the installation disk on the directory /repo. Configure your system to remove this loop-mounted ISO as the only repository that is used for installation. Do not register your system with subscription-manager, and remove all references to external repositories that may already exist.
4. Reboot your server. Assume that you don’t know the root password, and use the appropriate mode to enter a root shell that doesn’t require a password. Set the root password to mypassword.
5. Set default values for new users. Set the default password validity to 90 days, and set the first UID that is used for new users to 2000.
6. Create users edwin and santos and make them members of the group livingopensource as a secondary group
membership. Also, create users serene and alex and make them members of the group operations as a secondary
group. Ensure that user santos has UID 1234 and cannot start an interactive shell.
7. Create shared group directories /groups/livingopensource and /groups/operations, and make sure the groups meet the following requirements:
    1. Members of the group livingopensource have full access to their directory.
    2. Members of the group operations have full access to their directory.
    3. New files that are created in the group directory are group owned by the group owner of the parent   directory.
    4. Others have no access to the group directories.
 8. Create a 2-GiB volume group with the name myvg, using 8-MiB physical extents. In this volume group,create   a 500-MiB logical volume with the name mydata, and mount it persistently on the directory mydata.
 9. Find all files that are owned by user edwin and copy them to the directory/rootedwinfiles.
 10. Schedule a task that runs the command touch /etc/motd every day from Monday through Friday at 2 a.m.
 11. Add a new 10-GiB virtual disk to your virtual machine. On this disk, add a Stratis volume and mountit  persistently.
 12. Create user bob and set this user’s shell so that this user can only change the password and cannotdo  anything else.
 13. Install the vsftpd service and ensure that it is started automatically at reboot.
 14. Create a container that runs an HTTP server. Ensure that it mounts the host directory /httproot onthe  directory /var/www/html.
 15. Configure this container such that it is automatically started on system boot as a system userservice.
 16. Create a directory with the name /users and ensure it contains the subdirectories linda and anna.Export    this directory by using an NFS server.
 17. Create users linda and anna and set their home directories to /home/users/linda and /home/users/anna. Make sure that while these users access their home directory, autofs is used to mount the NFS shares /users/linda and /users/anna from the same server.