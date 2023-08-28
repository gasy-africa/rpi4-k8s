#  :droplet: kubelet

| Service | Usage                                                             |
|---------|--------------------------------------------------------------------------------------------------------------------------|
| kubelet | Responsible for maintaining a set of `pods`, which are composed of one or more `containers`, in a local system |

## :zero: Install library

:bulb: Make sure that the `kubernetes` library repository has been installed previously (see [Installing kubernetes tools](kube-tools.md#one-install-the-google-cloud-repository))

:round_pushpin: Install the `kubelet` service library

- [ ] Choose a specific version of Kubernetes

```
export KUBEVERSION=1.27.2
``` 

- [ ] Begin installing

```
sudo apt update && sudo apt -y install kubelet=${KUBEVERSION}-00
```
> Returns :
```yaml
Hit:1 http://ports.ubuntu.com/ubuntu-ports jammy InRelease
Get:3 http://ports.ubuntu.com/ubuntu-ports jammy-updates InRelease [119 kB]
Hit:2 https://packages.cloud.google.com/apt kubernetes-xenial InRelease
Get:4 http://ports.ubuntu.com/ubuntu-ports jammy-backports InRelease [108 kB]
Get:5 http://ports.ubuntu.com/ubuntu-ports jammy-security InRelease [110 kB]
Get:6 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main arm64 Packages [889 kB]
Fetched 1225 kB in 2s (602 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
All packages are up to date.
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
kubelet is already the newest version (1.27.2-00).
kubelet set to manually installed.
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

:round_pushpin:  Prevent tampering (updating) of the :droplet: kubelet service

```
sudo apt-mark hold kubelet
```
> kubelet set on hold.

## :gear: Where is the `kubelet` service file [drop-in file](https://stackoverflow.com/questions/59842743/what-is-a-drop-in-file-what-is-a -drop-in-directory-how-to-edit-systemd-service) (i.e. systemd .conf file)

```
sudo cat /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
```
> Returns
```yaml
# Note: This dropin only works with kubeadm and kubelet v1.11+
[Service]
Environment="KUBELET_KUBECONFIG_ARGS=--bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf"
Environment="KUBELET_CONFIG_ARGS=--config=/var/lib/kubelet/config.yaml"
# This is a file that "kubeadm init" and "kubeadm join" generates at runtime, populating the KUBELET_KUBEADM_ARGS variable dynamically
EnvironmentFile=-/var/lib/kubelet/kubeadm-flags.env
# This is a file that the user can use for overrides of the kubelet args as a last resort. Preferably, the user should use
# the .NodeRegistration.KubeletExtraArgs object in the configuration files instead. KUBELET_EXTRA_ARGS should be sourced from this file.
EnvironmentFile=-/etc/default/kubelet
ExecStart=
ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS $KUBELET_EXTRA_ARGS
```

## :one: Immediately after installing the service

:+1: Test that the `kubelet` service is loaded **(loaded)**

```
systemctl status kubelet
```
> Returns
```yaml
● kubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /etc/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: activating (auto-restart) (Result: exit-code) since Wed 2023-05-24 18:29:35 UTC; 3s ago
       Docs: https://kubernetes.io/docs/home/
    Process: 168398 ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS $KUBELET_EXTRA_ARGS (code=exited, status=1/FAILURE)
   Main PID: 168398 (code=exited, status=1/FAILURE)
        CPU: 222ms
```

:bulb: Press `q` to quit

## :two: Start the :droplet: `kubelet` service

``` 
sudo systemctl enable kubelet && sudo systemctl start kubelet
```

#### :warning: `kubelet` service will only be available after `kubeadm init` cluster initialization

## [:back:](../README.md#round_pushpin-installing-services)

# Références

https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/kubelet-integration/


# :x: Troubleshooting

:one: If you got the wrong version

- [ ] check installed version

```
sudo dpkg --list kubelet
```
> Returns
```
Desired=Unknown/Install/Remove/Purge/Hold
| Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
|/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
||/ Name           Version      Architecture Description
+++-==============-============-============-=================================
ii  kubelet        1.26.3-00    amd64        Kubernetes Node Agent
```

- [ ] If it is not the correct version,

   - [ ] remove marking
   
     ```
     sudo apt-mark unhold kubelet kubeadm
     ```

   - [ ] remove the library

     ```
     sudo apt purge kubelet
     ```

:bulb: If a library depends on it, uninstall it (v.i.e. `kubeadm`)

- [ ] Reinstall the library with the correct version

     - [ ] Choose a specific version of Kubernetes

     ```
     export KUBEVERSION=1.27.2
     ``` 

- [ ] restart installing (including kubeadm)

     ```
     sudo apt -y install kubelet=${KUBEVERSION}-00 && sudo apt -y install kubeadm=${KUBEVERSION}-00
     ```
