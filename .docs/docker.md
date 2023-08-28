# Docker :whale:


:x: Uninstall old versions

Older versions of Docker went by the names of `docker`, `docker.io`, or `docker-engine`, you might also have installations of `containerd` or `runc`. Uninstall any such older versions before attempting to install a new version:

```
sudo apt-get remove docker docker-engine docker.io containerd runc
```
> Returns :
```yaml
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
E: Unable to locate package docker-engine
```

## :gear: Installation methods

You can install Docker Engine in different ways, depending on your needs:


* Set up and install Docker Engine from [Docker’s `apt` repository](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository).

* Use a [convenience scripts](https://docs.docker.com/engine/install/ubuntu/#install-using-the-convenience-script). Only recommended for testing and development environments.

## :a: [Install using the apt repository](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)

:round_pushpin: Update the apt package index and install packages to allow apt to use a repository over HTTPS:

```
sudo apt-get update && sudo apt-get install ca-certificates curl gnupg
```
> Returns :
```yaml
Hit:1 http://ports.ubuntu.com/ubuntu-ports jammy InRelease
Hit:3 http://ports.ubuntu.com/ubuntu-ports jammy-updates InRelease
Hit:4 http://ports.ubuntu.com/ubuntu-ports jammy-backports InRelease
Hit:5 http://ports.ubuntu.com/ubuntu-ports jammy-security InRelease              
Hit:2 https://packages.cloud.google.com/apt kubernetes-xenial InRelease          
Reading package lists... Done                              
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
ca-certificates is already the newest version (20211016ubuntu0.22.04.1).
curl is already the newest version (7.81.0-1ubuntu1.10).
gnupg is already the newest version (2.2.27-3ubuntu2.1).
gnupg set to manually installed.
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

:round_pushpin: Add Docker’s official GPG key:

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker.gpg
```

:bulb: check

```
file /usr/share/keyrings/docker.gpg
```
> /usr/share/keyrings/docker.gpg: OpenPGP Public Key Version 4, Created Wed Feb 22 18:36:26 2017, RSA (Encrypt or Sign, 4096 bits); User ID; Signature; OpenPGP Certificate


- [ ] Create the docker debian repository file

```
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/usr/share/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

* Vérifier

```
cat /etc/apt/sources.list.d/docker.list
```
> deb [arch=arm64 signed-by=/usr/share/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu   jammy stable

* Mettre à jour 

```
sudo apt-get update
```
> Retourne 
```yaml
Get:1 https://download.docker.com/linux/ubuntu jammy InRelease [48.9 kB]
Hit:2 http://ports.ubuntu.com/ubuntu-ports jammy InRelease                                                                               
Hit:3 http://ports.ubuntu.com/ubuntu-ports jammy-updates InRelease                                    
Hit:5 http://ports.ubuntu.com/ubuntu-ports jammy-backports InRelease
Get:6 https://download.docker.com/linux/ubuntu jammy/stable arm64 Packages [18.0 kB]
Hit:7 http://ports.ubuntu.com/ubuntu-ports jammy-security InRelease                                      
Hit:4 https://packages.cloud.google.com/apt kubernetes-xenial InRelease          
Fetched 66.9 kB in 2s (29.4 kB/s)                          
Reading package lists... Done
```

:round_pushpin: Installer le :whale: moteur `Docker` [docker-ce](https://docs.docker.com/engine/install/ubuntu/)

- [ ] Choisir une version spécifique

* Lister les versions

```
apt-cache madison docker-ce | awk '{ print $3 }'
```
> Returns :
```yaml
5:24.0.1-1~ubuntu.22.04~jammy
5:24.0.0-1~ubuntu.22.04~jammy
5:23.0.6-1~ubuntu.22.04~jammy
5:23.0.5-1~ubuntu.22.04~jammy
5:23.0.4-1~ubuntu.22.04~jammy
5:23.0.3-1~ubuntu.22.04~jammy
5:23.0.2-1~ubuntu.22.04~jammy
5:23.0.1-1~ubuntu.22.04~jammy
5:23.0.0-1~ubuntu.22.04~jammy
5:20.10.24~3-0~ubuntu-jammy
5:20.10.23~3-0~ubuntu-jammy
...
```

* Choisir la version

```
VERSION_STRING=5:24.0.1-1~ubuntu.22.04~jammy
```

- [ ] Procéder à l'installation

```
sudo apt-get install docker-ce=$VERSION_STRING docker-ce-cli=$VERSION_STRING containerd.io docker-buildx-plugin docker-compose-plugin
```
> Returns :
```yaml
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  docker-ce-rootless-extras libltdl7 libslirp0 pigz slirp4netns
Suggested packages:
  cgroupfs-mount | cgroup-lite
The following NEW packages will be installed:
  containerd.io docker-buildx-plugin docker-ce docker-ce-cli docker-ce-rootless-extras docker-compose-plugin libltdl7 libslirp0 pigz slirp4netns
0 upgraded, 10 newly installed, 0 to remove and 3 not upgraded.
Need to get 87.7 MB of archives.
After this operation, 342 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://ports.ubuntu.com/ubuntu-ports jammy/universe arm64 pigz arm64 2.6-1 [55.7 kB]
Get:2 https://download.docker.com/linux/ubuntu jammy/stable arm64 containerd.io arm64 1.6.21-1 [20.3 MB]
Get:3 http://ports.ubuntu.com/ubuntu-ports jammy/main arm64 libltdl7 arm64 2.4.6-15build2 [39.5 kB]
Get:4 http://ports.ubuntu.com/ubuntu-ports jammy/main arm64 libslirp0 arm64 4.6.1-1build1 [60.8 kB]
Get:5 http://ports.ubuntu.com/ubuntu-ports jammy/universe arm64 slirp4netns arm64 1.0.1-2 [27.0 kB]
Get:6 https://download.docker.com/linux/ubuntu jammy/stable arm64 docker-buildx-plugin arm64 0.10.4-1~ubuntu.22.04~jammy [23.8 MB]
Get:7 https://download.docker.com/linux/ubuntu jammy/stable arm64 docker-ce-cli arm64 5:24.0.1-1~ubuntu.22.04~jammy [12.0 MB]
Get:8 https://download.docker.com/linux/ubuntu jammy/stable arm64 docker-ce arm64 5:24.0.1-1~ubuntu.22.04~jammy [14.0 MB]
Get:9 https://download.docker.com/linux/ubuntu jammy/stable arm64 docker-ce-rootless-extras arm64 5:24.0.1-1~ubuntu.22.04~jammy [8107 kB]
Get:10 https://download.docker.com/linux/ubuntu jammy/stable arm64 docker-compose-plugin arm64 2.18.1-1~ubuntu.22.04~jammy [9398 kB]
Fetched 87.7 MB in 6s (14.1 MB/s)                                                                                                                                    
Selecting previously unselected package pigz.
(Reading database ... 117871 files and directories currently installed.)
Preparing to unpack .../0-pigz_2.6-1_arm64.deb ...
Unpacking pigz (2.6-1) ...
Selecting previously unselected package containerd.io.
Preparing to unpack .../1-containerd.io_1.6.21-1_arm64.deb ...
Unpacking containerd.io (1.6.21-1) ...
Selecting previously unselected package docker-buildx-plugin.
Preparing to unpack .../2-docker-buildx-plugin_0.10.4-1~ubuntu.22.04~jammy_arm64.deb ...
Unpacking docker-buildx-plugin (0.10.4-1~ubuntu.22.04~jammy) ...
Selecting previously unselected package docker-ce-cli.
Preparing to unpack .../3-docker-ce-cli_5%3a24.0.1-1~ubuntu.22.04~jammy_arm64.deb ...
Unpacking docker-ce-cli (5:24.0.1-1~ubuntu.22.04~jammy) ...
Selecting previously unselected package docker-ce.
Preparing to unpack .../4-docker-ce_5%3a24.0.1-1~ubuntu.22.04~jammy_arm64.deb ...
Unpacking docker-ce (5:24.0.1-1~ubuntu.22.04~jammy) ...
Selecting previously unselected package docker-ce-rootless-extras.
Preparing to unpack .../5-docker-ce-rootless-extras_5%3a24.0.1-1~ubuntu.22.04~jammy_arm64.deb ...
Unpacking docker-ce-rootless-extras (5:24.0.1-1~ubuntu.22.04~jammy) ...
Selecting previously unselected package docker-compose-plugin.
Preparing to unpack .../6-docker-compose-plugin_2.18.1-1~ubuntu.22.04~jammy_arm64.deb ...
Unpacking docker-compose-plugin (2.18.1-1~ubuntu.22.04~jammy) ...
Selecting previously unselected package libltdl7:arm64.
Preparing to unpack .../7-libltdl7_2.4.6-15build2_arm64.deb ...
Unpacking libltdl7:arm64 (2.4.6-15build2) ...
Selecting previously unselected package libslirp0:arm64.
Preparing to unpack .../8-libslirp0_4.6.1-1build1_arm64.deb ...
Unpacking libslirp0:arm64 (4.6.1-1build1) ...
Selecting previously unselected package slirp4netns.
Preparing to unpack .../9-slirp4netns_1.0.1-2_arm64.deb ...
Unpacking slirp4netns (1.0.1-2) ...
Setting up docker-buildx-plugin (0.10.4-1~ubuntu.22.04~jammy) ...
Setting up containerd.io (1.6.21-1) ...
Created symlink /etc/systemd/system/multi-user.target.wants/containerd.service → /lib/systemd/system/containerd.service.
Setting up docker-compose-plugin (2.18.1-1~ubuntu.22.04~jammy) ...
Setting up libltdl7:arm64 (2.4.6-15build2) ...
Setting up docker-ce-cli (5:24.0.1-1~ubuntu.22.04~jammy) ...
Setting up libslirp0:arm64 (4.6.1-1build1) ...
Setting up pigz (2.6-1) ...
Setting up docker-ce-rootless-extras (5:24.0.1-1~ubuntu.22.04~jammy) ...
Setting up slirp4netns (1.0.1-2) ...
Setting up docker-ce (5:24.0.1-1~ubuntu.22.04~jammy) ...
Created symlink /etc/systemd/system/multi-user.target.wants/docker.service → /lib/systemd/system/docker.service.
Created symlink /etc/systemd/system/sockets.target.wants/docker.socket → /lib/systemd/system/docker.socket.
Processing triggers for man-db (2.10.2-1) ...
Processing triggers for libc-bin (2.35-0ubuntu3.1) ...
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

- [ ] Vérifier que le service est actif

```
systemctl status docker
```
> Retourne :
```yaml
● docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2023-05-24 18:43:58 UTC; 1min 6s ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 171780 (dockerd)
      Tasks: 10
     Memory: 26.4M
        CPU: 3.708s
     CGroup: /system.slice/docker.service
             └─171780 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock

May 24 18:43:54 rukbat systemd[1]: Starting Docker Application Container Engine...
May 24 18:43:54 rukbat dockerd[171780]: time="2023-05-24T18:43:54.296597733Z" level=info msg="Starting up"
May 24 18:43:54 rukbat dockerd[171780]: time="2023-05-24T18:43:54.298957044Z" level=info msg="detected 127.0.0.53 nameserver, assuming systemd-resolved, so using res>
May 24 18:43:56 rukbat dockerd[171780]: time="2023-05-24T18:43:56.247712689Z" level=info msg="Loading containers: start."
May 24 18:43:58 rukbat dockerd[171780]: time="2023-05-24T18:43:58.413998563Z" level=info msg="Loading containers: done."
May 24 18:43:58 rukbat dockerd[171780]: time="2023-05-24T18:43:58.565046428Z" level=info msg="Docker daemon" commit=463850e graphdriver=overlay2 version=24.0.1
May 24 18:43:58 rukbat dockerd[171780]: time="2023-05-24T18:43:58.565577659Z" level=info msg="Daemon has completed initialization"
May 24 18:43:58 rukbat dockerd[171780]: time="2023-05-24T18:43:58.707205983Z" level=info msg="API listen on /run/docker.sock"
May 24 18:43:58 rukbat systemd[1]: Started Docker Application Container Engine.
```


## :b: Ajouter votre utilisateur au groupe `docker`

```
sudo usermod -aG docker $USER
```

* Sortez de la session et revenez pour l'activer

* pour tester, taper la commande ssuivante et vérifier que le groupe docker est présent

```
groups
```
> ubuntu adm dialout cdrom floppy sudo audio dip video plugdev netdev lxd docker

## :ab: Bridges

Docker uses `iptables` and a kernel module called `br_netfilter` to manage inter-container networking. When the Docker daemon starts, it creates a variety of IPTables rules it required to operate.

- [ ] br_netfilter

```
sudo lsmod | grep br_netfilter
```
> Return
```
br_netfilter           28672  0
bridge                176128  1 br_netfilter
```

- [ ] [Forwarding IPv4 and letting iptables see bridged traffic](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#forwarding-ipv4-and-letting-iptables-see-bridged-traffic)

- [ ] iptables

```
sudo iptables --list | grep DOCKER
```
> Return
```
DOCKER-USER  all  --  anywhere             anywhere            
DOCKER-ISOLATION-STAGE-1  all  --  anywhere             anywhere            
DOCKER     all  --  anywhere             anywhere            
Chain DOCKER (1 references)
Chain DOCKER-ISOLATION-STAGE-1 (1 references)
DOCKER-ISOLATION-STAGE-2  all  --  anywhere             anywhere            
Chain DOCKER-ISOLATION-STAGE-2 (1 references)
Chain DOCKER-USER (1 references)
```

- [ ] Check that the service is enabled

```
systemctl list-unit-files | grep docker
```
> Return
```
docker.service                         enabled         enabled      
docker.socket                          enabled         enabled  
```


## [:back:](../README.md#round_pushpin-installation-des-services)

# References

- [ ] [Container Runtimes](https://kubernetes.io/docs/setup/production-environment/container-runtimes/)
- [ ] [Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

- [ ] Default bridge (docker0) is assigned with an IP address 172.17.0.0/16.

```
systemctl status docker
```
```yaml

● docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2023-04-09 22:30:10 UTC; 1min 44s ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 1168503 (dockerd)
      Tasks: 22
     Memory: 24.6M
        CPU: 423ms
     CGroup: /system.slice/docker.service
             └─1168503 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock

Apr 09 22:30:09 betelgeuse dockerd[1168503]: time="2023-04-09T22:30:09.536171132Z" level=info msg="[core] [Channel #4] Channel Connectivity change to READY" module=grpc
Apr 09 22:30:09 betelgeuse dockerd[1168503]: time="2023-04-09T22:30:09.545919465Z" level=info msg="[graphdriver] using prior storage driver: overlay2"
Apr 09 22:30:09 betelgeuse dockerd[1168503]: time="2023-04-09T22:30:09.548645469Z" level=info msg="Loading containers: start."
Apr 09 22:30:10 betelgeuse dockerd[1168503]: time="2023-04-09T22:30:10.081820871Z" level=info msg="Default bridge (docker0) is assigned with an IP address 172.17.0.0/16. Daemon option --bip can be used to set a preferred IP address"
Apr 09 22:30:10 betelgeuse dockerd[1168503]: time="2023-04-09T22:30:10.214736288Z" level=info msg="Loading containers: done."
Apr 09 22:30:10 betelgeuse dockerd[1168503]: time="2023-04-09T22:30:10.227044928Z" level=info msg="Docker daemon" commit=219f21b graphdriver=overlay2 version=23.0.2
Apr 09 22:30:10 betelgeuse dockerd[1168503]: time="2023-04-09T22:30:10.227108594Z" level=info msg="Daemon has completed initialization"
Apr 09 22:30:10 betelgeuse dockerd[1168503]: time="2023-04-09T22:30:10.244777223Z" level=info msg="[core] [Server #7] Server created" module=grpc
Apr 09 22:30:10 betelgeuse systemd[1]: Started Docker Application Container Engine.
Apr 09 22:30:10 betelgeuse dockerd[1168503]: time="2023-04-09T22:30:10.253118704Z" level=info msg="API listen on /run/docker.sock"
```
