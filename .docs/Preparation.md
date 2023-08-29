## :zero: Node Preparation

- [ ] Make sure you have the right hostname (based on your cluster's naming)

```
hostnamectl --static set-hostname rukbat
```


## :one: Disabling the swap (otherwise Kubernetes won't work) 

[how-do-i-disable-swap?](https://askubuntu.com/questions/214805/how-do-i-disable-swap)

- [ ] Before

```
free --human
```
> Outputs :
<pre>
               total        used        free      shared  buff/cache   available
Mem:            62Gi       541Mi        61Gi       1.0Mi       500Mi        61Gi
Swap:          8.0Gi          0B       8.0Gi
</pre>

- [ ] swap off and disabled at boot in `/etc/fstab` file

```
sudo swapoff --all && sudo sed --regexp-extended --in-place '/\sswap\s/s/^#?/#/' /etc/fstab
```

- [ ] After

```
free --human
```
> Outputs :
<pre>
               total        used        free      shared  buff/cache   available
Mem:            62Gi       523Mi        61Gi       1.0Mi       501Mi        61Gi
Swap:             0B          0B          0B
</pre>

- [ ] Check `swap` in `/etc/fstab` is commented

```
cat /etc/fstab 
```
> Outputs :
```yaml
# /etc/fstab: static file system information.
...
#/swap.img	none	swap	sw	0	0
```

## :two: [Letting iptables see bridged traffic](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#letting-iptables-see-bridged-traffic)

[sysctl](https://wiki.archlinux.org/index.php/sysctl) is a tool for examining and changing kernel parameters at runtime . sysctl is implemented in `procfs`, the virtual process file system at `/proc/`.

for more details, see [sysctl-cluster](https://kubernetes.io/docs/tasks/administer-cluster/sysctl-cluster) in Kubernetes

:round_pushpin: Enable the usage of iptables. To prevent some routing errors happening, add runtime parameters:

- [ ] Enable the parameters by setting them to 1 for both IPv6 and IPv4

##### :gear: k8s.conf [drop-in file](https://stackoverflow.com/questions/59842743/what-is-a-drop-in-file-what-is-a-drop-in-directory-how-to-edit-systemd-service) (i.e. sysctl .conf file)

```
sudo bash -c 'echo "net.bridge.bridge-nf-call-ip6tables = 1" > /etc/sysctl.d/k8s.conf' && \
sudo bash -c 'echo "net.bridge.bridge-nf-call-iptables  = 1" >> /etc/sysctl.d/k8s.conf'
```

- [ ] reload the configuration

```
sudo sysctl --system
```
> Outputs :
```yaml
* Applying /etc/sysctl.d/10-console-messages.conf ...
kernel.printk = 4 4 1 7
* Applying /etc/sysctl.d/10-ipv6-privacy.conf ...
net.ipv6.conf.all.use_tempaddr = 2
net.ipv6.conf.default.use_tempaddr = 2
* Applying /etc/sysctl.d/10-kernel-hardening.conf ...
kernel.kptr_restrict = 1
* Applying /etc/sysctl.d/10-magic-sysrq.conf ...
kernel.sysrq = 176
* Applying /etc/sysctl.d/10-network-security.conf ...
net.ipv4.conf.default.rp_filter = 2
net.ipv4.conf.all.rp_filter = 2
* Applying /etc/sysctl.d/10-ptrace.conf ...
kernel.yama.ptrace_scope = 1
* Applying /etc/sysctl.d/10-zeropage.conf ...
vm.mmap_min_addr = 65536
* Applying /usr/lib/sysctl.d/50-default.conf ...
kernel.core_uses_pid = 1
net.ipv4.conf.default.rp_filter = 2
net.ipv4.conf.default.accept_source_route = 0
sysctl: setting key "net.ipv4.conf.all.accept_source_route": Invalid argument
net.ipv4.conf.default.promote_secondaries = 1
sysctl: setting key "net.ipv4.conf.all.promote_secondaries": Invalid argument
net.ipv4.ping_group_range = 0 2147483647
net.core.default_qdisc = fq_codel
fs.protected_hardlinks = 1
fs.protected_symlinks = 1
fs.protected_regular = 1
fs.protected_fifos = 1
* Applying /usr/lib/sysctl.d/50-pid-max.conf ...
kernel.pid_max = 4194304
* Applying /usr/lib/sysctl.d/99-protect-links.conf ...
fs.protected_fifos = 1
fs.protected_hardlinks = 1
fs.protected_regular = 2
fs.protected_symlinks = 1
* Applying /etc/sysctl.d/99-sysctl.conf ...
* Applying /etc/sysctl.d/k8s.conf ...
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
* Applying /etc/sysctl.conf ...
```

:three: Extra Modules

* Install missing library (Ubuntu 22.04) i.e. needed for Raspberry Pi

Used by:
- [ ] CNI Cilium
- [ ] iSCSI 

```
sudo apt update && sudo apt install linux-modules-extra-raspi
```
> Returns :
```yaml
Package: linux-modules-extra-raspi
Version: 5.15.0.1029.26
Priority: optional
Section: kernel
Source: linux-meta-raspi
Origin: Ubuntu
Maintainer: Ubuntu Kernel Team <kernel-team@lists.ubuntu.com>
Bugs: https://bugs.launchpad.net/ubuntu/+filebug
Installed-Size: 14.3 kB
Depends: linux-modules-extra-5.15.0-1029-raspi
Download-Size: 2420 B
APT-Sources: http://ports.ubuntu.com/ubuntu-ports jammy-updates/main arm64 Packages
Description: Raspberry Pi Linux kernel extra modules
 This package will always depend on the latest Raspberry Pi Linux
 kernel extra modules package available.

N: There is 1 additional record. Please use the '-a' switch to see it
ubuntu@alnasl:~$ sudo apt install linux-modules-extra-raspi
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  linux-modules-extra-5.15.0-1029-raspi
The following NEW packages will be installed:
  linux-modules-extra-5.15.0-1029-raspi linux-modules-extra-raspi
0 upgraded, 2 newly installed, 0 to remove and 2 not upgraded.
Need to get 24.6 MB of archives.
After this operation, 122 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main arm64 linux-modules-extra-5.15.0-1029-raspi arm64 5.15.0-1029.31 [24.6 MB]
Get:2 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main arm64 linux-modules-extra-raspi arm64 5.15.0.1029.26 [2420 B]
Fetched 24.6 MB in 2s (9961 kB/s)                   
Selecting previously unselected package linux-modules-extra-5.15.0-1029-raspi.
(Reading database ... 101026 files and directories currently installed.)
Preparing to unpack .../linux-modules-extra-5.15.0-1029-raspi_5.15.0-1029.31_arm64.deb ...
Unpacking linux-modules-extra-5.15.0-1029-raspi (5.15.0-1029.31) ...
Selecting previously unselected package linux-modules-extra-raspi.
Preparing to unpack .../linux-modules-extra-raspi_5.15.0.1029.26_arm64.deb ...
Unpacking linux-modules-extra-raspi (5.15.0.1029.26) ...
Setting up linux-modules-extra-5.15.0-1029-raspi (5.15.0-1029.31) ...
Setting up linux-modules-extra-raspi (5.15.0.1029.26) ...
Processing triggers for linux-image-5.15.0-1029-raspi (5.15.0-1029.31) ...
/etc/kernel/postinst.d/initramfs-tools:
update-initramfs: Generating /boot/initrd.img-5.15.0-1029-raspi
Using DTB: bcm2711-rpi-4-b.dtb
Installing /lib/firmware/5.15.0-1029-raspi/device-tree/broadcom/bcm2711-rpi-4-b.dtb into /boot/dtbs/5.15.0-1029-raspi/./bcm2711-rpi-4-b.dtb
Taking backup of bcm2711-rpi-4-b.dtb.
Installing new bcm2711-rpi-4-b.dtb.
Installing /lib/firmware/5.15.0-1029-raspi/device-tree/broadcom/bcm2711-rpi-4-b.dtb into /boot/dtbs/5.15.0-1029-raspi/./bcm2711-rpi-4-b.dtb
Taking backup of bcm2711-rpi-4-b.dtb.
Installing new bcm2711-rpi-4-b.dtb.
flash-kernel: deferring update (trigger activated)
/etc/kernel/postinst.d/zz-flash-kernel:
Using DTB: bcm2711-rpi-4-b.dtb
Installing /lib/firmware/5.15.0-1029-raspi/device-tree/broadcom/bcm2711-rpi-4-b.dtb into /boot/dtbs/5.15.0-1029-raspi/./bcm2711-rpi-4-b.dtb
Taking backup of bcm2711-rpi-4-b.dtb.
Installing new bcm2711-rpi-4-b.dtb.
Installing /lib/firmware/5.15.0-1029-raspi/device-tree/broadcom/bcm2711-rpi-4-b.dtb into /boot/dtbs/5.15.0-1029-raspi/./bcm2711-rpi-4-b.dtb
Taking backup of bcm2711-rpi-4-b.dtb.
Installing new bcm2711-rpi-4-b.dtb.
flash-kernel: deferring update (trigger activated)
Processing triggers for flash-kernel (3.104ubuntu16) ...
Using DTB: bcm2711-rpi-4-b.dtb
Installing /lib/firmware/5.15.0-1029-raspi/device-tree/broadcom/bcm2711-rpi-4-b.dtb into /boot/dtbs/5.15.0-1029-raspi/./bcm2711-rpi-4-b.dtb
Taking backup of bcm2711-rpi-4-b.dtb.
Installing new bcm2711-rpi-4-b.dtb.
flash-kernel: installing version 5.15.0-1029-raspi
Taking backup of vmlinuz.
Installing new vmlinuz.
Taking backup of initrd.img.
Installing new initrd.img.
Taking backup of uboot_rpi_3.bin.
Installing new uboot_rpi_3.bin.
Taking backup of uboot_rpi_4.bin.
Installing new uboot_rpi_4.bin.
Taking backup of uboot_rpi_arm64.bin.
Installing new uboot_rpi_arm64.bin.
Generating boot script u-boot image... done.
Taking backup of boot.scr.
Installing new boot.scr.
Taking backup of start4.elf.
Installing new start4.elf.
Taking backup of fixup4db.dat.
Installing new fixup4db.dat.
Taking backup of start.elf.
Installing new start.elf.
Taking backup of start4x.elf.
Installing new start4x.elf.
Taking backup of start4db.elf.
Installing new start4db.elf.
Taking backup of fixup_x.dat.
Installing new fixup_x.dat.
Taking backup of start4cd.elf.
Installing new start4cd.elf.
Taking backup of fixup4cd.dat.
Installing new fixup4cd.dat.
Taking backup of fixup_db.dat.
Installing new fixup_db.dat.
Taking backup of fixup.dat.
Installing new fixup.dat.
Taking backup of bootcode.bin.
Installing new bootcode.bin.
Taking backup of fixup4x.dat.
Installing new fixup4x.dat.
Taking backup of fixup4.dat.
Installing new fixup4.dat.
Taking backup of start_db.elf.
Installing new start_db.elf.
Taking backup of start_x.elf.
Installing new start_x.elf.
Taking backup of start_cd.elf.
Installing new start_cd.elf.
Taking backup of fixup_cd.dat.
Installing new fixup_cd.dat.
Taking backup of bcm2710-rpi-3-b.dtb.
Installing new bcm2710-rpi-3-b.dtb.
Taking backup of bcm2711-rpi-400.dtb.
Installing new bcm2711-rpi-400.dtb.
Taking backup of bcm2710-rpi-cm3.dtb.
Installing new bcm2710-rpi-cm3.dtb.
Taking backup of bcm2711-rpi-cm4.dtb.
Installing new bcm2711-rpi-cm4.dtb.
Taking backup of bcm2837-rpi-3-b-plus.dtb.
Installing new bcm2837-rpi-3-b-plus.dtb.
Taking backup of bcm2837-rpi-cm3-io3.dtb.
Installing new bcm2837-rpi-cm3-io3.dtb.
Taking backup of bcm2837-rpi-3-a-plus.dtb.
Installing new bcm2837-rpi-3-a-plus.dtb.
Taking backup of bcm2837-rpi-3-b.dtb.
Installing new bcm2837-rpi-3-b.dtb.
Taking backup of bcm2711-rpi-4-b.dtb.
Installing new bcm2711-rpi-4-b.dtb.
Taking backup of bcm2710-rpi-2-b.dtb.
Installing new bcm2710-rpi-2-b.dtb.
Taking backup of bcm2710-rpi-zero-2.dtb.
Installing new bcm2710-rpi-zero-2.dtb.
Taking backup of bcm2710-rpi-3-b-plus.dtb.
Installing new bcm2710-rpi-3-b-plus.dtb.
Taking backup of i-sabre-q2m.dtbo.
Installing new i-sabre-q2m.dtbo.
Taking backup of waveshare-can-fd-hat-mode-a.dtbo.
Installing new waveshare-can-fd-hat-mode-a.dtbo.
Taking backup of ads7846.dtbo.
Installing new ads7846.dtbo.
Taking backup of jedec-spi-nor.dtbo.
Installing new jedec-spi-nor.dtbo.
Taking backup of highperi.dtbo.
Installing new highperi.dtbo.
Taking backup of audioinjector-bare-i2s.dtbo.
Installing new audioinjector-bare-i2s.dtbo.
Taking backup of dwc-otg.dtbo.
Installing new dwc-otg.dtbo.
Taking backup of ov5647.dtbo.
Installing new ov5647.dtbo.
Taking backup of uart2.dtbo.
Installing new uart2.dtbo.
Taking backup of pitft22.dtbo.
Installing new pitft22.dtbo.
Taking backup of midi-uart4.dtbo.
Installing new midi-uart4.dtbo.
Taking backup of vc4-kms-dpi-hyperpixel4.dtbo.
Installing new vc4-kms-dpi-hyperpixel4.dtbo.
Taking backup of spi2-3cs.dtbo.
Installing new spi2-3cs.dtbo.
Taking backup of ov2311.dtbo.
Installing new ov2311.dtbo.
Taking backup of adafruit-st7735r.dtbo.
Installing new adafruit-st7735r.dtbo.
Taking backup of media-center.dtbo.
Installing new media-center.dtbo.
Taking backup of spi5-2cs.dtbo.
Installing new spi5-2cs.dtbo.
Taking backup of i2c-mux.dtbo.
Installing new i2c-mux.dtbo.
Taking backup of adv7282m.dtbo.
Installing new adv7282m.dtbo.
Taking backup of vc4-kms-dsi-lt070me05000.dtbo.
Installing new vc4-kms-dsi-lt070me05000.dtbo.
Taking backup of rpi-cirrus-wm5102.dtbo.
Installing new rpi-cirrus-wm5102.dtbo.
Taking backup of ssd1306-spi.dtbo.
Installing new ssd1306-spi.dtbo.
Taking backup of spi2-2cs.dtbo.
Installing new spi2-2cs.dtbo.
Taking backup of ov9281.dtbo.
Installing new ov9281.dtbo.
Taking backup of balena-fin.dtbo.
Installing new balena-fin.dtbo.
Taking backup of dionaudio-loco-v2.dtbo.
Installing new dionaudio-loco-v2.dtbo.
Taking backup of adau7002-simple.dtbo.
Installing new adau7002-simple.dtbo.
Taking backup of mcp2515.dtbo.
Installing new mcp2515.dtbo.
Taking backup of vc4-kms-v3d-pi4.dtbo.
Installing new vc4-kms-v3d-pi4.dtbo.
Taking backup of pps-gpio.dtbo.
Installing new pps-gpio.dtbo.
Taking backup of vga666.dtbo.
Installing new vga666.dtbo.
Taking backup of i2c3.dtbo.
Installing new i2c3.dtbo.
Taking backup of sc16is752-i2c.dtbo.
Installing new sc16is752-i2c.dtbo.
Taking backup of piscreen.dtbo.
Installing new piscreen.dtbo.
Taking backup of dpi24.dtbo.
Installing new dpi24.dtbo.
Taking backup of arducam-pivariety.dtbo.
Installing new arducam-pivariety.dtbo.
Taking backup of cma.dtbo.
Installing new cma.dtbo.
Taking backup of udrc.dtbo.
Installing new udrc.dtbo.
Taking backup of gpio-no-irq.dtbo.
Installing new gpio-no-irq.dtbo.
Taking backup of imx378.dtbo.
Installing new imx378.dtbo.
Taking backup of goodix.dtbo.
Installing new goodix.dtbo.
Taking backup of dpi18cpadhi.dtbo.
Installing new dpi18cpadhi.dtbo.
Taking backup of piglow.dtbo.
Installing new piglow.dtbo.
Taking backup of mcp342x.dtbo.
Installing new mcp342x.dtbo.
Taking backup of pibell.dtbo.
Installing new pibell.dtbo.
Taking backup of audremap.dtbo.
Installing new audremap.dtbo.
Taking backup of allo-katana-dac-audio.dtbo.
Installing new allo-katana-dac-audio.dtbo.
Taking backup of imx258.dtbo.
Installing new imx258.dtbo.
Taking backup of draws.dtbo.
Installing new draws.dtbo.
Taking backup of allo-piano-dac-pcm512x-audio.dtbo.
Installing new allo-piano-dac-pcm512x-audio.dtbo.
Taking backup of i2c-gpio.dtbo.
Installing new i2c-gpio.dtbo.
Taking backup of gpio-key.dtbo.
Installing new gpio-key.dtbo.
Taking backup of imx219.dtbo.
Installing new imx219.dtbo.
Taking backup of rpi-ft5406.dtbo.
Installing new rpi-ft5406.dtbo.
Taking backup of hifiberry-digi.dtbo.
Installing new hifiberry-digi.dtbo.
Taking backup of pitft35-resistive.dtbo.
Installing new pitft35-resistive.dtbo.
Taking backup of disable-bt.dtbo.
Installing new disable-bt.dtbo.
Taking backup of midi-uart3.dtbo.
Installing new midi-uart3.dtbo.
Taking backup of uart1.dtbo.
Installing new uart1.dtbo.
Taking backup of vl805.dtbo.
Installing new vl805.dtbo.
Taking backup of pifi-mini-210.dtbo.
Installing new pifi-mini-210.dtbo.
Taking backup of i2c-rtc.dtbo.
Installing new i2c-rtc.dtbo.
Taking backup of imx519.dtbo.
Installing new imx519.dtbo.
Taking backup of rpi-poe-plus.dtbo.
Installing new rpi-poe-plus.dtbo.
Taking backup of vc4-kms-dsi-7inch.dtbo.
Installing new vc4-kms-dsi-7inch.dtbo.
Taking backup of at86rf233.dtbo.
Installing new at86rf233.dtbo.
Taking backup of exc3000.dtbo.
Installing new exc3000.dtbo.
Taking backup of edt-ft5406.dtbo.
Installing new edt-ft5406.dtbo.
Taking backup of rpi-display.dtbo.
Installing new rpi-display.dtbo.
Taking backup of mlx90640.dtbo.
Installing new mlx90640.dtbo.
Taking backup of hy28b.dtbo.
Installing new hy28b.dtbo.
Taking backup of papirus.dtbo.
Installing new papirus.dtbo.
Taking backup of iqaudio-digi-wm8804-audio.dtbo.
Installing new iqaudio-digi-wm8804-audio.dtbo.
Taking backup of hifiberry-dacplushd.dtbo.
Installing new hifiberry-dacplushd.dtbo.
Taking backup of googlevoicehat-soundcard.dtbo.
Installing new googlevoicehat-soundcard.dtbo.
Taking backup of spi-gpio40-45.dtbo.
Installing new spi-gpio40-45.dtbo.
Taking backup of gpio-shutdown.dtbo.
Installing new gpio-shutdown.dtbo.
Taking backup of seeed-can-fd-hat-v2.dtbo.
Installing new seeed-can-fd-hat-v2.dtbo.
Taking backup of akkordion-iqdacplus.dtbo.
Installing new akkordion-iqdacplus.dtbo.
Taking backup of vc4-kms-kippah-7inch.dtbo.
Installing new vc4-kms-kippah-7inch.dtbo.
Taking backup of sh1106-spi.dtbo.
Installing new sh1106-spi.dtbo.
Taking backup of mcp3202.dtbo.
Installing new mcp3202.dtbo.
Taking backup of spi0-0cs.dtbo.
Installing new spi0-0cs.dtbo.
Taking backup of adafruit18.dtbo.
Installing new adafruit18.dtbo.
Taking backup of mbed-dac.dtbo.
Installing new mbed-dac.dtbo.
Taking backup of wm8960-soundcard.dtbo.
Installing new wm8960-soundcard.dtbo.
Taking backup of vc4-fkms-v3d-pi4.dtbo.
Installing new vc4-fkms-v3d-pi4.dtbo.
Taking backup of spi6-2cs.dtbo.
Installing new spi6-2cs.dtbo.
Taking backup of imx477.dtbo.
Installing new imx477.dtbo.
Taking backup of ghost-amp.dtbo.
Installing new ghost-amp.dtbo.
Taking backup of pifi-dac-zero.dtbo.
Installing new pifi-dac-zero.dtbo.
Taking backup of iqs550.dtbo.
Installing new iqs550.dtbo.
Taking backup of spi3-2cs.dtbo.
Installing new spi3-2cs.dtbo.
Taking backup of sdhost.dtbo.
Installing new sdhost.dtbo.
Taking backup of tpm-slb9670.dtbo.
Installing new tpm-slb9670.dtbo.
Taking backup of spi6-1cs.dtbo.
Installing new spi6-1cs.dtbo.
Taking backup of hifiberry-dacplusadc.dtbo.
Installing new hifiberry-dacplusadc.dtbo.
Taking backup of pcie-32bit-dma.dtbo.
Installing new pcie-32bit-dma.dtbo.
Taking backup of spi0-2cs.dtbo.
Installing new spi0-2cs.dtbo.
Taking backup of spi3-1cs.dtbo.
Installing new spi3-1cs.dtbo.
Taking backup of smi-nand.dtbo.
Installing new smi-nand.dtbo.
Taking backup of pisound.dtbo.
Installing new pisound.dtbo.
Taking backup of pitft28-resistive.dtbo.
Installing new pitft28-resistive.dtbo.
Taking backup of justboom-digi.dtbo.
Installing new justboom-digi.dtbo.
Taking backup of dwc2.dtbo.
Installing new dwc2.dtbo.
Taking backup of spi0-1cs.dtbo.
Installing new spi0-1cs.dtbo.
Taking backup of gpio-ir.dtbo.
Installing new gpio-ir.dtbo.
Taking backup of ltc294x.dtbo.
Installing new ltc294x.dtbo.
Taking backup of uart5.dtbo.
Installing new uart5.dtbo.
Taking backup of hifiberry-amp100.dtbo.
Installing new hifiberry-amp100.dtbo.
Taking backup of hifiberry-dacplusadcpro.dtbo.
Installing new hifiberry-dacplusadcpro.dtbo.
Taking backup of ssd1351-spi.dtbo.
Installing new ssd1351-spi.dtbo.
Taking backup of mcp251xfd.dtbo.
Installing new mcp251xfd.dtbo.
Taking backup of sc16is750-i2c.dtbo.
Installing new sc16is750-i2c.dtbo.
Taking backup of tc358743.dtbo.
Installing new tc358743.dtbo.
Taking backup of uart0.dtbo.
Installing new uart0.dtbo.
Taking backup of midi-uart2.dtbo.
Installing new midi-uart2.dtbo.
Taking backup of apds9960.dtbo.
Installing new apds9960.dtbo.
Taking backup of allo-piano-dac-plus-pcm512x-audio.dtbo.
Installing new allo-piano-dac-plus-pcm512x-audio.dtbo.
Taking backup of pwm.dtbo.
Installing new pwm.dtbo.
Taking backup of sdio.dtbo.
Installing new sdio.dtbo.
Taking backup of disable-wifi.dtbo.
Installing new disable-wifi.dtbo.
Taking backup of applepi-dac.dtbo.
Installing new applepi-dac.dtbo.
Taking backup of mcp23s17.dtbo.
Installing new mcp23s17.dtbo.
Taking backup of vc4-kms-dpi-panel.dtbo.
Installing new vc4-kms-dpi-panel.dtbo.
Taking backup of rotary-encoder.dtbo.
Installing new rotary-encoder.dtbo.
Taking backup of vc4-kms-dpi-hyperpixel4sq.dtbo.
Installing new vc4-kms-dpi-hyperpixel4sq.dtbo.
Taking backup of fbtft.dtbo.
Installing new fbtft.dtbo.
Taking backup of qca7000-uart0.dtbo.
Installing new qca7000-uart0.dtbo.
Taking backup of hifiberry-dacplusdsp.dtbo.
Installing new hifiberry-dacplusdsp.dtbo.
Taking backup of mipi-dbi-spi.dtbo.
Installing new mipi-dbi-spi.dtbo.
Taking backup of imx296.dtbo.
Installing new imx296.dtbo.
Taking backup of hy28a.dtbo.
Installing new hy28a.dtbo.
Taking backup of i2c-pwm-pca9685a.dtbo.
Installing new i2c-pwm-pca9685a.dtbo.
Taking backup of i2c6.dtbo.
Installing new i2c6.dtbo.
Taking backup of audioinjector-ultra.dtbo.
Installing new audioinjector-ultra.dtbo.
Taking backup of seeed-can-fd-hat-v1.dtbo.
Installing new seeed-can-fd-hat-v1.dtbo.
Taking backup of enc28j60-spi2.dtbo.
Installing new enc28j60-spi2.dtbo.
Taking backup of spi-gpio35-39.dtbo.
Installing new spi-gpio35-39.dtbo.
Taking backup of i2c1.dtbo.
Installing new i2c1.dtbo.
Taking backup of smi-dev.dtbo.
Installing new smi-dev.dtbo.
Taking backup of justboom-dac.dtbo.
Installing new justboom-dac.dtbo.
Taking backup of tinylcd35.dtbo.
Installing new tinylcd35.dtbo.
Taking backup of superaudioboard.dtbo.
Installing new superaudioboard.dtbo.
Taking backup of rpi-dac.dtbo.
Installing new rpi-dac.dtbo.
Taking backup of mcp23017.dtbo.
Installing new mcp23017.dtbo.
Taking backup of sx150x.dtbo.
Installing new sx150x.dtbo.
Taking backup of sc16is752-spi1.dtbo.
Installing new sc16is752-spi1.dtbo.
Taking backup of vc4-kms-vga666.dtbo.
Installing new vc4-kms-vga666.dtbo.
Taking backup of hifiberry-amp.dtbo.
Installing new hifiberry-amp.dtbo.
Taking backup of upstream-pi4.dtbo.
Installing new upstream-pi4.dtbo.
Taking backup of tc358743-audio.dtbo.
Installing new tc358743-audio.dtbo.
Taking backup of piscreen2r.dtbo.
Installing new piscreen2r.dtbo.
Taking backup of overlay_map.dtb.
Installing new overlay_map.dtb.
Taking backup of gpio-poweroff.dtbo.
Installing new gpio-poweroff.dtbo.
Taking backup of vc4-kms-dpi-hyperpixel2r.dtbo.
Installing new vc4-kms-dpi-hyperpixel2r.dtbo.
Taking backup of dionaudio-loco.dtbo.
Installing new dionaudio-loco.dtbo.
Taking backup of ilitek251x.dtbo.
Installing new ilitek251x.dtbo.
Taking backup of allo-digione.dtbo.
Installing new allo-digione.dtbo.
Taking backup of i2c-rtc-gpio.dtbo.
Installing new i2c-rtc-gpio.dtbo.
Taking backup of hy28b-2017.dtbo.
Installing new hy28b-2017.dtbo.
Taking backup of qca7000.dtbo.
Installing new qca7000.dtbo.
Taking backup of gpio-fan.dtbo.
Installing new gpio-fan.dtbo.
Taking backup of miniuart-bt.dtbo.
Installing new miniuart-bt.dtbo.
Taking backup of uart4.dtbo.
Installing new uart4.dtbo.
Taking backup of pwm-2chan.dtbo.
Installing new pwm-2chan.dtbo.
Taking backup of spi4-2cs.dtbo.
Installing new spi4-2cs.dtbo.
Taking backup of ssd1331-spi.dtbo.
Installing new ssd1331-spi.dtbo.
Taking backup of vc4-kms-dpi-generic.dtbo.
Installing new vc4-kms-dpi-generic.dtbo.
Taking backup of smi.dtbo.
Installing new smi.dtbo.
Taking backup of midi-uart1.dtbo.
Installing new midi-uart1.dtbo.
Taking backup of act-led.dtbo.
Installing new act-led.dtbo.
Taking backup of gpio-hog.dtbo.
Installing new gpio-hog.dtbo.
Taking backup of vc4-kms-dsi-lt070me05000-v2.dtbo.
Installing new vc4-kms-dsi-lt070me05000-v2.dtbo.
Taking backup of mcp2515-can1.dtbo.
Installing new mcp2515-can1.dtbo.
Taking backup of spi4-1cs.dtbo.
Installing new spi4-1cs.dtbo.
Taking backup of i2c-sensor.dtbo.
Installing new i2c-sensor.dtbo.
Taking backup of spi1-1cs.dtbo.
Installing new spi1-1cs.dtbo.
Taking backup of upstream.dtbo.
Installing new upstream.dtbo.
Taking backup of rpi-poe.dtbo.
Installing new rpi-poe.dtbo.
Taking backup of rpi-backlight.dtbo.
Installing new rpi-backlight.dtbo.
Taking backup of chipdip-dac.dtbo.
Installing new chipdip-dac.dtbo.
Taking backup of pwm-ir-tx.dtbo.
Installing new pwm-ir-tx.dtbo.
Taking backup of i2c5.dtbo.
Installing new i2c5.dtbo.
Taking backup of allo-boss2-dac-audio.dtbo.
Installing new allo-boss2-dac-audio.dtbo.
Taking backup of dht11.dtbo.
Installing new dht11.dtbo.
Taking backup of gpio-no-bank0-irq.dtbo.
Installing new gpio-no-bank0-irq.dtbo.
Taking backup of ov7251.dtbo.
Installing new ov7251.dtbo.
Taking backup of hifiberry-dacplus.dtbo.
Installing new hifiberry-dacplus.dtbo.
Taking backup of i2c0.dtbo.
Installing new i2c0.dtbo.
Taking backup of w1-gpio.dtbo.
Installing new w1-gpio.dtbo.
Taking backup of wittypi.dtbo.
Installing new wittypi.dtbo.
Taking backup of gpio-led.dtbo.
Installing new gpio-led.dtbo.
Taking backup of sainsmart18.dtbo.
Installing new sainsmart18.dtbo.
Taking backup of i2s-gpio28-31.dtbo.
Installing new i2s-gpio28-31.dtbo.
Taking backup of justboom-both.dtbo.
Installing new justboom-both.dtbo.
Taking backup of pifi-dac-hd.dtbo.
Installing new pifi-dac-hd.dtbo.
Taking backup of spi1-3cs.dtbo.
Installing new spi1-3cs.dtbo.
Taking backup of audioinjector-addons.dtbo.
Installing new audioinjector-addons.dtbo.
Taking backup of pitft28-capacitive.dtbo.
Installing new pitft28-capacitive.dtbo.
Taking backup of irs1125.dtbo.
Installing new irs1125.dtbo.
Taking backup of audioinjector-wm8731-audio.dtbo.
Installing new audioinjector-wm8731-audio.dtbo.
Taking backup of sc16is752-spi0.dtbo.
Installing new sc16is752-spi0.dtbo.
Taking backup of fe-pi-audio.dtbo.
Installing new fe-pi-audio.dtbo.
Taking backup of spi1-2cs.dtbo.
Installing new spi1-2cs.dtbo.
Taking backup of i2c-bcm2708.dtbo.
Installing new i2c-bcm2708.dtbo.
Taking backup of hdmi-backlight-hwhack-gpio.dtbo.
Installing new hdmi-backlight-hwhack-gpio.dtbo.
Taking backup of pifi-40.dtbo.
Installing new pifi-40.dtbo.
Taking backup of fsm-demo.dtbo.
Installing new fsm-demo.dtbo.
Taking backup of vc4-kms-v3d.dtbo.
Installing new vc4-kms-v3d.dtbo.
Taking backup of hd44780-lcd.dtbo.
Installing new hd44780-lcd.dtbo.
Taking backup of adau1977-adc.dtbo.
Installing new adau1977-adc.dtbo.
Taking backup of waveshare-can-fd-hat-mode-b.dtbo.
Installing new waveshare-can-fd-hat-mode-b.dtbo.
Taking backup of rra-digidac1-wm8741-audio.dtbo.
Installing new rra-digidac1-wm8741-audio.dtbo.
Taking backup of si446x-spi0.dtbo.
Installing new si446x-spi0.dtbo.
Taking backup of maxtherm.dtbo.
Installing new maxtherm.dtbo.
Taking backup of midi-uart5.dtbo.
Installing new midi-uart5.dtbo.
Taking backup of uart3.dtbo.
Installing new uart3.dtbo.
Taking backup of mpu6050.dtbo.
Installing new mpu6050.dtbo.
Taking backup of hifiberry-digi-pro.dtbo.
Installing new hifiberry-digi-pro.dtbo.
Taking backup of imx290.dtbo.
Installing new imx290.dtbo.
Taking backup of cutiepi-panel.dtbo.
Installing new cutiepi-panel.dtbo.
Taking backup of midi-uart0.dtbo.
Installing new midi-uart0.dtbo.
Taking backup of rpivid-v4l2.dtbo.
Installing new rpivid-v4l2.dtbo.
Taking backup of audioinjector-isolated-soundcard.dtbo.
Installing new audioinjector-isolated-soundcard.dtbo.
Taking backup of rpi-tv.dtbo.
Installing new rpi-tv.dtbo.
Taking backup of ugreen-dabboard.dtbo.
Installing new ugreen-dabboard.dtbo.
Taking backup of mcp2515-can0.dtbo.
Installing new mcp2515-can0.dtbo.
Taking backup of anyspi.dtbo.
Installing new anyspi.dtbo.
Taking backup of i2c-fan.dtbo.
Installing new i2c-fan.dtbo.
Taking backup of dpi18.dtbo.
Installing new dpi18.dtbo.
Taking backup of adv728x-m.dtbo.
Installing new adv728x-m.dtbo.
Taking backup of gpio-ir-tx.dtbo.
Installing new gpio-ir-tx.dtbo.
Taking backup of enc28j60.dtbo.
Installing new enc28j60.dtbo.
Taking backup of audiosense-pi.dtbo.
Installing new audiosense-pi.dtbo.
Taking backup of pifacedigital.dtbo.
Installing new pifacedigital.dtbo.
Taking backup of mcp3008.dtbo.
Installing new mcp3008.dtbo.
Taking backup of ads1115.dtbo.
Installing new ads1115.dtbo.
Taking backup of iqaudio-dac.dtbo.
Installing new iqaudio-dac.dtbo.
Taking backup of vc4-fkms-v3d.dtbo.
Installing new vc4-fkms-v3d.dtbo.
Taking backup of merus-amp.dtbo.
Installing new merus-amp.dtbo.
Taking backup of rpi-sense.dtbo.
Installing new rpi-sense.dtbo.
Taking backup of ssd1306.dtbo.
Installing new ssd1306.dtbo.
Taking backup of i2c4.dtbo.
Installing new i2c4.dtbo.
Taking backup of allo-boss-dac-pcm512x-audio.dtbo.
Installing new allo-boss-dac-pcm512x-audio.dtbo.
Taking backup of iqaudio-codec.dtbo.
Installing new iqaudio-codec.dtbo.
Taking backup of w1-gpio-pullup.dtbo.
Installing new w1-gpio-pullup.dtbo.
Taking backup of rpi-proto.dtbo.
Installing new rpi-proto.dtbo.
Taking backup of mmc.dtbo.
Installing new mmc.dtbo.
Taking backup of ads1015.dtbo.
Installing new ads1015.dtbo.
Taking backup of max98357a.dtbo.
Installing new max98357a.dtbo.
Taking backup of spi5-1cs.dtbo.
Installing new spi5-1cs.dtbo.
Taking backup of mz61581.dtbo.
Installing new mz61581.dtbo.
Taking backup of pca953x.dtbo.
Installing new pca953x.dtbo.
Taking backup of iqaudio-dacplus.dtbo.
Installing new iqaudio-dacplus.dtbo.
Taking backup of spi-rtc.dtbo.
Installing new spi-rtc.dtbo.
Taking backup of hifiberry-dac.dtbo.
Installing new hifiberry-dac.dtbo.
Taking backup of spi2-1cs.dtbo.
Installing new spi2-1cs.dtbo.
Taking backup of w5500.dtbo.
Installing new w5500.dtbo.
Taking backup of cap1106.dtbo.
Installing new cap1106.dtbo.
Taking backup of minipitft13.dtbo.
Installing new minipitft13.dtbo.
Taking backup of README.
Installing new README.
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


## [:back:](../README.md#round_pushpin-the-node)
