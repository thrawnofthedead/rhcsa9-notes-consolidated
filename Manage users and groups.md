### Manage users and groups

1. Create, delete, and modify local user accounts

    * RHEL 8 supports three user account types: root, normal and service. The root user has full access to all services and administrative functions. A normal user can run applications and programs that they are authorised to execute. Service accounts are responsible for taking care of the installed services.

    * The `/etc/passwd` file contains vital user login data.

    * The `/etc/shadow` file is readable only by the root user and contains user authentication information. Each row in the file corresponds to one entry in the passwd file. The password expiry settings are defined in the `/etc/login.defs` file. The `/etc/defaults/useradd` file contains defaults for the *useradd* command.

    * The `/etc/group` file contains the group information. Each row in the file stores one group entry.

    * The `/etc/gshadow` file stores encrypted group passwords. Each row in the file corresponds to one entry in the group file.

    * Due to manual modification, inconsistencies may arise between the above four authentication files. The *pwck* command is used to check for inconsistencies.

    * The *vipw* and *vigr* commands are used to modify the *passwd* and *group* files, respectively. These commands disable write access to these files while the privileged user is making the modifications.

    * To create a user:
        ```shell
        useradd user1
        ```
    
    * To check that the user has been created:
        ```shell
        cat /etc/group | grep user1
        ```

    * To specify the UID and GID at user creation:
        ```shell
        useradd -u 1010 -g 1005 user1
        ```

    * To create a user and add them to a group:
        ```shell
        useradd -G IT user2
        ```

    * Note that *-G* is a secondary group, and *-g* is the primary group. The primary group is the group that the operating system assigns to files to which a user belongs. A secondary group is one or more other groups to which a user also belongs. 

    * To delete a user:
        ```shell
        userdel user1
        ```

    * To modify a user:
        ```shell 
        usermod -l user5 user1 # note that home directory will remain as user1
        ```

    * To add a user but not give access to the shell:
        ```shell 
        useradd -s /sbin/nologin user
        ```

1. Change passwords and adjust password aging for local user accounts

    * To change the password for a user:
        ```shell 
        passwd user1
        ```

    * To step through password aging information the *chage* command can be used without any options.

    * To view user password expiry information:
        ```shell 
        chage -l user1
        ```

    * To set the password expiry for a user 30 days from now:
        ```shell 
        chage -M 30 user1
        ```

    * To set the password expiry date:
        ```shell 
        chage -E 2021-01-01 user1
        ```

    * To set the password to never expire:
        ```shell 
        chage -E -1 user1
        ```

1. Create, delete, and modify local groups and group memberships

    * To create a group:
        ```shell 
        groupadd IT
        ```

    * To create a group with a specific GID:
        ```shell 
        groupadd -g 3032
        ```

    * To delete a group:
        ```shell 
        groupdel IT
        ```

    * To modify the name of a group:
        ```shell 
        groupmod -n IT-Support IT
        ```

    * To modify the GID of a group:
        ```shell 
        groupmod -g 3033 IT-Support
        ```

    * To add a user to a group:
        ```shell 
        groupmod -aG IT-Support user1
        ```

    * To view the members of a group:
        ```shell 
        groupmems -l -g IT-Support
        ```

    * To remove a user from a group:
        ```shell 
        gpasswd -d user1 IT-Support
        ```

1. Configure superuser access

    * To view the sudoers file:
        ```shell 
        visudo /etc/sudoers
        ```

    * Members of the wheel group can use sudo on all commands. To add a user to the wheel group:
        ```shell 
        sudo usermod -aG wheel user1
        ```

    * To allow an individual user sudo access to specific commands:
        ```shell
        visudo /etc/sudoers
        user2 ALL=(root) /bin/ls, /bin/df -h, /bin/date

# Reset Root Password

Things have changed in RHEL 9. rd.break does not work anymore.
These are the steps that need to be taken.

1. Find the line that loads the Linux kernel and add ``init=/bin/bash`` to the end of the line.
2. ``mount -o remount,rw /`` This is necessary because it's mounted as read-only.
3. ``passwd root``
4. ``touch /.autorelabel``
5. ``exec /usr/lib/systemd/systemd/`` To reboot the machine or ``/sbin/reboot -f``

# Users and Groups

## Users 

### Adding users

``useradd john`` to create user john.
Checkout useradd --help to see more available options.

Files in **/etc/default/useradd** apply to useradd only.  
Alternatively, write default settings to **/etc/login.defs**. This is the main settings config file.

Changing this will not affect previously created users, only users that will be created in the future.

Files in **/etc/skel** are copied to the user home directory upon creation. If we want to send a message, scripts, etc. We can use **/etc/skel**.

## Managing users

### Passwords
To view password settings for user John.
``chage -l john``

To set password options for John.
``chage john``

You can also view the password options in **/etc/shadow**. You can see if the user account is locked out. The second field is the password hash. If the password hash starts with **!** The user account is locked out. As you can see, the user John is locked out.

![The shadow file](RHCSA%20Notes%20Consolidated/pictures/shadow.png)

You can also see if the account is locked with ``passwd -S armann`` 

If you want to transfer a password from another server to another one, simply copy the password hash in **/etc/shadow** from the server with the correct password and paste it into field number 2. 

To edit **/etc/passwd** use ``vipw``. Do not edit the file directly.

### User account management

To lock a user account.
``usermod -L john``

To unlock an account.
``usermod -U john``

See previous logged in users.
``last``

See currently logged in users.
``w`` or ``who``

### User file management

**/etc/login.defs**: Used for default settings like UID settings, passwd default settings, and other things.

**/etc/profile**: Used for default settings for all users when starting a login shell.

**/etc/bashrc**: Used to define defaults for all users when starting a subshell.

**~/.profile**: Specific settings for one user applied when starting a login shell.

**~/.bashrc**: Specific settings for one user applied when starting a subshell.

## Groups

To create a new group. ``groupadd groupname``

To see members of a group. ``groupmems -g sales`` or ``lid -g groupname``. For a specific user you can use ``id john`` or ``groups john``. The first group listed is the primary group.

Add John to the group sales. ``usermod -aG sales john``. The new group for the user is applied when they log out and back in. If they don't want to do that and use the new group right away, that's when we use the ``newgrp`` command. Remember to use the ``-a`` option when adding people to groups. If you don't, it will **override** all secondary groups the member is a part of.

Remove user John from the group printers.
``gpasswd -d john printers``

You can use ``newgrp sales`` to change the primary group to sales. This is only a **temporary** primary group change, when you exit, it's back to your original primary group. Remember that the ``newgrp`` command opens a subshell where the user is a member of the group sales.

Use ``vigr`` to change the **/etc/groups** file.  

See all groups `cat /etc/group`

## SUDO 

To have sudo rights the user needs to be a part of the wheel group.

To see current members of the wheel group.

``lid -g wheel``

Let's add John to the wheel group. ``usermod -aG wheel john``

Let's remove John from the wheel group. ``gpasswd -d john wheel``
