
# :cd: Volume Logique (lv)

:bulb: Si vous avez déjà fail l'activité [L.LVM](https://github.com/CollegeBoreal/Tutoriels/tree/main/O.OS/1.Linux/L.LVM), [:eye: vérifier](#eye-vérifier-si-le-volume-logique-a-été-créé) :writing_hand: seulement que le volume logique `iscsi-lv` a été créé 



## LVM (Logical Volume Managerr)

- [ ] Create a PV (Physical Volume)

```
sudo pvcreate /dev/sda1
```
> Physical volume "/dev/sda1" successfully created.

- [ ] Create a VG (Volume Group)

```
sudo vgcreate ubuntu-vg /dev/sda1
```
> Volume group "ubuntu-vg" successfully created

- [ ] Create a VG (Volume Group)

```
sudo lvcreate --name iscsi-lv --size 40G ubuntu-vg
```
>  Logical volume "iscsi-lv" created.

# :eye: Vérifier si le volume logique a été créé

```
lsblk /dev/sda --output NAME,SIZE,TYPE,FSSIZE,FSTYPE,FSUSED,FSUSE%,MOUNTPOINT 
```
> Returns :
```yaml
NAME                      SIZE TYPE FSSIZE FSTYPE      FSUSED FSUSE% MOUNTPOINT
sda                      57.7G disk                                  
└─sda1                   57.7G part        LVM2_member               
  └─ubuntu--vg-iscsi--lv   40G lvm        
```

## [:back:](../../#roll_of_paper-le-périphérique-block-device)

# References

- [ ] [How to Format and Mount a USB drive on Raspberry Pi](https://raspberrytips.com/format-mount-usb-drive)
- [ ] [Playing with Blocks - LVM](https://waldorf.waveform.org.uk/2021/playing-with-blocks-lvm.html)
