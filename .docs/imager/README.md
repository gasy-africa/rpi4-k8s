# Raspberry Pi Imager

## :a: Install Raspberry Pi Imager

```
brew install --cask raspberry-pi-imager
```

## :b: Create a MicroSD Boot Drive

- [ ] Instart a [MicroSD](https://en.wikipedia.org/wiki/SD_card#:~:text=slot,microSD) Card (at least 16Gb of content) 

- [ ] `Burn` the Boot Drive

- Start [Raspberry Pi Imager](https://github.com/raspberrypi/rpi-imager) and click on `CHOOSE OS`

<img src=images/rpi-imager-01.png width=50% height=50% > <img>

- Select :computer: `Other general-purpose OS` 

<img src=images/rpi-imager-02.png width=50% height=50% > <img>

- Select `Ubuntu`

<img src=images/rpi-imager-03.png width=50% height=50% > <img>

- Select `Ubuntu Server 22.04.03 LTS (64 bits)` :information_source:

<img src=images/rpi-imager-04.png width=50% height=50% > <img>

- Click on the `CHOOSE STO...` Button

<img src=images/rpi-imager-05.png width=50% height=50% > <img>

- Select the :trident: `...Storage Media...`

<img src=images/rpi-imager-06.png width=50% height=50% > <img>
<img src=images/rpi-imager-07.png width=50% height=50% > <img>
<img src=images/rpi-imager-08.png width=50% height=50% > <img>
<img src=images/rpi-imager-09.png width=50% height=50% > <img>
<img src=images/rpi-imager-10.png width=50% height=50% > <img>
<img src=images/rpi-imager-11.png width=50% height=50% > <img>
<img src=images/rpi-imager-12.png width=50% height=50% > <img>
<img src=images/rpi-imager-13.png width=50% height=50% > <img>


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
               - 192.168.1.200/24
            routes:
               - to: default
                 via: 192.168.1.1
            nameservers:
               addresses:
                  - 1.1.1.1
                  - 8.8.8.8
            dhcp4: false
            optional: false
    version: 2
```

```
sudo netplan apply
```
