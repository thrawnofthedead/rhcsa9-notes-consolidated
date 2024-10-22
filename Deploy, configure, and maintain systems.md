### Deploy, configure, and maintain systems

1. Schedule tasks using at and cron

    * Job scheduling and execution is handled by the *atd* and *crond* daemons. While *atd* manages jobs scheduled to run once in the future, *crond* is responsible for running jobs repetitively at pre-specified times. At start-up, *crond* reads schedules in files located in the `/var/spool/cron` and `/etc/cron.d` directories, and loads them in memory for later execution.

    * There are 4 files that control permissions for setting scheduled jobs. These are *at.allow*, *at.deny*, *cron.allow* and *cron.deny*. These files are in the `/etc` directory. The syntax of the files is identical, with each file taking 1 username per line. If no files exist, then no users are permitted. By default, the *deny* files exist and are empty, and the *allow* files do not exist. This opens up full access to using both tools for all users.

    * All activities involving *atd* and *crond* are logged to the `/var/log/cron` file.

    * The *at* command is used to schedule one-time execution of a program by the *atd* daemon. All submitted jobs are stored in the `/var/spool/at` directory.

    * To schedule a job using *at* the below syntax is used:
        ```shell
        at 11:30pm 6/30/15
        ```

    * The commands to execute are defined in the terminal, press *ctrl+d* when finished. The added job can be viewed with *at* and can be removed with the *-d* option.

    * A shell script can also be provided:
        ```shell
        at -f ~/script1.sh 11:30pm 6/30/15
        ```

    * The `/etc/crontab` file has the following columns:
        * 1: Minutes of hour (0-59), with multiple comma separated values, or * to represent every minute.
        * 2: Hours of day (0-23), with multiple comma separated values, or * to represent every hour.
        * 3: Days of month (1-31), with multiple comma separated values, or * to represent every day.
        * 4: Month of year (1-12, jan-dec), with multiple comma separated values, or * to represent every month.
        * 5: Day of week (0-6, sun-sat), with multiple comma separated values, or * to represent every day.
        * 6: Full path name of the command or script to be executed, along with any arguments.
        
    * Step values can be used with */2 meaning every 2nd minute.
    
    * The *crontab* command can be used to edit the file. Common options are *e* (edit), *l* (view), *r* (remove):
        ```shell
        crontab -e
        ```

1. Start and stop services and configure services to start automatically at boot

    * To check the status of a service:
        ```shell
        systemctl status <service>
        ```

    * To start a service:
        ```shell
        systemctl start <service>
        ```

    * To stop a service:
        ```shell
        systemctl stop <service>
        ```

    * To make a service reload its configuration:
        ```shell
        systemctl reload <service>
        ```

    * To make a service reload its configuration or restart if it can't reload:
        ```shell
        systemctl reload-or-restart <service>
        ```

    * To make a service start on boot:
        ```shell
        systemctl enable <service>
        ```

    * To stop a service starting on boot:
        ```shell
        systemctl disable <service>
        ```

    * To check if a service is enabled:
        ```shell
        systemctl is-enabled <service>
        ```

    * To check if a service has failed:
        ```shell
        systemctl is-failed <service>
        ```

    * To view the configuration file for a service:
        ```shell
        systemctl cat /usr/lib/sysdtemd/system/<service>
        ```

   * To view the dependencies for a service:
        ```shell
        systemctl list-dependencies <service>
        ```

   * To stop a service from being run by anyone but the system and from being started on boot:
        ```shell
        systemctl mask <service>
        ```

   * To remove a mask:
        ```shell
        systemctl unmask <service>
        ```

1. Configure systems to boot into a specific target automatically

   * To get the default target:
        ```shell
        systemctl get-default
        ```

   * To list available targets:
        ```shell
        systemctl list-units --type target --all
        ```

   * To change the default target:
        ```shell
        systemctl set-default <target>
        ```

    * The change will take affect after a reboot.

1. Configure time service clients

    * To print the date:
        ```shell
        date +%d%m%y-%H%M%S
        ```

    * To set the system clock as per the hardware clock:
        ```shell
        hwclock -s
        ```

    * To set the hardware clock as per the system clock:
        ```shell
        hwclock -w
        ```

    * The *timedatectl* command can also be used to view the date and time.

    * To change the date or time:
        ```shell
        timedatectl set-time 2020-03-18
        timedatectl set-time 22:43:00
        ```

    * To view a list of time zones:
        ```shell
        timedatectl list-timezones
        ```

    * To change the time zone:
        ```shell
        timedatectl set-timezone <timezone>
        ```

    * To enable NTP:
        ```shell
        timedatectl set-ntp yes
        ```

    * To start the *chronyd* service:
        ```shell
        systemctl start chronyd
        ```

1. Install and update software packages from Red Hat Network, a remote repository, or from the local file system

    * The .rpm extension is a format for files that are manipulated by the Redhat Package Manager (RPM) package management system. RHEL 8 provides tools for the installation and administration of RPM packages. A package is a group of files organised in a directory structure and metadata that makes up a software application.

    * An RPM package name follows the below format:
        ```shell
        openssl-1.0.1e-34.el7.x86_64.rpm
        # package name = openssl
        # package version = 1.0.1e
        # package release = 34
        # RHEL version = el7
        # processor architecture = x86_64
        # extension = .rpm
        ```

    * The *subscription-manager* command can be used to link a Red Hat subscription to a system.

    * The *dnf* command is the front-end to *rpm* and is the preferred tool for package management. The *yum* command has been superseded by *dnf* in RHEL 8. It requires that the system has access to a software repository. The primary benefit of *dnf* is that it automatically resolves dependencies by downloading and installing any additional required packages.

    * To list enabled and disabled repositories:
        ```shell
        dnf repolist all
        dnf repoinfo
        ```
    
    * To search for a package:
        ```shell
        dnf search <package>
        dnf list <package>
        ```
    
    * To view more information about a particular package:
        ```shell
        dnf info <package>
        ```

    * To install a package:
        ```shell
        dnf install <package>
        ```

    * To remove a package:
        ```shell
        dnf remove <package>
        ```

    * To find a package from a file:
        ```shell
        dnf provides <path>
        ```

    * To install a package locally:
        ```shell
        dnf localinstall <path>
        ```

    * To view available groups:
        ```shell
        dnf groups list
        ```

    * To install a group (e.g. System Tools):
        ```shell
        dnf group "System Tools"
        ```

    * To remove a group (e.g. System Tools):
        ```shell
        dnf group remove "System Tools"
        ```

    * To see the history of installations using *dnf*:
        ```shell
        dnf history list
        ```

    * To undo a particular installation (e.g. ID=22):
        ```shell
        dnf history undo 22
        ```

    * To redo a particular installation (e.g. ID=22):
        ```shell
        dnf history redo 22
        ```

    * To add a repository using the dnf config manager:
        ```shell
        dnf config-manager --add-repo <url>
        ```

    * To enable a repository using the dnf config manager:
        ```shell
        dnf config-manager --enablerepo <repository>
        ```

    * To disable a repository using the dnf config manager:
        ```shell
        dnf config-manager --disablerepo <repository>
        ```

    * To create a repository:
        ```shell
        dnf install createrepo
        mkdir <path>
        createrepo --<name> <path>
        yum-config-manager --add-repo file://<path>
        ```

1. Work with package module streams

    * RHEL 8 introduced the concept of Application Streams. Components made available as Application Streams can be packaged as modules or RPM packages and are delivered through the AppStream repository in RHEL 8. Module streams represent versions of the Application Stream components. Only one module stream can be active at a particular time, but it allows multiple different versions to be available in the same dnf repository.
    
    * To view modules:
        ```shell
        dnf module list
        ```

    * To get information about a module: 
        ```shell
        dnf module info --profile <module-name>
        ```

    * To install a module: 
        ```shell
        dnf module install <module-name>
        ```

    * To remove a module: 
        ```shell
        dnf module remove <module-name>
        ```

    * To reset a module after removing it: 
        ```shell
        dnf module reset <module-name>
        ```

    * To be specific about the module installation:
        ```shell
        dnf module install <module-name>:<version>/<profile>
        ```

    * To check the version of a module:
        ```shell
        <module-name> -v
        ```

1. Modify the system bootloader

    * The GRUB2 configuration can be edited directly on the boot screen. The configuration can also be edited using the command line.

    * To view the grub2 commands: 
        ```shell
        grub2
        ```

    * To make a change to the configuration: 
        ```shell
        vi /etc/default/grub
        # Change a value
        grub2-mkconfig -o /boot/grub2/grub.cfg
        # View changes
        vi /boot/grub2/grub.cfg
        ```

# Crontab

![Crontab](RHCSA%20Notes%20Consolidated/pictures/crontab.png)

``crontab -e``
Edits the current crontab using the editor specified by the VISUAL or EDITOR environment variables.
## Anacron

## At
The **atd** service needs to be running to run one-time only jobs.

``at 11:25AM`` to schedule job.
You enter an interactive shell and you use Ctrl-D to close it.

Use ``at -l`` to list all pending jobs.
To see the contents of a scheduled job. ``at -c [job_number]``
Use ``atrm`` to remove jobs from the list.

# Software management

## Common DNF commands

``dnf repolist`` \
``dnf provides htop`` or ``dnf provides */Containerfile`` \
``dnf search htop`` \
``dnf search all htop`` \
``dnf update`` The same as ``dnf upgrade``. The "update" is an alias for "upgrade". \
``dnf group install`` Only mandatory and default packages are installed, to see optional packages use ``dnf group info`` and to install with all optional packages do ``dnf group install --with-optional`` \
``dnf group list`` See group packages that you can install. \
``dnf group list hidden`` Some groups are normally only installed through environment groups and not separately, and for that reason don't show when using ``dnf group list``
``dnf list installed`` List installed software on the machine.

### History
``dnf history``
``dnf history info 10`` 

### Rollback and undo
``dnf history undo 10``
``sudo dnf history rollback 10`` Let's say you want to undo everything that was installed after number 10. This command seen below would remove mutt, emacs and powertop.

![dnf rollback](RHCSA%20Notes%20Consolidated/pictures/rollback.png)

To reinstall something that was removed. \
``dnf history redo 15``

## Repository management during exam

During the exam your virtual machine **will not have access to the internet**. Hence, we cannot use subscription-manager and associated repos. **No repositories will be available by default**. This means we cannot install any packages by default.

Red Hat will tell you that a repository is available at a certain location, and you will have to configure the repository for that manually.

You need to be capable of configuring repository access, or you will **fail the exam**.

### Use subscription manager repositories
This is just and FYI. **You will not** have access to these repositories during the exam!.
To access repositories that are offered through subscription manager, use ``dnf config-manager --enable name-of-the-repository`` to add repository access.

### Create a repository locally
To enable third party repositories, create a repo files in "/etc/yum.repos.d/". 

Let's create a local repository from the RHEL 9 ISO file.
I mount the RHEL 9 ISO in the virtual cdrom drive.
For me, it's mounted on "/run/media/armann/RHEL-9-0-0-BaseOS-x86_64".

If you need to mount the cdrom manually. ``mount /dev/sr0 /mnt``

Let's copy the iso file to our computer. Make sure you have around 9GB available            
on the root of your hard disk. ``dd if=/dev/sr0 of=/rhel9.iso bs=1M status=progress``

Let's edit "/etc/fstab" so it's mounted automatically for us. You can see the last line, that's how we mount the rhel9.iso automatically after boot.

![repository](RHCSA%20Notes%20Consolidated/pictures/repo.png)

Let's mount it.  ``mount -a``

Now we need to manually create the repository file. \
Go under "/etc/yum.repos.d/". \
Create a file, ``vim baseos.repo``

Let's add the following lines into baseos.repo.

[baseos] \
name=baseos \
baseurl=file:///opt/iso/BaseOS \
gpgcheck=0

**Create another repo file.** \
Call it appstream.repo

[appstream] \
name=appstream \
baseurl=file:///opt/iso/AppStream \
gpgcheck=0 

Now we can check to see if our repository file is okay. \
``dnf repolist`` \
We should see baseos and appstream and no errors.

## DNF Modules

**BaseOS repo** is for packages that don't change during the lifecycle of the OS.
The **AppStream repo** is for packages that do change major versions during
the lifecycle period of the Os.

## Subscription

To see all the software you are entitled to use with the subscription attached to the machine.

``rct cat-cert /etc/pki/entitlement/5715597599610761455.pem``
# Systemd
### Important items

`/usr/lib/systemd/system` - systemd unit files that are distributed with the RPM package manager are found here
`/run/systemd/system` - systemd unit files that are created at runtime -takes priority over files in /usr/lib/systemd/system
`/etc/systemd/system` - systemd unit files created by `systemctl enable {servicename}`
`/etc/systemd/system.com` - systemd Configuration file
## Common commands

``systemctl status``

* List currently running services
``systemctl list-units -t service``

* List **all** services 
``systemctl list-units -t service --all``

* List dependencies (A good overview of what is "on" and what is "off". )
``systemctl list-dependencies``

* List dependencies that start **after** the service starts
`systemctl list-dependencies --after`

* List dependencies that start **before** the service starts (that this service relies upon)
`systemctl list-dependencies --before`

* To enable the service to start at boot
``systemctl enable name.service``

* To manually start a service
``systemctl start name.service``

* To restart a service
``systemctl restart name.service``

- To restart a service only if it's already running
`systemctl try-restart name.service`

- To reload a service (attempts to keep the service active while reloading config changes)
`systemctl reload name.service`

- To mask a service (prevents a service from being able to be started until unmasked)
`systemctl mask name.service`

To unmask a service
`systemctl unmask name.service`

Reload config
``systemctl reload name.service``
`reload` will reload a specific service. That means that the systemd will send a SIGHUP signal to a service, and that signal will tell the service to reload its configuration files, which has nothing to do with systemd config files.

Reload custom service files
``systemctl daemon-reload``
Location of custom systemd units is "/etc/systemd/system". This command reloads the service files in that directory.

Check the configuration of a unit file
``systemctl cat sshd.service``

Edit unit file
``systemctl edit sshd.service``
By default it uses Nano. To change that to Vim.
``export SYSTEMD_EDITOR=/usr/bin/vim``
This creates a drop-in-file in "/etc/systemd/system/"
If it does not create the drop-in-file automatically, do ``systemctl daemon-reload``

To see tunables for a service do ``systemctl show httpd.service``

/user/lib/systemd/system/ is for configuration files provided by packages.
Do not edit those directly since they can be overwritten by newer packages.

/etc/tuned/ and /usr/lib/tuned/ need an explanation for it here.

Mask
To prevent certain units from starting up, use ``systemctl mask``. It links a unit to the /dev/null device, which ensures it cannot be started. For instance ``systemctl mask nginx``

``systemctl unmask`` removes the unit mask.

## Systemd Journal (journalctl)

Show all boots that have been logged. Needs to have persistent journalling.
``journalctl --list-boots``

``journalctl -xrb``
-x = Augment log lines with explanation texts from the message catalog. This will add explanatory help texts to log messages
-r = Reverse output so that the newest entries are displayed first.
-b = Show messages from a specific boot. This will add a match for "_BOOT_ID=".
The argument may be empty, in which case logs for the current boot will be shown.

### How to make the journal persistent

You could use rsyslog to make the journal persistent.

"/etc/systemd/journal.conf"
The setting "Storage=auto" ensures that persistent storage is happening automatically **after manually creating the directory** "/var/log/journal"

Then we need to restart the journal service.
``systemctl restart systemd-journal-flush.service``

### Common commands

View only messages with a priority error and higher.
``journalctl -p err``

View the last 10 lines, and adds new messages when they are added.
``journalctl -f``

Show messages for the sshd service only.
``journalctl -u sshd.service``

### See space used by Journalctl

See current settings for growth and what it's currently using.
``journalctl | grep -E 'Runtime Journal|System Journal'``


## Systemd Timers (Scheduling)

When using systemd timers, the timer should be started, and **not** the service unit.

``systemctl list-units -t timer``
``systemctl list-unit-files dnf-makecache*``
``systemctl status dnf-makecache.timer``
Checkout the "Trigger and Triggers".

To schedule and activate a timer you use the "OnCalendar" option.

``OnCalendar=*:00/20`` runs every 20 minutes.

You can use "OnUnitActiveSec" to start the unit at a specific time after the unit was last activated.

You can use "OnBootSec" or "OnStartupSec" to start the unit a specific time after booting.

## Working with Tuned

Install tuned. ``dnf install tuned``

To see available commands, type this in and press double tab. ``tuned-adm`` 

To see available profiles. ``tune-adm list``

Config files for Tuned are located in "/usr/lib/tuned/".
# General information

The time zone configured on the server is found in /etc/localtime. This is a symbolic link that points to one of the time zones files in /usr/share/zoneinfo.

# Configure time service clients

``hwclock`` sets the hardware time.
If the hardware clock is not correct but the system time is you can sync the hardware time to the 
system time with ``hwclock --systohc``

Use ``date`` so show current date and time.

Use ``timedatectl`` to manage time and time zone configuration.

``timedatectl status`` show all time properties in use.
``timedatectl list-timezones`` show all available timezones.
``timedatectl set-timezone Europe/Rome``
``timedatectl set-time`` \
``timedatectl set-timezone`` \
``timedatectl set-ntp`` enables or disables NTP sync.

An NTP service must be configured. The default for RHEL is Chrony.
If your server is running **systemd-timesyncd.service** you must disable that service before enabling Chrony.

## Chronyd
Chronyd is the default RHEL 9 NTP service.
Use "/etc/chrony.conf" to change sync parameters.

Use iburst to permit fast synchronization.

After changing the conf file restart the chronyd service.

``chronyc sources -v`` to see the servers you are synchronizing with.
