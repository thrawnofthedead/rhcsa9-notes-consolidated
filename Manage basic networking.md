### Manage basic networking

1. Configure IPv4 and IPv6 addresses

    * The format of an IPv4 address is a set of 4 8-bit integers that gives a 32-bit IP address.  The format of an IPv6 is a set of 8 16-bit hexadecimal numbers that gives a 128-bit IP address.

    * The *nmcli* command is used to configure networking using the NetworkManager service. This command is used to create, display, edit, delete, activate, and deactivate network connections. Each network device corresponds to a Network Manager device.

    * Using nmcli with the connection option lists the available connection profiles in NetworkManager.

    * The *ip* command can also be used for network configuration. The main difference between ip and nmcli is that changes made with the ip command are not persistent.
    
    * To view system IP addresses:
        ```shell
        ip addr
        ```

    * To show the current connections:
        ```shell
        nmcli connection show
        ```

    * Using nmcli with the device option lists the available network devices in the system.

    * To view the current network device status and details:
        ```shell
        nmcli device status
        nmcli device show
        ```

    * To add an ethernet IPv4 connection:
        ```shell
        nmcli connection add con-name <name> ifname <name> type ethernet ip4 <address> gw4 <address>
        ```

    * To manually modify a connection:
        ```shell
        nmcli connection modify <name> ipv4.addresses <address>
        nmcli connection modify <name> ipv4.method manual
        ```

    * To delete a connection:
        ```shell
        nmcli connection delete <name>
        ```

    * To activate a connection:
        ```shell
        nmcli connection up <name>
        ```

    * To deactivate a connection:
        ```shell
        nmcli connection down <name>
        ```

    * To check the DNS servers that are being used:
        ```shell
        cat /etc/resolv.conf
        ```

    * To change the DNS server being used:
        ```shell
        nmcli con mod <name> ipv4.dns <dns>
        systemctl restart NetworkManager.service
        ```
   
1. Configure hostname resolution

    * To lookup the IP address based on a host name the *host* or *nslookup* commands can be used.

    * The `/etc/hosts` file is like a local DNS. The `/etc/nsswitch.conf` file controls the order that resources are checked for resolution. 

    * To lookup the hostname:
        ```shell
        hostname -s # short
        hostname -f # fully qualified domain name
        ```

    * The hostname file is in `/etc/hostname`. To refresh any changes run the *hostnamectl* command.

1. Configure network services to start automatically at boot

    * To install a service and make it start automatically at boot:
        ```shell
        dnf install httpd
        systemctl enable httpd
        ```
    
    * To set a connection to be enabled on boot:
        ```shell
        nmcli connection modify eth0 connection.autoconnect yes
        ```

1. Restrict network access using firewall-cmd/firewall

    * Netfilter is a framework provided by the Linux kernel that provides functions for packet filtering. In RHEL 7 and earlier iptables was the default way of configuring Netfilter. Disadvantages of ipables were that a separate version (ip6tables) was required for ipv6, and that the user interface is not very user friendly.

    * The default firewall system in RHEL 8 is *firewalld*. Firewalld is a zone-based firewall. Each zone can be associated with one or more network interfaces, and each zone can be configured to accept or deny services and ports. The *firewall-cmd* command is the command line client for firewalld.

    * To check firewall zones:
        ```shell
        firewall-cmd --get-zones
        ```

    * To list configuration for a zone:
        ```shell
        firewall-cmd --zone work --list-all
        ```

    * To create a new zone:
        ```shell
        firewall-cmd --new-zone servers --permanent
        ```

    * To reload firewall-cmd configuration:
        ```shell
        firewall-cmd --reload
        ```

    * To add a service to a zone:
        ```shell
        firewall-cmd --zone servers --add-service=ssh --permanent
        ```

    * To add an interface to a zone:
        ```shell
        firewall-cmd --change-interface=enp0s8 --zone=servers --permanent
        ```

    * To get active zones:
        ```shell
        firewall-cmd --get-active-zones
        ```

    * To set a default zone:
        ```shell
        firewall-cmd --set-default-zone=servers
        ```

    * To check the services allowed for a zone:
        ```shell
        firewall-cmd --get-services
        ```

    * To add a port to a zone:
        ```shell
        firewall-cmd --add-port 8080/tcp --permanent --zone servers
        ```

    * To remove a service from a zone:
        ```shell
        firewall-cmd --remove-service https --permanent --zone servers
        ```

    * To remove a port from a zone:
        ```shell
        firewall-cmd --remove-port 8080/tcp --permanent --zone servers
        ```
# Networking

## Notes

Do not use ifconfig anymore. It's been deprecated for over 20 years now.
Ifconfig does not support secondary ip addresses for example.

## See information for your NIC

### Display IP addresses
``ip ad`` or ``nmcli``

### Display route/gateway information
``ip -4 route``
``ip -6 route``

Legacy command to see route information.
``netstat -rn`` or ``route -n``

### Display ARP table
``arp -a``

### Display the ARP default timeout
``cat /proc/sys/net/ipv4/neigh/default/gc_stale_time``

## Change settings for your NIC

### Assign IP address

You can use the "graphical" ``nmtui`` tool to configure your network connection. If you use ``nmtui``, remember to **set the subnet mask** when you enter the ip address. Using ``nmtui`` for the exam is better, it will save time.

![nmtui](RHCSA%20Notes%20Consolidated/pictures/nmtui.png)

Another way is to use the ``nmcli`` tool. Remember to swap out the name for the correct name you see when you execute ``nmcli connection show``
 ``nmcli connection edit enp1s0``

Now you are in the nmcli interface. Type ``print`` to see detailed information for the connection named "enp1s0". To see the name of all your connection, ``nmcli connection show`` and ``nmcli device show``

Find the connection name, ``nmcli connection show``
Make sure that the **bash-completion package** is installed when working with nmcli.

Assign the IP address to the correct connection name.
``nmcli connection modify enp0s3 ipv4.addresses 192.168.1.21/24``

ip is an excellent command for troubleshooting but using the ip command only changes runtime environment, **it does not change anything in the configuration files.**

Here are a few nmcli examples:

``nmcli device status`` \
``nmcli connection show -active`` \
If the connection is unmanaged or not connecting, try this command. \
``sudo nmcli connection mod <connection-name> connection.autoconnect yes``

**Activate Changes** \
``nmcli connection reload`` \
This only makes the NM aware of the changes. \
You have to take the connection down and then up (``nmcli con down NAME; nmcli con up NAME``) or most changes can be applied directly with ``nmcli dev reapply NAME``

### Change the gateway

``nmcli connection modify enp0s3 ipv4.gateway 192.168.1.254``

### Use static instead of DHCP

``nmcli connection modify enp0s3 ipv4.method static``

### Disabling and enabling an interface
``ip link set ens33 down``
``ip link set ens33 up``

### Changing the MTU for e.g., iSCSI
``nmcli con mod ensp92 802-3-ethernet.mtu 9000``

## Firewall (netfilter/nftables)

On the exam it's better to restart services than to reload them.

The netfilter framework in the Linux kernel manages firewall operations, and it forwards specific operations to kernel modules.
- Packet filtering
- Network address translation
- Port forwarding

### Firewalld

Firewalld is a good interface to create and manage a simple firewall but the framework behind it is the Netfilter (nftables) firewall. 

To see config files for services, check out.
"/usr/lib/firewalld/"

### General commands

``firewall-cmd --list-all``

``firewall-cmd --get-services``

``firewall-cmd --add-service squid --permanent`` \
Remember to use the permanent switch, otherwise the rule is written only to the runtime and is lost if you restart firewalld or the server!

``firewall-cmd --reload``

Add IP address to the trusted zone.
``firewall-cmd --zone=trusted --add-source=192.168.124.1 --permanent``

List configuration for all zones.
``firewall-cmd --list-all-zones``

### Zones

A zone is a default configuration to which network cards can be assigned to apply specific settings. 

### Service

You only need to know the service part for the RHCSA exam!

### Ports

Optional elements to allow access to specific ports.

## Network Sockets

Use ``ss`` to show socket information. This will show all connections.

``ss -tu`` shows connected TCP and UDP sockets.

``ss -tua`` shows connected TCP and UDP sockets + sockets in a listening state.

``ss -tulpn`` Shows TCP and UDP sockets in a listening state, it also adds process names or PID to the output. 



## Debug

- Use ``nm-connection-editor`` if you have issues with certs for you network cards.
- Make sure your subnet mask is correct.
- Ping your gateway to see if you can reach the router.
- DNS not working, check "/etc/resolv.conf" to make sure it's correct.
