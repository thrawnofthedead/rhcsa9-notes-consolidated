### Operate running systems

1. Boot, reboot, and shut down a system normally

    * The RHEL boot process occurs when the system is powered up or reset and lasts until all enabled services are started and a login prompt appears at the screen. The login process consists of 4 steps:

        * The firmware is the Basic Input Output System (BIOS) or Unified Extensible Firmware Interface (UEFI) code that is stored in flash memory on the motherboard. The first thing it does is run the power-on-self-test (POST) to initialise the system hardware components. It also installs appropriate drivers for the video hardware and displays system messages to the screen. It scans the available storage devices to locate a boot device (GRUB2 on RHEL), and then loads it into memory and passes control to it.

        * The boot loader presents a menu with a list of bootable kernels available on the system. After a pre-defined amount of time it boots the default kernel. GRUB2 searches for the kernel in the `/boot` file system. It then extracts the kernel code into memory and loads it based on the configuration in `/boot/grub2/grub.cfg`. Note that for UEFI systems, GRUB2 looks in `/boot/efi` instead and loads based on configuration in `/boot/efi/EFI/redhat/grub.efi`. Once the kernel is loaded, GRUB2 passes control to it.

        * The kernel loads the initial RAM disk (initrd) image from the `/boot` file system. This acts as a temporary file system. The kernel then loads necessary modules from initrd to allow access to the physical disks and the partitions and file systems within. It also loads any drivers required to support the boot process. Later, the kernel unmounts initrd and mounts the actual root file system.

        * The kernel continues the boot process. *systemd* is the default system initialisation scheme. It starts all enabled user space system and network services.

    * The *shutdown* command is used to halt, power off, or reboot the system gracefully. This command broadcasts a warning message to all logged-in users, disables further user logins, waits for the specified time, and then stops the service and shuts to the system down to the specified target state. 
    
    * To shut down the system now:
        ```shell
        shutdown -P now
        ```

    * To halt the system now:
        ```shell
        shutdown -H now
        ```

    * To reboot the system now:
        ```shell
        shutdown -r now
        ```

    * To shut down the system after 5 minutes:
        ```shell
        shutdown -P 5
        ```

1. Boot systems into different targets manually

    * *systemd* is the default system initialisation mechanism in RHEL 8. It is the first process that starts at boot and it is the last process that terminates at shutdown.

    * *Units* are systemd objects that are used for organising boot and maintenance tasks, such as hardware initialisation, socket creation, file system mounts, and service start-ups. Unit configuration is stored in their respective configuration files, which are auto generated from other configurations, created dynamically from the system state, produced at runtime, or user developed. Units are in one of several operational states, including active, inactive, in the process of being activated or deactivated, and failed. Units can be enabled or disabled.

    * Units have a name and a type, which are encoded in files of the form unitname.type. Units can be viewed using the *systemctl* command. A target is a logical collection of units. They are a special systemd unit type with the .target file extension.

    * *systemctl* is the primary command for interaction with systemd. 

    * To boot into a custom target the *e* key can be pressed at the GRUB2 menu, and the desired target specified using systemd.unit. After editing press *ctrl+x* to boot into the target state. To boot into the emergency target: 
        ```shell
        systemd.unit=emergency.target
        ```

    * To boot into the rescue target:
        ```shell
        systemd.unit=rescue.target
        ```

    * Run *systemctl reboot* after you are done to reboot the system.

1. Interrupt the boot process in order to gain access to a system

    * Press *e* at the GRUB2 menu and add "rd.break" in place of "ro crash". This boot option tells the boot sequence to stop while the system is still using initramfs so that we can access the emergency shell.
    
    * Press *ctrl+x* to reboot.
    
    * Run the following command to remount the `/sysroot` directory with rw privileges:
        ```shell
        mount -o remount,rw /sysroot
        ```
    *  Run the following command to change the root directory to `/sysroot`:
        ```shell
        chroot /sysroot
        ```
    *  Run *passwd* command to change the root password.
    
    *  Run the following commands to create an empty, hidden file to instruct the system to perform SELinux relabelling after the next boot:
        ```shell
        touch /.autorelabel
        exit
        exit
        ```

1. Identify CPU/memory intensive processes and kill processes

    * A process is a unit for provisioning system resources. A process is created in memory in its own address space when a program, application, or command is initiated. Processes are organised in a hierarchical fashion. Each process has a parent process that spawns it and may have one or many child processes. Each process is assigned a unique identification number, known as the Process Identifier (PID). When a process completes its lifecycle or is terminated, this event is reported back to its parent process, and all the resources provisioned to it are then freed and the PID is removed. Processes spawned at system boot are called daemons. Many of these sit in memory and wait for an event to trigger a request to use their services.

    * There are 5 basic process states:
        * Running: The process is being executed by the CPU.
        
        * Sleeping: The process is waiting for input from a user or another process.
        
        * Waiting: The process has received the input it was waiting for and is now ready to run when its turn arrives.
        
        * Stopped: The process is halted and will not run even when its turn arrives, unless a signal is sent to change its behaviour.
        
        * Zombie: The process is dead. Its entry is retained until the parent process permits it to die.

    * The *ps* and *top* commands can be used to view running processes.
    
    * The *pidof* or *pgrep* commands can be used to view the PID associated with a process name.
    
    * The *ps* command can be used to view the processes associated with a particular user. An example is shown below:
        ```shell
        ps -U root
        ```

    * To kill a process the *kill* or *pkill* commands can be used. Ordinary users can kill processes they own, while the *root* user can kill any process. The *kill* command requires a PID and the *pkill* command requires a process name. An example is shown below:
        ```shell
        pkill crond
        kill `pidof crond`
        ```

    * The list of signals accessible by *kill* can be seen by passing the *-l* option. The default signal is SIGTERM which signals for a process to terminate in an orderly fashion.

    * To use the SIGKILL signal:
        ```shell
        pkill -9 crond
        kill -9 `pgrep crond`
        ```

    * The *killall* command can be used to terminate all processes that match a specified criterion.
    
1. Adjust process scheduling

    * The priority of a process ranges from -20 (highest) to +19 (lowest). A higher niceness lowers the execution priority of a process and a lower niceness increases it. A child process inherits the niceness of its parent process.

    * To run a command with a lower (+2) priority:
        ```shell
        nice -2 top
        ```

    * To run a command with a higher (-2) priority:
        ```shell
        nice --2 top
        ```

    * To renice a running process:
        ```shell
        renice 5 1919
        ```
    
1. Manage tuning profiles

    * Tuned is a service which monitors the system and optimises the performance of the system for different use cases. There are pre-defined tuned profiles available in the `/usr/lib/tuned` directory. New profiles are created in the `/etc/tuned` directory. The *tuned-adm* command allows you to interact with the Tuned service.

    * To install and start the tuned service:
        ```shell
        yum install tuned
        systemctl enable --now tuned
        ```
 
    * To check the currently active profile:
        ```shell
        tuned-adm active
        ```

    * To check the recommended profile:
        ```shell
        tuned-adm recommend
        ```

    * To change the active profile:
        ```shell
        tuned-adm profile <profile-name>
        ```

    * To create a customised profile and set it as active:
        ```shell   
        mkdir /etc/tuned/<profile-name>
        vi /etc/tuned/<profile-name>/<profile-name.conf>
        # customise as required
        tuned-adm profile <profile-name>
        systmctl restart tuned.service
        ```

1. Locate and interpret system log files and journals

    * In RHEL logs capture messages generated by the kernel, daemons, commands, user activities, applications, and other events. The daemon that is responsible for system logging is called *rsyslogd*. The configuration file for *rsyslogd* is in the `/etc/rsyslog.conf` file. As defined in this configuration file, the default repository for most logs is the `/var/log` directory.

    * The below commands can be used to start and stop the daemon:
        ```shell   
        systemctl stop rsyslog
        systemctl start rsyslog
        ```
    * A script called *logrotate* in `/etc/cron.daily` invokes the *logrotate* command to rotate log files as per the configuration file.

    * The boot log file is available at `/var/log/boot.log` and contains logs generated during system start-up. The system log file is available in `/var/log/messages` and is the default location for storing most system activities.

1. Preserve system journals

    * In addition to system logging, the *journald* daemon (which is an element of *systemd*) also collects and manages log messages from the kernel and daemon processes. It also captures system logs and RAM disk messages, and any alerts generated during the early boot stage. It stores these messages in binary format in files called *journals* in the `/var/run/journal` directory. These files are structured and indexed for faster and easier searches and can be viewed and managed using the *journalctl* command.

    * By default, journals are stored temporarily in the `/run/log/journal` directory. This is a memory-based virtual file system and does not persist across reboots. To have journal files stored persistently in `/var/log/journal` the following commands can be run:
        ```shell   
        mkdir -p /var/log/journal
        systemctl restart systemd-journald
        ```

1. Start, stop, and check the status of network services

    * The *sshd* daemon manages ssh connections to the server. To check the status of this service:
        ```shell   
        systemctl is-active sshd        
        systemctl status sshd
        ```

    * To start and stop this service:
        ```shell   
        systemctl start sshd
        systemctl stop sshd
        ```

    * To enable or disable this service:
        ```shell      
        systemctl enable sshd
        systemctl disable sshd
        ```

    * To completely disable the service (i.e. to avoid loading the service at all):
        ```shell   
        systemctl mask sshd
        systemctl unmask sshd
        ```

1. Securely transfer files between systems

    * To transfer a file using the Secure Copy Protocol (SCP):
        ```shell   
        scp <file> <user>@<ip>:<file>
        ```

    * To transfer a directory:
        ```shell   
        scp /etc/ssh/* <user>@<ip>:/tmp
        ```

    * The direction of transfer can also be reversed:
        ```shell   
        scp <user>@<ip>:/tmp/sshd_config sshd_config_external
        ```

# Boot Procedure

## Grub
The location of Grub depends upon if you boot using BIOS system or en EFI system. To know if you are on a BIOS or EFI system. ``dmesg | grep "EFI"``. If you get nothing you booted using BIOS. You can ``dmesg | grep "BIOS"`` just to be sure. 
Another way is to use ``lsblk`` and if your boot disk is mounted on "/boot" than you are using BIOS.
If it says "/boot/efi". You guessed it, you are using EFI. :)

You need to edit the configuration file in "/etc/default/grub" to make changes persistent. Afterwards you must compile the changes to **grub.cfg**. You cannot edit that file directly.

### For BIOS/MBR

To make static changes to Grub edit "/etc/default/grub".
Commit the changes you make. ``grub2-mkconfig -o /boot/grub2/grub.cfg``

### For EFI

To make static changes to Grub edit "/etc/default/grub". Commit the changes you make. ``grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg``

## Systemd Targets

- emergency.target
  - Used for troubleshooting in a minimal environment.
- rescue.target
  - Also used for troubleshooting but not in a minimal environment.
- multi-user.target
  - The default environment, without a GUI.
- graphical.target
  - A fully functioning target like multi-user.target but with a GUI.

To know what target you are in do ``systemctl get-default``. You can also do ``systemctl list-dependencies``
Since I'm in the grapical.target, it lists first the default.target and then underneath it is multi-user.target.

### Change the default systemd target

Let's set the default to a non GUI target. ``systemctl set-default multi-user.target``
You must reboot the machine to enter the new default target.

### Change a running target

Let's say we are running in a multi-user.target (GUI) and we want to
switch to a non-graphical target we run this command. ``systemctl isolate multi-user.target``
Once we are done and we want to get back to the GUI we issue``systemctl set-default multi-user.target``

### Change a target during boot in Grub

Add this the end of the linux line. ``systemd.unit=emergency.target`` You can use any of the targets I listed before instead of emergency.

## Debug Shell

How to use early boot debug shell:
1. systemctl enable --now debug-shell.service
2. During boot you can access the virtual terminal on TTY9. (Ctrl-Alt-F9)
3. Enter a root shell without entering a password
4. When you have finished using it you should disable the service right away.
``systemctl disable --now debug-shell.service ``

# Managing processes

## General notes

Use cgroups instead of nice and renice to manage processes.

To list, read, and set kernel tunables.
``sysctl -a``

To make system tuning easier, use ``tuned``
``tuned`` is a systemd service that works with different profiles.
``tuned-adm list`` shows current profiles.

## General commands

**Find process by name**
``pidof processname``
``pgrep processname``
``ps aux | grep 'firefox'``

**Show hierarchical relations between processes**
``ps -fax``

**Show all processes owned by armann**
``ps -fU armann``

**Shows a process tree for a specific process**
``ps -d --forest -C sshd``


## Working with TOP

![Commands for TOP](RHCSA%20Notes%20Consolidated/pictures/topcommands.png)

![More Top Commands](RHCSA%20Notes%20Consolidated/pictures/topmorecommands.png)

# Rsyslog

For information on journalctl, go to the Systemd page.

Rsyslog needs the rsyslogd service to be running.

The configuration file is in "/etc/rsyslog.conf".
Drop files can be places in "/etc/rsyslog.d/"

Read https://www.rsyslog.com/doc/master/index.html and add to this page.

### kdump
* Log file is `/etc/kdump.conf`
* Purpose of kdump is to take a snapshot of your kernel and save it so you can use it for troubleshooting purposes

## Kernel Modules

* `lsmod` - Show the status of modules in the Linux Kernel
* `modinfo` - Show information about a Linux Kernel module
* `modprobe` - add and remove modules from the Linux Kernel
### lsmod
`lsmod | less` - get an idea of what modules are currently loaded on the system
`lsmod | grep kvm` - search for a particular module

### modinfo
`modinfo kvm | less` - get the status of the 'kvm' module

### modprobe 
```shell
$ lsmod | grep kvm 
#no return, not loaded
$ modinfo kvm
...
# returns info about the module
$ modprobe kvm
$ lsmod | grep kvm
kvm                   970752  0
irqbypass              16384  1 kvm
# module is now loaded
$ modprobe -r kvm
# Unloads module
```

