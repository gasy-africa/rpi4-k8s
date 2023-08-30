# Imager


 <img src=images/rpi-imager-01.png width=50% height=50% > <img>
 <img src=images/rpi-imager-02.png width=50% height=50% > <img>
 <img src=images/rpi-imager-03.png width=50% height=50% > <img>
 <img src=images/rpi-imager-04.png width=50% height=50% > <img>

 ```
sudo vi /etc/netplan/50-cloud-init.yaml
```
> Edit:
```yaml
# This file is generated from information provided by the datasource.  Changes
# to it will not persist across an instance reboot.  To disable cloud-init's
# network configuration capabilities, write a file
# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
network:
    ethernets:
        eth0:
            addresses:
               - 192.168.2.200/24
            routes:
               - to: default
                 via: 192.168.2.1
            nameservers:
               addresses:
                  - 1.1.1.1
                  - 8.8.8.8
            dhcp4: false
            optional: false
    version: 2
```
