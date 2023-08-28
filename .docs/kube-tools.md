# Kube Tools

Sur chacun des serveurs, installer les outils suivants

## :zero: Preparer le serveur

Mettre le serveur à jour et le redémarrer.

:warning: Attention, le serveur va `reboot`er

```
sudo apt update && sudo apt -y upgrade && sudo systemctl reboot
```

## :one: Installer le [référentiel](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-using-native-package-management) `google cloud`

:round_pushpin: Installer `curl`, `apt-transport-https` et autres outils utilisés pour télécharger les librairies (Package Manager)

:bulb: Certains référentiels ont des `URL` utilisant `HTTPS`. La librairie `apt-transport-https` doit être installée pour acceder le lien `HTTPS`:

```
sudo apt-get install -y apt-transport-https ca-certificates curl
```

:round_pushpin: Ajout du référentiel de librairies `kubernetes` au référentiel de gestionnaire de libraries  

- [ ] Ajout de la clé officielle `gpg` :key: de `google cloud`  `GPG` (GNU Privacy Guard)
      
* placée dans le fichier `binaire` :grey_exclamation: `/usr/share/keyrings/kubernetes-archive-keyring.gpg`
* en tapant la commande

```
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://dl.k8s.io/apt/doc/apt-key.gpg 
```

* Vérification de la clé :key: `GPG`

```
file /usr/share/keyrings/kubernetes-archive-keyring.gpg
```
> /usr/share/keyrings/kubernetes-archive-keyring.gpg: OpenPGP Public Key Version 4, Created Sat May 21 09:50:12 2022, RSA (Encrypt or Sign, 2048 bits); User ID; Signature; OpenPGP Certificate

- [ ] Ajouter le fichier `kubernetes debian` au référentiel `apt`

```
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" \
         | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

- [ ] Vérifier le contenu du fichier

```
cat /etc/apt/sources.list.d/kubernetes.list
```
> Retourne :
<pre> 
deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main
</pre>

## :two: Installer **kubeadm** and **kubectl**

:bulb: Quels outils :ice_cube: kube doit on installer? 

| Outil   | Utilisation                                                      |
|---------|------------------------------------------------------------------|
| kubeadm | Outil créé pour fournir **kubeadm** `init` et **kubeadm** `join` |
| kubectl | Outil de ligne de commande Kubernetes                            |

:round_pushpin: Installer les outils :ice_cube: `kube`

- [ ] Choisir une version spécifique de Kubernetes

```
export KUBEVERSION=1.27.2
``` 

- [ ] Installer la version spécifique de Kubernetes

```
sudo apt update && sudo apt install -y kubeadm=${KUBEVERSION}-00 kubectl=${KUBEVERSION}-00
```
> Retourne :
```yaml
Hit:1 http://ports.ubuntu.com/ubuntu-ports jammy InRelease
Hit:2 http://ports.ubuntu.com/ubuntu-ports jammy-updates InRelease
Hit:3 http://ports.ubuntu.com/ubuntu-ports jammy-backports InRelease
Hit:5 http://ports.ubuntu.com/ubuntu-ports jammy-security InRelease
Hit:4 https://packages.cloud.google.com/apt kubernetes-xenial InRelease
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
11 packages can be upgraded. Run 'apt list --upgradable' to see them.
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  conntrack cri-tools ebtables kubelet kubernetes-cni socat
The following NEW packages will be installed:
  conntrack cri-tools ebtables kubeadm kubectl kubelet kubernetes-cni socat
0 upgraded, 8 newly installed, 0 to remove and 11 not upgraded.
Need to get 77.0 MB of archives.
After this operation, 316 MB of additional disk space will be used.
Get:1 http://ports.ubuntu.com/ubuntu-ports jammy/main arm64 conntrack arm64 1:1.4.6-2build2 [32.4 kB]
Get:3 http://ports.ubuntu.com/ubuntu-ports jammy/main arm64 ebtables arm64 2.0.11-4build2 [85.4 kB]
Get:8 http://ports.ubuntu.com/ubuntu-ports jammy/main arm64 socat arm64 1.7.4.1-3ubuntu4 [348 kB]                 
Get:2 https://packages.cloud.google.com/apt kubernetes-xenial/main arm64 cri-tools arm64 1.26.0-00 [17.3 MB]
Get:4 https://packages.cloud.google.com/apt kubernetes-xenial/main arm64 kubernetes-cni arm64 1.2.0-00 [25.8 MB]
Get:5 https://packages.cloud.google.com/apt kubernetes-xenial/main arm64 kubelet arm64 1.27.2-00 [16.2 MB]
Get:6 https://packages.cloud.google.com/apt kubernetes-xenial/main arm64 kubectl arm64 1.27.1-00 [8751 kB]
Get:7 https://packages.cloud.google.com/apt kubernetes-xenial/main arm64 kubeadm arm64 1.27.1-00 [8483 kB]                                                           
Fetched 77.0 MB in 7s (10.6 MB/s)                                                                                                                                    
Selecting previously unselected package conntrack.
(Reading database ... 117764 files and directories currently installed.)
Preparing to unpack .../0-conntrack_1%3a1.4.6-2build2_arm64.deb ...
Unpacking conntrack (1:1.4.6-2build2) ...
Selecting previously unselected package cri-tools.
Preparing to unpack .../1-cri-tools_1.26.0-00_arm64.deb ...
Unpacking cri-tools (1.26.0-00) ...
Selecting previously unselected package ebtables.
Preparing to unpack .../2-ebtables_2.0.11-4build2_arm64.deb ...
Unpacking ebtables (2.0.11-4build2) ...
Selecting previously unselected package kubernetes-cni.
Preparing to unpack .../3-kubernetes-cni_1.2.0-00_arm64.deb ...
Unpacking kubernetes-cni (1.2.0-00) ...
Selecting previously unselected package socat.
Preparing to unpack .../4-socat_1.7.4.1-3ubuntu4_arm64.deb ...
Unpacking socat (1.7.4.1-3ubuntu4) ...
Selecting previously unselected package kubelet.
Preparing to unpack .../5-kubelet_1.27.2-00_arm64.deb ...
Unpacking kubelet (1.27.2-00) ...
Selecting previously unselected package kubectl.
Preparing to unpack .../6-kubectl_1.27.1-00_arm64.deb ...
Unpacking kubectl (1.27.1-00) ...
Selecting previously unselected package kubeadm.
Preparing to unpack .../7-kubeadm_1.27.1-00_arm64.deb ...
Unpacking kubeadm (1.27.1-00) ...
Setting up conntrack (1:1.4.6-2build2) ...
Setting up kubectl (1.27.1-00) ...
Setting up ebtables (2.0.11-4build2) ...
Setting up socat (1.7.4.1-3ubuntu4) ...
Setting up cri-tools (1.26.0-00) ...
Setting up kubernetes-cni (1.2.0-00) ...
Setting up kubelet (1.27.2-00) ...
Created symlink /etc/systemd/system/multi-user.target.wants/kubelet.service → /lib/systemd/system/kubelet.service.
Setting up kubeadm (1.27.1-00) ...
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

:round_pushpin: Prévenir l'altération (mise à jour) des outils :ice_cube: `kube`

```
sudo apt-mark hold kubeadm kubectl
```
> Retourne :
```yaml
kubeadm set on hold.
kubectl set on hold.
```

:+1: Tester que les outils :ice_cube: `kube` sont installés

```
OUT="--output=json"; kubeadm version ${OUT} && kubectl version --client ${OUT}
```
> Retourne
```json
{
  "clientVersion": {
    "major": "1",
    "minor": "27",
    "gitVersion": "v1.27.2",
    "gitCommit": "7f6f68fdabc4df88cfea2dcf9a19b2b830f1e647",
    "gitTreeState": "clean",
    "buildDate": "2023-05-17T14:18:49Z",
    "goVersion": "go1.20.4",
    "compiler": "gc",
    "platform": "linux/arm64"
  }
}
{
  "clientVersion": {
    "major": "1",
    "minor": "27",
    "gitVersion": "v1.27.2",
    "gitCommit": "7f6f68fdabc4df88cfea2dcf9a19b2b830f1e647",
    "gitTreeState": "clean",
    "buildDate": "2023-05-17T14:20:07Z",
    "goVersion": "go1.20.4",
    "compiler": "gc",
    "platform": "linux/arm64"
  },
  "kustomizeVersion": "v5.0.1"
}
```

## [:back:](../README.md#round_pushpin-installation-des-outils)

# References

- [ ] [Installing kubeadm, kubelet and kubectl](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl)
- [ ] [Temporarily host apt-key.gpg as original url is failing](https://github.com/kubernetes/k8s.io/pull/4837)
