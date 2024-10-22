### Manage security

1. Configure firewall settings using firewall-cmd/firewalld

    * Network settings such as masquerading and IP forwarding can also be configured in the firewall-config GUI application. To install this application:
        ```shell
        dnf install firewall-config
        ```

    * To set port forwarding in the kernel setting:
        ```shell
        vi /etc/sysctl.conf
        # add line
        net.ipv4.ip_forward=1
        # save file
        sysctl -p
        ```

1. Create and use file access control lists

    * To give a user read and write access to a file using an access control list:
        ```shell
        setfacl -m u:user1:rw- testFile
        getfacl testFile
        ```

    * To restrict a user from accessing a file using an access control list:
        ```shell
        setfacl -m u:user1:000 testFile
        getfacl testFile
        ```

    * To remove an access control list for a user:
        ```shell
        setfacl -x u:user1 testFile
        getfacl testFile
        ```

    * To give a group read and execute access to a directory recursively using an access control list:
        ```shell
        setfacl -R -m g:IT-Support:r-x testDirectory
        getfacl testFile
        ```

    * To remove an access control list for a group:
        ```shell
        setfacl -x g:IT-Support testDirectory
        getfacl testFile
        ```

1. Configure key-based authentication for SSH

    * To generate an id_rsa and id_rsa.pub files:
        ```shell
        ssh-keygen
        ```
    * To enable ssh for a user:
        ```shell
        touch authorized_keys
        echo "publicKey" > /home/new_user/.ssh/authorized_keys
        ```

    * To copy the public key from server1 to server2:
        ```shell
        ssh-copy-id -i ~/.ssh/id_rsa.pub server2
        cat ~/.ssh/known_hosts # validate from server1
        ```

1. Set enforcing and permissive modes for SELinux

    * Security Enhanced Linux (SELinux) is an implementation of Mandatory Access Control (MAC) architecture developed by the U.S National Security Agency (NSA). MAC provides an added layer of protection beyond the standard Linux Discretionary Access Control (DAC), which includes the traditional file and directory permissions, ACL settings, setuid/setgid bit settings, su/sudo privileges etc.

    * MAC controls are fine-grained; they protect other services in the event one of the services is negotiated. MAC uses a set of defined authorisation rules called policy to examine security attributes associated with subjects and objects when a subject tries to access an object and decides whether to permit this access attempt. SELinux decisions are stored in a special cache referred to as Access Vector Cache (AVC).

    * When an application or process makes a request to access an object, SELinux checks with the AVC, where permissions are cached for subjects and objects. If a decision is unable to be made, it sends the request to the security server. The security server checks for the security context of the app or process and the object. Security context is applied from the SELinux policy database. 

    * To check the SELinux status:
        ```shell
        getenforce
        sestatus
        ```

    * To put SELinux into permissive mode modify the `/etc/selinux/config` file as per the below and reboot:
        ```shell
        SELINUX=permissive
        ```

    * Messages logged from SELinux are available in `/var/log/messages`.

1. List and identify SELinux file and process context

    * To view the SELinux contexts for files:
        ```shell
        ls -Z
        ```

    * To view the contexts for a user:
        ```shell
        id -Z
        ```
    * The contexts shown follow the user:role:type:level syntax. The SELinux user is mapped to a Linux user using the SELinux policy. The role is an intermediary between domains and SELinux users. The type defines a domain for processes, and a type for files. The level is used for Multi-Category Security (MCS) and Multi-Level Security (MLS).

    * To view the processes for a user:
        ```shell
        ps -Z # ps -Zaux to see additional information
        ```

1. Restore default file contexts

    * To view the SELinux contexts for files:
        ```shell
        chcon unconfined:u:object_r:tmp_t:s0
        ```

    * To restore the SELinux contexts for a file:
        ```shell
        restorecon file.txt
        ```

    * To restore the SELinux contexts recursively for a directory:
        ```shell
        restorecon -R directory
        ```

1. Use Boolean settings to modify system SELinux settings

    * SELinux has many contexts and policies already defined. Booleans within SELinux allow common rules to be turned on and off.

    * To check a SELinux Boolean setting:  
        ```shell
        getsebool -a | grep virtualbox
        ```

    * To set a SELinux Boolean setting permanently:  
        ```shell
       setsebool -P use_virtualbox on
        ```

1. Diagnose and address routine SELinux policy violations

    * The SELinux Administration tool is a graphical tool that enables many configuration and management operations to be performed. To install and run the tool:
       ```shell
       yum install setools-gui
       yum install policycoreutils-gui
       system-config-selinux
       ```

    * SELinux alerts are written to `/var/log/audit/audit.log` if the *auditd* daemon is running, or to the `/var/log/messages` file via the *rsyslog* daemon in the absence of *auditd*.

    * A GUI called the SELinux Troubleshooter can be accessed using the *sealert* command. This allows SELinux denial messages to be analysed and provides recommendations on how to fix issues.

# SELinux

Selinux state is either enabled or disabled. You must reboot to switch between the two.
Enabled has two modes, permissive or enforcing.

If you want to temporarily disable SELinux to debug or analyze something you should
change the SELinux state while booting by using a kernel parameter.

- enforcing=0 will start SELinux in permissive mode. 
- enforcing=1 will start SELinux in enforcing mode.
- selinux=0 will disable SELinux.

``getenforce`` to see the status of SELinux.

``setenforce`` to switch between modes, permissive or enforcing. This is temporary, it will go back
to whatever is defined in "/etc/selinux/config" after the server is rebooted.

``ps Zaux``

## Context and Context types

### Context

Remember to look at `` man semanage-fcontext`` during the exam. At the bottom you have examples you can use.

When files are created in a directory, they typically inherit the context of the parent directory and most services don't need additional SELinux configuration if default settings are used.

When files are copied, they typically inherit the context of the parent directory. If it's not relabeled correctly you can use ``restorecon -Rv /mydirectory`` 

Use ``semanage fcontext`` to set the file context label. This will write the context to the SELinux Policy, but **it is not written yet to the filesystem**.
A second step is necessary to write it to the filesystem by using ``restorecon``

Instead of using ``restorecon`` you can use ``touch /.autorelabel`` to relabel all files to the context that is specified in the policy. That should be our last option, it happens while rebooting.
So ``restorecon`` is preferred.


Use ``semanage fcontext -a`` to set a new context label. If you get an error that it already exists. Use the -m option.

Use ``semanage fcontext -m`` to modify an existing context label.

**Important for the exam!** See ``man semanage-fcontect`` for documentation.

If you apply non-default configuration, check the default configuration context setting but if that's not available install the man pages with  ``dnf install selinux-policy-doc`` and then ``man -k _selinux | grep http`` as an example.

Do **NOT** use ``chcon`` as the changes it makes may be overwritten.

Use ``semanage fcontext -I -C`` to show only settings that have changed in the current policy.

### Context Types

In most configurations, only context type matters, so you can safely ignore user and role for RHCSA. Every object is labeled with a context label.

- user: user specific context
- role: role specific context
- type: flags which type of operation is allowed on this object


Many commands support the -Z option to see the context for files.
``ls -Z /etc/``

Context types are used in the rules in the policy to define which source object has access to which target object.

### Booleans

A boolean is an easy-to-use configuration switch to enable or disable specific parts of the SELinux policy.

For an overview of all booleans, use ``semanage boolean -I`` or ``getsebool -a``.

To see all booleans that have a non-default setting ``semanage boolean -l -C`` 

An example to learn from. ``getsebool -a | grep ftp``

To set booleans, use ``setsebool -P boolean [on|off]``

### Networking

Network ports are also provided with an SELinux context label. The SELinux policy is configured to allow default port access. For any non-default port access, use ``semanage-port`` to apply the right label to the port.

Use the examples section in man ``semanage-port`` for examples.

### Debug

- Check to see if SEAlert is installed. ``dnf provides */sealert``
  - ``journalctl | grep sealert``
  - Find what you are looking for and run the the command it shows you, e.g., `` sealert -l 59cd7b4c-2b08-4bfc-8c15-4369f46c7355``
- Check the audit log for AVC errors. It's the most important source to debug SELinux problems.
  - ``grep AVC /var/log/audit/audit.log``
- 
