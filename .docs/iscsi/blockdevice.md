# :roll_of_paper: Le périphérique en mode bloc (Block Device)

Les périphériques en mode bloc réferent souvant aux disques durs dont la lecture se fait en bloc, généralement de 512 octets (bytes)

## :ab: Information sur le périphérique 

`Udev` est le gestionnaire de périphérique du noyau `Linux`. Dynamiquement, Udev crée ou enlève des noeuds de fichiers périphériques au démarrage en fonction du répertoire `/dev` pour tous types de périphériques. `Udev` fait maintenant partie de `systemd` et peut être controllé par `$ systemctl status systemd-udevd`.

- [ ] Pour avoir des informations sur un périphérique: (i.e `/dev/ubuntu-vg/iscsi-lv`)

```
udevadm info --query property --name /dev/ubuntu-vg/iscsi-lv
```
> Retourne
```
DEVPATH=/devices/virtual/block/dm-0
DEVNAME=/dev/dm-0
DEVTYPE=disk
DISKSEQ=11
MAJOR=253
MINOR=0
SUBSYSTEM=block
USEC_INITIALIZED=44911158436
DM_UDEV_DISABLE_LIBRARY_FALLBACK_FLAG=1
DM_UDEV_PRIMARY_SOURCE_FLAG=1
DM_UDEV_RULES=1
DM_UDEV_RULES_VSN=2
DM_NAME=ubuntu--vg-iscsi--lv
DM_UUID=LVM-8DrvdiI76BnJYD0yMtTcmKhHMTZuQcKwfuq1VmUbNb4cHjcjYq7NcV7uaGXue40l
DM_SUSPENDED=0
DM_VG_NAME=ubuntu-vg
DM_LV_NAME=iscsi-lv
DEVLINKS=/dev/disk/by-id/dm-uuid-LVM-8DrvdiI76BnJYD0yMtTcmKhHMTZuQcKwfuq1VmUbNb4cHjcjYq7NcV7uaGXue40l /dev/ubuntu-vg/iscsi-lv /dev/mapper/ubuntu--vg-iscsi--lv /dev/disk/by-id/dm-name-ubuntu--vg-iscsi--lv
TAGS=:systemd:
CURRENT_TAGS=:systemd:
```

- [ ] pour avoir la taille d'un périphérique

```
sudo fdisk --list /dev/mapper/ubuntu--vg-iscsi--lv
```
> Retourne
```
Disk /dev/mapper/ubuntu--vg-iscsi--lv: 40 GiB, 42949672960 bytes, 83886080 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

- [ ] pour avoir son :id: ou encore son **PARTUUID**

```
echo "blockdevice-"`sudo blkid --match-tag PARTUUID --output value /dev/sda1`
```
> Retourne
```
blockdevice-c3b2e1a8-85b0-fc4e-8caf-5ba8c2fd4444
```

- [ ] pour enfin avoir le nom du noeud

```
uname --nodename
```
> Retourne
```
nunki
```

:building_construction: Ces informations sont requises pour créer le fichier de configuration `yaml` utilisé pour créer le périphérique en mode bloc `block device`. 3 périphériques `(block devices)` sont requis pour former un réservoir `(pool)` de stockage géré par un réseau `iSCSI`

| TAG | Valeur |
|--------------------------------------------|------------------------------------|
| `{metadata.name}`                          | blockdevice-**PARTUUID**           |
| `{metadata.labels.kubernetes.io/hostname}` | **uname --nodename**               |
| `{spec.capacity.storage}`                  | **fdisk --list (taille en octets -- bytes)** |
| `{spec.devlinks.kind[by-id].links}`        | **udevadm info** - DEVLINKS /dev/disk/**by-id(1)**  |
|                                            | **udevadm info**- DEVLINKS /dev/disk/**by-id(2)**  |
| `{spec.devlinks.kind[by-path].links}`      | **udevadm info**- DEVLINKS /dev/**mapper(1)**      |
| `{spec.nodeAttributes.nodeName]`           | **uname --nodename**               |
| `{spec.path}`                              | **udevadm info**- DEVNAME |

:star: Le fichier se présente comme ceci:

```yaml
apiVersion: openebs.io/v1alpha1
kind: BlockDevice
metadata:
  name: blockdevice-c3b2e1a8-85b0-fc4e-8caf-5ba8c2fd4444
  labels:
    kubernetes.io/hostname: nunki
    ndm.io/managed: "false"
    ndm.io/blockdevice-type: blockdevice
status:
  claimState: Unclaimed
  state: Active
spec:
  partitioned: "No"
  capacity:
    logicalSectorSize: 512
    storage: 42949672960
  details:
    deviceType: lvm
  devlinks:
  - kind: by-id
    links:
    - /dev/disk/by-id/dm-uuid-LVM-8DrvdiI76BnJYD0yMtTcmKhHMTZuQcKwfuq1VmUbNb4cHjcjYq7NcV7uaGXue40l
    - /dev/disk/by-id/dm-name-ubuntu--vg-iscsi--lv
  - kind: by-path
    links:
    - /dev/mapper/ubuntu--vg-iscsi--lv
  nodeAttributes:
    nodeName: nunki
  path: /dev/dm-0
---
```

## [:back:](../#roll_of_paper-le-périphérique-block-device)


# References

- [ ] [BlockDevice CR creation for partitioned disks](https://mdap.zendesk.com/hc/en-us/articles/360033465571-BlockDevice-CR-creation-for-partitioned-disks)

https://openebs.io/blog/creating-manual-blockdevice/

 - [ ] [Create blockdevice CRs for unsupported disks](https://openebs.io/docs/user-guides/ndm#create-blockdevice-crs-for-unsupported-disks)

 
```yaml
 apiVersion: openebs.io/v1alpha1
 kind: BlockDevice
 metadata:
   name: example-blockdevice-1
   labels:
     kubernetes.io/hostname: <host name in which disk/blockdevice is attached> # like gke-openebs-user-default-pool-044afcb8-bmc0
     ndm.io/managed: "false" # for manual blockdevice creation put false
     ndm.io/blockdevice-type: blockdevice
 status:
   claimState: Unclaimed
   state: Active
 spec:
   capacity:
     logicalSectorSize: 512
     storage: <total capacity in bytes> #like 53687091200
   details:
     deviceType: <device type> # like disk, partition, lvm, crypt, md
     firmwareRevision: <firmware revision>
     model: <model name of blockdevice> # like PersistentDisk
     serial: <serial no of disk> # like google-disk-2
     compliance: <compliance of disk> #like "SPC-4"
     vendor: <vendor of disk> #like Google
   devlinks:
   - kind: by-id
     links:
     - <link1> # like /dev/disk/by-id/scsi-0Google_PersistentDisk_disk-2
     - <link2> # like /dev/disk/by-id/google-disk-2
   - kind: by-path
     links:
     - <link1> # like /dev/disk/by-path/virtio-pci-0000:00:03.0-scsi-0:0:2:0
   nodeAttributes:
     nodeName: <node name> # output of `kubectl get nodes` can be used
   path: <devpath> # like /dev/md0
```

 # References

- [ ] [Beginners Guide to Udev in Linux](https://www.thegeekdiary.com/beginners-guide-to-udev-in-linux)
- [ ] [RAID vs LVM vs ZFS Comparison](https://computingforgeeks.com/raid-vs-lvm-vs-zfs-comparison/)
- [ ] [A hands on demo of Volume Populator using OpenEBS LVM CSI driver](https://openebs.io/blog/a-hands-on-demo-of-volume-populator-using-openebs-lvm-csi-driver)
 - [ ] [OpenEBS - Node Disk Manager](https://openebs.io/docs/concepts/ndm)
    - [ ] [Node Disk Manager User Guide](https://openebs.io/docs/user-guides/ndm)
    - [ ] [Create blockdevice CRs for unsupported disks](https://openebs.io/docs/user-guides/ndm#create-blockdevice-crs-for-unsupported-disks)
    - [ ] [Disk Reservation: How OpenEBS cStor uses this feature from Node Disk Manager](https://medium.com/@sonasingh46/disk-reservation-how-openebs-cstor-uses-this-feature-from-node-disk-manager-d4ff4f96b5d7)
