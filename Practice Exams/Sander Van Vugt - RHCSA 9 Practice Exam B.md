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
    3. One additional 20-GB disk that does not have partitions installed
    4. Server with GUI installation pattern
2. Create user student with password password, and user root with password password.
3. Configure your system to automatically mount the ISO of the installation disk on the directory / repo. Configure your system to remove this loop-mounted ISO as the only repository that is used for installation. Do not register your system with subscription-manager, and remove all references to external repositories that may already exist.
4. Create a 1-GB partition on /dev/sdb. Format it with the vfat file system. Mount it persistently on the directory /mydata, using the label mylabel.
5. Set default values for new users. Ensure that an empty file with the name NEWFILE is copied to the home directory of each new user that is created.
6. Create users laura and linda and make them members of the group livingopensource as a secondary group membership. Also, create users lisa and lori and make them members of the group operations as a secondary group.
7. Create shared group directories /groups/livingopensource and /groups/operations and make sure these groups meet the following requirements:
    1. Members of the group livingopensource have full access to their directory.
    2. Members of the group operations have full access to their directory.
    3. Users should be allowed to delete only their own files.
    4. Others should have no access to any of the directories.
    8. Create a 2-GiB swap partition and mount it persistently.
    9. Resize the LVM logical volume that contains the root file system and add 1 GiB. Perform all tasks necessary to do so.
    10. Find all files that are owned by user linda and copy them to the file /tmp/lindafiles/.
    11. Create user vicky with the custom UID 2008.
    12. Install a web server and ensure that it is started automatically.
    13. Configure a container that runs the docker.io/library/mysql:latest image and ensure it meets the following conditions
        1. It runs as a rootless container in the user linda account.
        2. It is configured to use the mysql root password password.
        3. It bind mounts the host directory /home/student/mysql to the container directory /var/lib/mysql. 
        4. It automatically starts through a systemd job, where it is not needed for user linda to log in.