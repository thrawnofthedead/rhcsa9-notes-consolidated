### Configure local storage

1. List, create, delete partitions on MBR and GPT disks

    * Data is stored on disk drives that are logically divided into partitions. A partition can exist on a portion of a disk, an entire disk, or across multiple disks. Each partition can contain a file system, raw data space, swap space, or dump space.

    * A disk in RHEL can be divided into several partitions. This partition information is stored on the disk in a small region, which is read by the operating system at boot time. This is known as the Master Boot Record (MBR) on BIOS-based systems, and GUID Partition Table (GPT) on UEFI-based systems. At system boot, the BIOS/UEFI scans all storage devices, detects the presence of MBR/GPT, identifies the boot disks, loads the boot loader program in memory from the default boot disk, executes the boot code to read the partition table and identify the `/boot` partition, and continues with the boot process by loading the kernel in the memory and passing control over to it.

    * MBR allows the creation of only up to 4 primary partitions on a single disk, with the option of using one of the 4 partitions as an extended partition to hold an arbitrary number of logical partitions. MBR also lacks addressing space beyond 2TB due to its 32-bit nature and the disk sector size of 512-byte that it uses. MBR is also non-redundant, so a system becomes unbootable if it becomes corrupted somehow.

    * GPT is a newer 64-bit partitioning standard developed and integrated to UEFI firmware. GPT allows for 128 partitions, use of disks much larger than 2TB, and redundant locations for the storage of partition information. GPT also allows a BIOS-based system to boot from a GPT disk, using the boot loader program stored in a protective MBR at the first disk sector.

    * To list the mount points, size, and available space:
        ```shell   
        df -h
        ```

    * In RHEL block devices are an abstraction for certain hardware, such hard disks. The *blkid* command lists all block devices. The *lsblk* command lists more details about block devices. If for some reason you don't think that is correct you can ``cat /proc/partitions``

    * To list all disks and partitions:
        ```shell   
        fdisk -l # MBR
        gdisk -l # GPT
        ```

    * For MBR based partitions the *fdisk* utility can be used to create and delete partitions. To make a change to a disk:
        ```shell   
        fdisk <disk>
        ```

    * For GPT based partitions the *gdisk* utility can be used to create and delete partitions. To make a change to a disk:
        ```shell   
        gdisk <disk>
        ```

    * To inform the OS of partition table changes:
        ```shell   
        partprobe
        ```
    * Deleting partitions in Fdisk does not delete the data on the partition. The only thing that's deleted is a line in the partition table. If you delete a partition in order to extend it, when you create a new one, Fdisk asks "*Do you want to remove the signature? [Y]es/[N]o"* Select **no**, otherwise you will delete all data on the partition.

2. Create and remove physical volumes

    * Logical Volume Manager (LVM) is used to provide an abstraction layer between the physical storage and the file system. This enables the file system to be resized, to span across multiple physical disks, use random disk space, etc. One or more partitions or disks (physical volumes) can form a logical container (volume group), which is then divided into logical partitions (called logical volumes). These are further divided into physical extents (PEs) and logical extents (LEs).

    * A physical volume (PV) is created when a block storage device is brought under LVM control after going through the initialisation process. This process constructs LVM data structures on the device, including a label on the second sector and metadata information. The label includes a UUID, device size, and pointers to the locations of data and metadata areas.

    * A volume group (VG) is created when at least one physical volume is added to it. The space from all physical volumes in a volume group is aggregated to form one large pool of storage, which is then used to build one or more logical volumes. LVM writes metadata information for the volume group on each physical volume that is added to it.

    * To view physical volumes:
        ```shell   
        pvs
        ```

    * To view physical volumes with additional details:
        ```shell   
        pvdisplay
        ```

    * To initialise a disk or partition for use by LVM:
        ```shell   
        pvcreate <disk>
        ```

    * To remove a physical volume from a disk:
        ```shell   
        pvremove <disk>
        ```

1. Assign physical volumes to volume groups

    * To view volume groups:
        ```shell   
        vgs
        ```

    * To view volume groups with additional details:
        ```shell   
        vgdisplay
        ```

    * To create a volume group:
        ```shell   
        vgcreate <name> <disk>
        ```

    * To extend an existing volume group:
        ```shell   
        vgextend <name> <disk>
        ```

    * To remove a disk from a volume group:
        ```shell   
        vgreduce <name> <disk>
        ```

    * To remove the last disk from a volume group:
        ```shell   
        vgremove <name> <disk>
        ```

1. Create and delete logical volumes

    * To view logical volumes:
        ```shell   
        lvs
        ```

    * To view logical volumes with additional details:
        ```shell   
        lvdisplay
        ```

    * To create a logical volume in vg1 named lv1 and with 4GB of space:
        ```shell   
        lvcreate -L 4G -n lv1 vg1 
        ```

    * To extend the logical volume by 1GB:
        ```shell   
        lvextend -L+1G <lvpath>
        ```

    * To extend the logical volume by 1GB:
        ```shell   
        lvextend -L+1G <lvpath>
        ```

    * To reduce the size for a logical volume by 1GB:
        ```shell   
        lvreduce -L-1G <lvpath>
        ```

    * To remove a logical volume:
        ```shell   
        umount <mountpoint>
        lvremove <lvpath>
        ```

1. Configure systems to mount file systems at boot by Universally Unique ID (UUID) or label
    
    * The `/etc/fstab` file is a system configuration file that lists all available disks, disk partitions and their options. Each file system is described on a separate line. The `/etc/fstab` file is used by the *mount* command, which reads the file to determine which options should be used when mounting the specific device. A file system can be added to this file so that it is mounted on boot automatically.
    
    * The *e2label* command can be used to change the label on ext file systems. This can then be used instead of the UUID.
    
1. Add new partitions and logical volumes, and swap to a system non-destructively
    1. Create the parition. Partitions types are important on the exam. In Fdisk remember to set the type to swap.
    2. Set up a Linux swap area on the device or file we created. ``mkswap /dev/vdd1`` You could also do ``mkswap -L myswapspace /dev/  vdd1`` and use the label to mount it in fstab.
    3. Mount the swap space in "/etc/fstab". In the first field you can use /dev/vdd1 or the UUID, or the label if you created one. !  [mkswap](./pictures/mkswap.png)
    4. In the "/etc/fstab" file remember to mount the swap file to **none** and set the filesystem as swap.
    5. To activate the swap run ``swapon -a`` 
    6. Check out the new swap space ``free -h``

If you have multiple swap files you can see the priority with ``swapon -s``

### Swap from file

1. sudo dd if=/dev/zero of=/swap count=2048 bs=1MiB
2. chmod 600 /swap
3. mkswap /swap
4. swapon /swap
5. fstab: /swap swap swap defaults 0 0

    * Virtual memory is equal to RAM plus swap space. A swap partition is a standard disk partition that is designated as swap space by the *mkswap* command. A file can also be used as swap space but that is not recommended.

    * To create a swap:
        ```shell   
        mkswap <device>
        ```

    * To enable a swap:
        ```shell   
        swapon <device>
        ```

    * To check the status of swaps:
        ```shell   
        swapon -s
        ```

    * To disable a swap:
        ```shell   
        swapoff <device>
        ```

    * The `/etc/fstab` file will need a new entry for the swap so that it is created persistently.
# Storage


### Listing devices


## Mount

This command checks the "/etc/fstab" file is valid.
``findmnt --verify`` 

To mount all unmounted devices.
``mount -a``

*During the exam, reboot the machine to verify all mounts!*
If your system can't boot because of a problem with "/etc/fstab" you will fail the exam.

In datacenter environments, block device names may change. Different solutions exist for persistent naming.

- **UUID**: a UUID is automatically generated for each device that contains a filesystem or anything similar.
- **Label**: while creating the filesystem, the option ``-L`` can be used to set an arbitrary name that can be used for mounting the filesystem.

To set a label on an XFS filesystem you can use ``xfs_admin -L mygreatlabel /dev/sda6`` To label it the volume needs to be unmounted. In "/etc/fstab" the **first field** would be ``LABEL=mygreatlabel`` instead of "/dev/sda6."

If you want to mount it based on UUID, you can find the UUID with ``blkid``.

### Mount via Systemd

Based on the example below, the mount filename would be mnt-vda6.mount and located in /etc/systemd/system/. \
\
[Unit]\
Description=Mount vda6 \
\
[Mount] \
What=UUID="07b6fb67-8687-40d5-81a4-bb58e28e1e0a" \
Where=/mnt/vda6 \
Type=xfs \
Options=defaults \
\
[Install] \
WantedBy=multi-user.target



