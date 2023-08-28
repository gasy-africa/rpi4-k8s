# How to: Raspberry Pi 4 as an iSCSI SAN (iSCSI Target)


## :cl: Install 

1. First we need to install the `targetcli` interface to configure the `target`.
As root (or use `sudo`) run the following command if you’re running Ubuntu Server.

```
apt install targetcli-fb
```
> Returns
```yaml
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  python3-configshell-fb python3-pyudev python3-rtslib-fb python3-urwid
Suggested packages:
  python-urwid-doc
The following NEW packages will be installed:
  python3-configshell-fb python3-pyudev python3-rtslib-fb python3-urwid targetcli-fb
0 upgraded, 5 newly installed, 0 to remove and 2 not upgraded.
Need to get 322 kB of archives.
After this operation, 2041 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://ports.ubuntu.com/ubuntu-ports jammy/main arm64 python3-urwid arm64 2.1.2-2build1 [171 kB]
Get:2 http://ports.ubuntu.com/ubuntu-ports jammy/main arm64 python3-configshell-fb all 1:1.1.28-2 [28.3 kB]
Get:3 http://ports.ubuntu.com/ubuntu-ports jammy/main arm64 python3-pyudev all 0.22.0-2 [33.2 kB]
Get:4 http://ports.ubuntu.com/ubuntu-ports jammy/main arm64 python3-rtslib-fb all 2.1.74-0ubuntu4 [49.8 kB]
Get:5 http://ports.ubuntu.com/ubuntu-ports jammy/main arm64 targetcli-fb all 1:2.1.53-1ubuntu3 [40.3 kB]
Fetched 322 kB in 1s (439 kB/s)        
Selecting previously unselected package python3-urwid.
(Reading database ... 103558 files and directories currently installed.)
Preparing to unpack .../python3-urwid_2.1.2-2build1_arm64.deb ...
Unpacking python3-urwid (2.1.2-2build1) ...
Selecting previously unselected package python3-configshell-fb.
Preparing to unpack .../python3-configshell-fb_1%3a1.1.28-2_all.deb ...
Unpacking python3-configshell-fb (1:1.1.28-2) ...
Selecting previously unselected package python3-pyudev.
Preparing to unpack .../python3-pyudev_0.22.0-2_all.deb ...
Unpacking python3-pyudev (0.22.0-2) ...
Selecting previously unselected package python3-rtslib-fb.
Preparing to unpack .../python3-rtslib-fb_2.1.74-0ubuntu4_all.deb ...
Unpacking python3-rtslib-fb (2.1.74-0ubuntu4) ...
Selecting previously unselected package targetcli-fb.
Preparing to unpack .../targetcli-fb_1%3a2.1.53-1ubuntu3_all.deb ...
Unpacking targetcli-fb (1:2.1.53-1ubuntu3) ...
Setting up python3-urwid (2.1.2-2build1) ...
Setting up python3-configshell-fb (1:1.1.28-2) ...
Setting up python3-pyudev (0.22.0-2) ...
Setting up python3-rtslib-fb (2.1.74-0ubuntu4) ...
Created symlink /etc/systemd/system/multi-user.target.wants/rtslib-fb-targetctl.service → /lib/systemd/system/rtslib-fb-targetctl.service.
Setting up targetcli-fb (1:2.1.53-1ubuntu3) ...
Processing triggers for man-db (2.10.2-1) ...
Scanning processes...                                                                                             
Scanning processor microcode...                                                                                   
Scanning linux images...                                                                                          

Running kernel seems to be up-to-date.

Failed to check for processor microcode upgrades.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
``` 

- [ ] Checking the `Storage Objects` and `Targets`

:bulb: Note: The `iscsi` Targets should be empty

```
sudo targetcli ls
```
> Returns :
```yaml
Warning: Could not load preferences file /root/.targetcli/prefs.bin.
o- / ....................................................................................................... [...]
  o- backstores ............................................................................................ [...]
  | o- block ................................................................................ [Storage Objects: 0]
  | o- fileio ............................................................................... [Storage Objects: 0]
  | o- pscsi ................................................................................ [Storage Objects: 0]
  | o- ramdisk .............................................................................. [Storage Objects: 0]
  o- iscsi .......................................................................................... [Targets: 0]
  o- loopback ....................................................................................... [Targets: 0]
  o- vhost .......................................................................................... [Targets: 0]
```

# [:back:](..)

 # References
 
 - [ ] [:x: Missing iscsi_target_mod for targetcli on Ubuntu 22.04 64-bit](https://www.reddit.com/r/raspberry_pi/comments/wmubyl/missing_iscsi_target_mod_for_targetcli_on_ubuntu/)

* Module iscsi_target_mod not found
  
```
sudo targetcli ls
```
> Returns :
```yaml
Warning: Could not load preferences file /root/.targetcli/prefs.bin.
b'modprobe: FATAL: Module iscsi_target_mod not found in directory /lib/modules/5.15.0-1026-raspi\n'
```

- [ ] [ iscsid: can not create NETLINK_ISCSI socket [NEEDINFO]](https://bugzilla.redhat.com/show_bug.cgi?id=1768635#c15)

```
gcc iscsi_socket.c -o iscsi_socket
```

```c
#include <sys/socket.h>
#include <sys/types.h>
#include <netinet/in.h>
#include <netdb.h>
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <unistd.h>
#include <errno.h>
#include <arpa/inet.h> 
#include <linux/netlink.h>

int main(int argc, char *argv[])
{
    int sockfd = 0;
    if((sockfd = socket(PF_NETLINK, SOCK_RAW, NETLINK_ISCSI)) < 0)
    {
        printf("\n Error : Could not create socket \n");
        return 1;
    } 
    return 0;
}
```
