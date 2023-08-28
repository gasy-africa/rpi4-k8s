# Format and Mount a USB drive on Raspberry Pi


## :floppy_disk: Format USB drive


```
sudo fdisk /dev/sda
```
> Returns :
```yaml
Welcome to fdisk (util-linux 2.37.2).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): g
Created a new GPT disklabel (GUID: 0F7219E2-0A8C-7D4D-BEF2-E69AECE1EA54).
The device contains 'dos' signature and it will be removed by a write command. See fdisk(8) man page and --wipe option for more details.

Command (m for help): m

Help:

  GPT
   M   enter protective/hybrid MBR

  Generic
   d   delete a partition
   F   list free unpartitioned space
   l   list known partition types
   n   add a new partition
   p   print the partition table
   t   change a partition type
   v   verify the partition table
   i   print information about a partition

  Misc
   m   print this menu
   x   extra functionality (experts only)

  Script
   I   load disk layout from sfdisk script file
   O   dump disk layout to sfdisk script file

  Save & Exit
   w   write table to disk and exit
   q   quit without saving changes

  Create a new label
   g   create a new empty GPT partition table
   G   create a new empty SGI (IRIX) partition table
   o   create a new empty DOS partition table
   s   create a new empty Sun partition table


Command (m for help): n
Partition number (1-128, default 1): 1
First sector (2048-121077727, default 2048): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-121077727, default 121077727): 

Created a new partition 1 of type 'Linux filesystem' and of size 57.7 GiB.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

```
sudo fdisk -l /dev/sda
```
> Returns :
```yaml
Disk /dev/sda: 57.73 GiB, 61991813632 bytes, 121077761 sectors
Disk model: DataTraveler 3.0
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 0F7219E2-0A8C-7D4D-BEF2-E69AECE1EA54

Device     Start       End   Sectors  Size Type
/dev/sda1   2048 121077727 121075680 57.7G Linux filesystem
```
