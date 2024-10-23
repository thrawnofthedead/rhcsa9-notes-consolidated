# Welcome!

This is a consolidation of two RHCSA Note Repositories ([jrandj/redhat](https://github.com/jrandj/redhat)/[1980is/rhcsa-9](https://github.com/1980is/rhcsa-9)) as well as my own notes from the Stormwind Studio's training course.

[The official Red Hat 9.1 Release Notes in pdf format.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/9/pdf/9.1_release_notes/red_hat_enterprise_linux-9-9.1_release_notes-en-us.pdf)

[The exam objectives](https://github.com/1980is/rhcsa-9/blob/main/RHCSA%209%20Exam%20Objectives.md)

## Table of Contents

### [Understand and use essential tools](https://github.com/thrawnofthedead/rhcsa9-notes-consolidated/blob/main/Understand%20and%20use%20essential%20tools.md)

- Access a shell prompt and issue commands with correct syntax
- Use input-output redirection (>, >>, |, 2>, etc.)
- Use grep and regular expressions to analyze text
- Access remote systems using SSH
- Log in and switch users in multiuser targets
- Archive, compress, unpack, and uncompress files using tar, gzip, and bzip2
- Create and edit text files
- Create, delete, copy, and move files and directories
- Create hard and soft links
- List, set, and change standard ugo/rwx permissions
- Locate, read, and use system documentation including man, info, and files in /usr/share/doc

### [Create simple shell scripts](https://github.com/thrawnofthedead/rhcsa9-notes-consolidated/blob/main/Create%20simple%20shell%20scripts.md)

- Conditionally execute code (use of: if, test, [], etc.)
- Use Looping constructs (for, etc.) to process file, command line input
- Process script inputs ($1, $2, etc.)
- Processing output of shell commands within a script

### [Operate running systems](https://github.com/thrawnofthedead/rhcsa9-notes-consolidated/blob/main/Operate%20running%20systems.md)

- Boot, reboot, and shut down a system normally
- Boot systems into different targets manually
- Interrupt the boot process in order to gain access to a system
- Identify CPU/memory intensive processes and kill processes
- Adjust process scheduling
- Manage tuning profiles
- Locate and interpret system log files and journals
- Preserve system journals
- Start, stop, and check the status of network services
- Securely transfer files between systems

### [Configure local storage](https://github.com/thrawnofthedead/rhcsa9-notes-consolidated/blob/main/Configure%20local%20storage.md)

- List, create, delete partitions on MBR and GPT disks
- Create and remove physical volumes
- Assign physical volumes to volume groups
- Create and delete logical volumes
- Configure systems to mount file systems at boot by universally unique ID (UUID) or label
- Add new partitions and logical volumes, and swap to a system non-destructively

### [Create and configure file systems](https://github.com/thrawnofthedead/rhcsa9-notes-consolidated/blob/main/Create%20and%20configure%20file%20systems.md)

- Create, mount, unmount, and use vfat, ext4, and xfs file systems
- Mount and unmount network file systems using NFS
- Configure autofs
- Extend existing logical volumes
- Create and configure set-GID directories for collaboration
- Diagnose and correct file permission problems

### [Deploy, configure, and maintain systems](https://github.com/thrawnofthedead/rhcsa9-notes-consolidated/blob/main/Deploy%2C%20configure%2C%20and%20maintain%20systems.md)

- Schedule tasks using at and cron
- Start and stop services and configure services to start automatically at boot
- Configure systems to boot into a specific target automatically
- Configure time service clients
- Install and update software packages from Red Hat Network, a remote repository, or from the local file system
- Modify the system bootloader

### [Manage basic networking](https://github.com/thrawnofthedead/rhcsa9-notes-consolidated/blob/main/Manage%20basic%20networking.md)

- Configure IPv4 and IPv6 addresses
- Configure hostname resolution
- Configure network services to start automatically at boot
- Restrict network access using firewall-cmd/firewall

### [Manage users and groups](https://github.com/thrawnofthedead/rhcsa9-notes-consolidated/blob/main/Manage%20users%20and%20groups.md)

- Create, delete, and modify local user accounts
- Change passwords and adjust password aging for local user accounts
- Create, delete, and modify local groups and group memberships
- Configure superuser access

### [Manage security](https://github.com/thrawnofthedead/rhcsa9-notes-consolidated/blob/main/Manage%20security.md)

- Configure firewall settings using firewall-cmd/firewalld
- Manage default file permissions
- Configure key-based authentication for SSH
- Set enforcing and permissive modes for SELinux
- List and identify SELinux file and process context
- Restore default file contexts
- Manage SELinux port labels
- Use boolean settings to modify system SELinux settings
- Diagnose and address routine SELinux policy violations

### [Manage containers](https://github.com/thrawnofthedead/rhcsa9-notes-consolidated/blob/main/Manage%20containers.md)

- Find and retrieve container images from a remote registry
- Inspect container images
- Perform container management using commands such as podman and skopeo
- Perform basic container management such as running, starting, stopping, and listing running containers
- Run a service inside a container
- Configure a container to start automatically as a systemd service
- Attach persistent storage to a container
