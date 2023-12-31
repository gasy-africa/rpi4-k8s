# :control_knobs: The control plane

:x: The control plane is also called `master` but this term should be used with caution

:warning: The commands below must be done on the server that you have chosen for `control plane`

The default version of `Docker` does not come with the CRI (`containerd`) you have to install it

## :a: Initializing your [control plane](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#initializing-your-control-plane-node) node

#### :construction: [Installing the CRI](#x-troubleshooting-cri-v1-runtime-api-is-not-implemented) :construction:

:round_pushpin: Download images (to speed up the process)

```
sudo kubeadm config images pull
```
> Returns:
```yaml
W0524 18:55:34.815797    1402 images.go:80] could not find officially supported version of etcd for Kubernetes v1.27.2, falling back to the nearest etcd version (3.5.7-0)
[config/images] Pulled registry.k8s.io/kube-apiserver:v1.27.2
[config/images] Pulled registry.k8s.io/kube-controller-manager:v1.27.2
[config/images] Pulled registry.k8s.io/kube-scheduler:v1.27.2
[config/images] Pulled registry.k8s.io/kube-proxy:v1.27.2
[config/images] Pulled registry.k8s.io/pause:3.9
[config/images] Pulled registry.k8s.io/etcd:3.5.7-0
[config/images] Pulled registry.k8s.io/coredns/coredns:v1.10.1
```

* Check the image installation

```
sudo kubeadm config images list
```
> Returns: 
```yaml
W0524 18:58:04.437445    1882 images.go:80] could not find officially supported version of etcd for Kubernetes v1.27.2, falling back to the nearest etcd version (3.5.7-0)
registry.k8s.io/kube-apiserver:v1.27.2
registry.k8s.io/kube-controller-manager:v1.27.2
registry.k8s.io/kube-scheduler:v1.27.2
registry.k8s.io/kube-proxy:v1.27.2
registry.k8s.io/pause:3.9
registry.k8s.io/etcd:3.5.7-0
registry.k8s.io/coredns/coredns:v1.10.1
```

:round_pushpin: Initialize the cluster

`<YOUR CLUSTER'S CONTROL PLAN>` must be a server name with its domain name for example `rukbat.sagittarius.gasy.africa`

```
sudo kubeadm init \
  --pod-network-cidr=172.16.0.0/16 \
  --control-plane-endpoint=<YOUR CLUSTER'S CONTROL PLAN>
```
> Returns:
```yaml
[init] Using Kubernetes version: v1.27.2
[preflight] Running pre-flight checks
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
W0524 18:58:58.608036    2020 images.go:80] could not find officially supported version of etcd for Kubernetes v1.27.2, falling back to the nearest etcd version (3.5.7-0)
W0524 18:58:59.060138    2020 checks.go:835] detected that the sandbox image "registry.k8s.io/pause:3.6" of the container runtime is inconsistent with that used by kubeadm. It is recommended that using "registry.k8s.io/pause:3.9" as the CRI sandbox image.
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local rukbat rukbat.sagittarius.gasy.africa] and IPs [10.96.0.1 192.168.1.200]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [localhost rukbat] and IPs [192.168.1.200 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [localhost rukbat] and IPs [192.168.1.200 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Starting the kubelet
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
[control-plane] Creating static Pod manifest for "kube-scheduler"
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
W0524 18:59:11.051335    2020 images.go:80] could not find officially supported version of etcd for Kubernetes v1.27.2, falling back to the nearest etcd version (3.5.7-0)
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[apiclient] All control plane components are healthy after 17.151430 seconds
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config" in namespace kube-system with the configuration for the kubelets in the cluster
[upload-certs] Skipping phase. Please see --upload-certs
[mark-control-plane] Marking the node rukbat as control-plane by adding the labels: [node-role.kubernetes.io/control-plane node.kubernetes.io/exclude-from-external-load-balancers]
[mark-control-plane] Marking the node rukbat as control-plane by adding the taints [node-role.kubernetes.io/control-plane:NoSchedule]
[bootstrap-token] Using token: ajqivp.mplynkn6sywlo0cf
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to get nodes
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] Configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] Configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
[kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of control-plane nodes by copying certificate authorities
and service account keys on each node and then running the following as root:

  kubeadm join rukbat.sagittarius.gasy.africa:6443 --token ajqivp.mplynkn6sywlo0cf \
        --discovery-token-ca-cert-hash sha256:3a459395bb2ce30e8649c1e6d70bfe6d82779c36252931079d6f13aff8e8209b \
        --control-plane 

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join rukbat.sagittarius.gasy.africa:6443 --token ajqivp.mplynkn6sywlo0cf \
        --discovery-token-ca-cert-hash sha256:3a459395bb2ce30e8649c1e6d70bfe6d82779c36252931079d6f13aff8e8209b 

```

#### :tada: Your Kubernetes control-plane has initialized successfully!

The cluster's initialization will give a long text where there is a :tickets: token `--token` used to:

- [ ] retrieve the [configuration file](control-plane.md#b-use-your-cluster-on-the-control-plane-servernode-) :writing_hand: to manage the cluster
- [ ] initialize the `pod network`
- [ ] allow other nodes to `join` the cluster.

## :b: Use your cluster (on the control plane server/node )

:round_pushpin: To `control` the cluster from the server, use the Kubernetes cluster control tool `kubectl`.

`kubectl` needs a configuration file, `$HOME/.kube/config`.

To install this configuration file, type the following commands:

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Alternatively, if you are the `root` user, you can use the command: (not recommended)

```
export KUBECONFIG=/etc/kubernetes/admin.conf
```

:round_pushpin: Check the current context (with `kubectl`)

```
kubectl config get-contexts
```
> Returns :
```yaml
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   
```

:round pushpin: Rename current context

```
kubectl config rename-context kubernetes-admin@kubernetes kubernetes-admin@sagittarius
```
> Context "kubernetes-admin@kubernetes" renamed to "kubernetes-admin@sagittarius".

:round pushpin: Check nodes

```
kubectl get nodes
```
> Returns :
```yaml
NAME         STATUS     ROLES    AGE     VERSION
rukbat       NotReady   master   4m23s   v1.27.2
```

:round pushpin: Check cluster info

```
kubectl cluster-info
```
> Returns :
```yaml 
Kubernetes control plane is running at https://rukbat.sagittarius.gasy.africa:6443
CoreDNS is running at https://rukbat.sagittarius.gasy.africa:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

:warning: Not yet ready 


## [:back:](../README.md#control_knobs-le-plan-de-contrôle-control-plane)


## :x: TroubleShooting: CRI v1 runtime API is not implemented

- [ ] [kubeadm init error: CRI v1 runtime API is not implemented](https://forum.linuxfoundation.org/discussion/862825/kubeadm-init-error-cri-v1-runtime-api-is-not-implemented)

* `validate service connection: CRI v1 image API is not implemented for endpoint "unix:///var/run/containerd/containerd.sock"`

:round_pushpin: If you encounter this error, the [CRI](https://kubernetes.io/docs/concepts/architecture/cri/) does not come with Docker Engine by default

```
sudo kubeadm config images pull
```
> Returns :
<pre> 
failed to pull image "registry.k8s.io/kube-apiserver:v1.26.3": output: time="2023-04-06T21:11:34Z" level=fatal msg="validate service connection: CRI v1 image API is not implemented for endpoint \"unix:///var/run/containerd/containerd.sock\": rpc error: code = Unimplemented desc = unknown service runtime.v1.ImageService"
, error: exit status 1
To see the stack trace of this error execute with --v=5 or higher
</pre>

:round_pushpin: Install the `CRI`  

- [ ] Remove the old `containerd` package in case it is installed

* Delete config file

```
sudo rm /etc/containerd/config.toml
```

* Remove package

```
sudo apt remove containerd
```
> Returns :
<pre>
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Package 'containerd' is not installed, so not removed
0 upgraded, 0 newly installed, 0 to remove and 5 not upgraded.
</pre>

- [ ] Install `containerd` package

```
sudo apt update, apt install containerd.io
```
> Returns :
```yaml
Hit:1 https://download.docker.com/linux/ubuntu focal InRelease
Hit:3 http://ca.archive.ubuntu.com/ubuntu jammy InRelease                                 
Hit:4 http://ca.archive.ubuntu.com/ubuntu jammy-updates InRelease
Hit:5 http://ca.archive.ubuntu.com/ubuntu jammy-backports InRelease              
Hit:6 http://ca.archive.ubuntu.com/ubuntu jammy-security InRelease               
Hit:2 https://packages.cloud.google.com/apt kubernetes-xenial InRelease
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
5 packages can be upgraded. Run 'apt list --upgradable' to see them.
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages will be upgraded:
  containerd.io
1 upgraded, 0 newly installed, 0 to remove and 4 not upgraded.
Need to get 28.3 MB of archives.
After this operation, 287 kB of additional disk space will be used.
Get:1 https://download.docker.com/linux/ubuntu focal/stable amd64 containerd.io amd64 1.6.20-1 [28.3 MB]
Fetched 28.3 MB in 2s (14.9 MB/s)         
(Reading database ... 76063 files and directories currently installed.)
Preparing to unpack .../containerd.io_1.6.20-1_amd64.deb ...
Unpacking containerd.io (1.6.20-1) over (1.6.19-1) ...
Setting up containerd.io (1.6.20-1) ...
Processing triggers for man-db (2.10.2-1) ...
Scanning processes...                                                                                            
Scanning processor microcode...                                                                                  
Scanning linux images...                                                                                         

Running kernel seems to be up-to-date.

The processor microcode seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
```

- [ ] Check that the `containerd` service is available at startup

```
systemctl is-enabled containerd
```
> enabled

- [ ] As a precautionary action, restart the `containerd` service

```
sudo systemctl restart containerd
```

- [ ] Check the `containerd` service

```
systemctl status containerd
```
> Returns :
```yaml
● containerd.service - containerd container runtime
     Loaded: loaded (/lib/systemd/system/containerd.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2023-04-06 21:47:37 UTC; 27s ago
       Docs: https://containerd.io
    Process: 21239 ExecStartPre=/sbin/modprobe overlay (code=exited, status=0/SUCCESS)
   Main PID: 21240 (containerd)
      Tasks: 79
     Memory: 989.9M
        CPU: 552ms
     CGroup: /system.slice/containerd.service
             ├─20551 /usr/bin/containerd-shim-runc-v2 -namespace k8s.io -id e61797e6a29a2c792dedf0335a50edd7c885>
             ├─20695 /usr/bin/containerd-shim-runc-v2 -namespace k8s.io -id 38ad2171522ac19d75609efad8c3f8de2068>
             ├─21038 /usr/bin/containerd-shim-runc-v2 -namespace k8s.io -id 7e49fdad2c5fae362342f15ea887ad8ebed9>
             ├─21146 /usr/bin/containerd-shim-runc-v2 -namespace k8s.io -id 7e93f53ddea0c4fbb6c32b0adbf5a082b713>
             ├─21240 /usr/bin/containerd
             └─21336 /usr/bin/containerd-shim-runc-v2 -namespace k8s.io -id 9bac2b13accab20c16accaa16d536d6eb5b9>

Apr 06 21:47:55 betelgeuse containerd[21240]: time="2023-04-06T21:47:55.407533370Z" level=info msg="StopPodSandb>
Apr 06 21:47:55 betelgeuse containerd[21240]: time="2023-04-06T21:47:55.407825344Z" level=info msg="StopPodSandb>
Apr 06 21:47:55 betelgeuse containerd[21240]: time="2023-04-06T21:47:55.407927724Z" level=info msg="TearDown net>
lines 1-20
```

* hit `q` to quit

# [:back:](..#ab-the-planes)

# References

- [ ] [Updated: Dockershim Removal FAQ](https://kubernetes.io/blog/2022/02/17/dockershim-faq/)
