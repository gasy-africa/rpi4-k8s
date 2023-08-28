# :droplet: CNI Plugin

Installer the CNI Plugin pour gérer le réseau de gousses [Cluster Networking](https://kubernetes.io/docs/concepts/cluster-administration/networking/)

* Exécuter `kubectl apply -f [podnetwork].yaml` with one of the options listed at:

  https://kubernetes.io/docs/concepts/cluster-administration/addons/

## :a: Installer le plugin CNI


- [ ] Le plugin CNI a utiliser est [Cilium]([https://cilium.io/](https://docs.cilium.io/en/stable/gettingstarted/k8s-install-default/))

Sur le serveur du plan de contrôle:

- [ ] Installer le binaire `cilium` dans `/usr/local/bin`

```
CILIUM_CLI_VERSION=$(curl -s https://raw.githubusercontent.com/cilium/cilium-cli/master/stable.txt)
CLI_ARCH=amd64
if [ "$(uname -m)" = "aarch64" ]; then CLI_ARCH=arm64; fi
curl -L --fail --remote-name-all https://github.com/cilium/cilium-cli/releases/download/${CILIUM_CLI_VERSION}/cilium-linux-${CLI_ARCH}.tar.gz{,.sha256sum}
sha256sum --check cilium-linux-${CLI_ARCH}.tar.gz.sha256sum
sudo tar xzvfC cilium-linux-${CLI_ARCH}.tar.gz /usr/local/bin
rm cilium-linux-${CLI_ARCH}.tar.gz{,.sha256sum}
```
> Returns :
```yaml
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100 26.6M  100 26.6M    0     0  9984k      0  0:00:02  0:00:02 --:--:-- 14.2M
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100    92  100    92    0     0    510      0 --:--:-- --:--:-- --:--:--   510
cilium-linux-arm64.tar.gz: OK
cilium
```

- [ ] Installer le plugin


```
cilium install
```
> Returns :

```yaml
ℹ️  Using Cilium version 1.13.2
🔮 Auto-detected cluster name: kubernetes
🔮 Auto-detected datapath mode: tunnel
🔮 Auto-detected kube-proxy has been installed
ℹ️  helm template --namespace kube-system cilium cilium/cilium --version 1.13.2 --set cluster.id=0,cluster.name=kubernetes,encryption.nodeEncryption=false,kubeProxyReplacement=disabled,operator.replicas=1,serviceAccounts.cilium.name=cilium,serviceAccounts.operator.name=cilium-operator,tunnel=vxlan
ℹ️  Storing helm values file in kube-system/cilium-cli-helm-values Secret
🔑 Created CA in secret cilium-ca
🔑 Generating certificates for Hubble...
🚀 Creating Service accounts...
🚀 Creating Cluster roles...
🚀 Creating ConfigMap for Cilium version 1.13.2...
🚀 Creating Agent DaemonSet...
🚀 Creating Operator Deployment...
⌛ Waiting for Cilium to be installed and ready...
✅ Cilium was successfully installed! Run 'cilium status' to view installation health
```

- [ ] Vérifier le statutt

```
cilium status
```
> Returns :
```yaml
    /¯¯\
 /¯¯\__/¯¯\    Cilium:          OK
 \__/¯¯\__/    Operator:        OK
 /¯¯\__/¯¯\    Hubble Relay:    disabled
 \__/¯¯\__/    ClusterMesh:     disabled
    \__/

DaemonSet         cilium             Desired: 1, Ready: 1/1, Available: 1/1
Deployment        cilium-operator    Desired: 1, Ready: 1/1, Available: 1/1
Containers:       cilium             Running: 1
                  cilium-operator    Running: 1
Cluster Pods:     2/2 managed by Cilium
Image versions    cilium             quay.io/cilium/cilium:v1.13.2@sha256:85708b11d45647c35b9288e0de0706d24a5ce8a378166cadc700f756cc1a38d6: 1
                  cilium-operator    quay.io/cilium/operator-generic:v1.13.2@sha256:a1982c0a22297aaac3563e428c330e17668305a41865a842dec53d241c5490ab: 1
```

- [ ] Vérifier le service :droplet: `Kubelet` après l'installation du plugin CNI

```
sudo systemctl status kubelet
```
> Retourne :
```yaml
● kubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /etc/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: active (running) since Wed 2023-05-24 18:59:31 UTC; 15min ago
       Docs: https://kubernetes.io/docs/home/
   Main PID: 2617 (kubelet)
      Tasks: 13 (limit: 4369)
     Memory: 39.7M
        CPU: 1min 25.298s
     CGroup: /system.slice/kubelet.service
             └─2617 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubele>

May 24 19:13:20 rukbat kubelet[2617]: I0524 19:13:20.404435    2617 topology_manager.go:212] "Topology Admit Handler"
May 24 19:13:20 rukbat kubelet[2617]: I0524 19:13:20.425671    2617 topology_manager.go:212] "Topology Admit Handler"
May 24 19:13:20 rukbat kubelet[2617]: I0524 19:13:20.470733    2617 reconciler_common.go:258] "operationExecutor.VerifyControllerAttachedVolume started for volume \">
May 24 19:13:20 rukbat kubelet[2617]: I0524 19:13:20.470915    2617 reconciler_common.go:258] "operationExecutor.VerifyControllerAttachedVolume started for volume \">
May 24 19:13:20 rukbat kubelet[2617]: I0524 19:13:20.471012    2617 reconciler_common.go:258] "operationExecutor.VerifyControllerAttachedVolume started for volume \">
May 24 19:13:20 rukbat kubelet[2617]: I0524 19:13:20.471111    2617 reconciler_common.go:258] "operationExecutor.VerifyControllerAttachedVolume started for volume \">
May 24 19:13:23 rukbat kubelet[2617]: I0524 19:13:23.384831    2617 pod_startup_latency_tracker.go:102] "Observed pod startup duration" pod="kube-system/coredns-5d78>
May 24 19:13:24 rukbat kubelet[2617]: I0524 19:13:24.313106    2617 pod_startup_latency_tracker.go:102] "Observed pod startup duration" pod="kube-system/coredns-5d78>
May 24 19:14:32 rukbat kubelet[2617]: W0524 19:14:32.034004    2617 sysinfo.go:203] Nodes topology is not available, providing CPU topology
May 24 19:14:32 rukbat kubelet[2617]: W0524 19:14:32.040579    2617 machine.go:65] Cannot read vendor id correctly, set empty.
```

:round_pushpin: Tester que le CNI :droplet: plugin est bien installé, 

```
k get no
```
> Retourne :
```yaml
NAME     STATUS     ROLES           AGE   VERSION
rukbat   NotReady   control-plane   16h   v1.27.2
```

`Ready` doit s'afficher pour le `control-plane` après quelques minutes

```
k get no
```
> Retourne :
```yaml
NAME     STATUS   ROLES           AGE   VERSION
rukbat   Ready    control-plane   16m   v1.27.2
```

## [:back:](../#control_knobs-le-plan-de-contrôle-control-plane)


## :x: Troubleshooting

:round_pushpin: Uninstall

```
cilium uninstall
```
> Returns :
```yaml
🔥 Deleting pods in cilium-test namespace...
🔥 Deleting cilium-test namespace...
🔥 Deleting Hubble UI...
🔥 Deleting Relay...
🔥 Deleting Relay certificates...
🔥 Deleting Peer Service...
🔥 Enabling CNI cleanup...
🔥 Deleting agent DaemonSet...
🔥 Deleting operator Deployment...
🔥 Deleting cilium-secrets namespace...
🔥 Deleting ConfigMap...
🔥 Deleting Roles...
🔥 Deleting Cluster roles...
🔥 Deleting IngressClass...
🔥 Deleting Ingress Service...
🔥 Deleting Ingress Endpoints...
🔥 Deleting Ingress Secret Namespace...
🔥 Deleting Service accounts...
🔥 Deleting clustermesh-apiserver...
🔥 Deleting ClusterMesh certificates...
✅ ClusterMesh disabled.
🔥 Deleting certificates...
🔥 Deleting secret with the helm values configuration...
✅ Cilium was successfully uninstalled.
```

## Afficher les pods systèmes

```
sudo crictl ps
```
> Retourne :
```yaml
CONTAINER           IMAGE               CREATED             STATE               NAME                      ATTEMPT             POD ID              POD
a76c06a5e77f8       97e04611ad434       16 minutes ago      Running             coredns                   0                   b56be6efe2fec       coredns-5d78c9869d-57k8b
13f65af296cfe       97e04611ad434       16 minutes ago      Running             coredns                   0                   69f82530fe597       coredns-5d78c9869d-zgfxd
6318421f95bd2       622517d56384b       17 minutes ago      Running             cilium-agent              0                   695da77b535b5       cilium-d8g5g
be4b4639292b8       61e0578a4382b       17 minutes ago      Running             cilium-operator           0                   eff6a2f56cddf       cilium-operator-5bb89494dc-hcj9h
e1ff389aa63d0       305d7ed1dae28       17 minutes ago      Running             kube-scheduler            2                   02cf629dc17f7       kube-scheduler-rukbat
0a4737f44641f       2ee705380c3c5       17 minutes ago      Running             kube-controller-manager   2                   f34f939c86549       kube-controller-manager-rukbat
ee462a6281532       29921a0845422       30 minutes ago      Running             kube-proxy                0                   614e6bc61ac03       kube-proxy-cc45c
14bc5f4c1e86a       72c9df6be7f1b       31 minutes ago      Running             kube-apiserver            0                   88098c77919f3       kube-apiserver-rukbat
a18ed5eb41ab8       24bc64e911039       31 minutes ago      Running             etcd                      0                   e347a95a1e7ee       etcd-rukbat
```

#### :round_pushpin: [Restart kube-apiserver when provisioned with kubeadm](https://stackoverflow.com/questions/42674726/restart-kube-apiserver-when-provisioned-with-kubeadm)

:construction: Si on perd le `kube-apiserver` à cause d'une commande (i.e. CNI)

- [ ] :one: initialiser l'heure du fichier de configuration `kube-apiserver.yaml` avec `touch`

```
sudo touch /etc/kubernetes/manifests/kube-apiserver.yaml

```

- [ ] :two: Redémarrer le service `kubelet`

```
sudo systemctl restart kubelet
```

- [ ] :two: Enlever la commande fautive (i.e. CNI)

```
cilium uninstall
```


#### :round_pushpin: Nettoyer la grappe

```
kubectl drain betelgeuse --delete-emptydir-data --ignore-daemonsets --force
```

```
k get no
```
> Retourne :
<pre> 
NAME         STATUS                     ROLES           AGE     VERSION
rukbat       Ready,SchedulingDisabled   control-plane   4h17m   v1.27.1
</pre> 

#### :skull_and_crossbones: Réinitialiser la grappe

```
sudo kubeadm reset
```
> Retourne :
```yaml 
```

#### :round_pushpin: https://stackoverflow.com/questions/51169728/failed-create-pod-sandbox-rpc-error-code-unknown-desc-networkplugin-cni-fa

| repertoire   | fonction |
|--------------|----------|
| /etc/cni     | net.d    |
| /opt/cni     | bin      |
| /var/lib/cni |          |


# References:

https://stackoverflow.com/questions/52390481/kubernetes-calico-node-xxxxxxxxxxx-already-using-ipv4-address-xxxxxxxxx-crash


- [ ] [Add requirements for Raspberry Pi #23337](https://github.com/cilium/cilium/pull/23337)

```
sudo apt install linux-modules-extra-5.15.0-1026-raspi
```

* Generated when extra modules are not installed

> level=fatal msg="failed to start: daemon creation failed: unable to setup device manager: protocol not supported" subsys=daemon

```yaml
level=info msg=" - !:etcd_node" subsys=labels-filter
level=info msg="Using autogenerated IPv4 allocation range" subsys=node v4Prefix=10.200.0.0/16
level=info msg=Invoked duration=4.919268ms function="gops.registerGopsHooks (cell.go:39)" subsys=hive
level=info msg=Invoked duration=19.956382ms function="cmd.glob..func2 (daemon_main.go:1644)" subsys=hive
level=info msg="Started gops server" address="127.0.0.1:9890" subsys=gops
level=info msg="Start hook executed" duration=1.63825ms function="gops.registerGopsHooks.func1 (cell.go:44)" subsys=hive
level=info msg="Establishing connection to apiserver" host="https://10.96.0.1:443" subsys=k8s-client
level=info msg="Connected to apiserver" subsys=k8s-client
level=info msg="Start hook executed" duration=26.30283ms function="client.(*compositeClientset).onStart" subsys=hive
level=info msg="Start hook executed" duration=81.523908ms function="cmd.newDatapath.func1 (daemon_main.go:1624)" subsys=hive
level=info msg="Start hook executed" duration="18.166µs" function="*resource.resource[*k8s.io/api/core/v1.Node].Start" subsys=hive
level=info msg="Start hook executed" duration="5.889µs" function="*resource.resource[*github.com/cilium/cilium/pkg/k8s/apis/cilium.io/v2.CiliumNode].Start" subsys=hive
level=info msg="Auto-disabling \"enable-node-port\", \"enable-external-ips\", \"bpf-lb-sock\", \"enable-host-port\" features and falling back to \"enable-host-legacy-routing\"" subsys=daemon
level=info msg="Inheriting MTU from external network interface" device=eth0 ipAddr=192.168.1.200 mtu=1500 subsys=mtu
level=error msg="Start hook failed" error="daemon creation failed: unable to setup device manager: protocol not supported" function="cmd.newDaemonPromise.func1 (daemon_main.go:1677)" subsys=hive
level=info msg="Stop hook executed" duration="19.018µs" function="*resource.resource[*github.com/cilium/cilium/pkg/k8s/apis/cilium.io/v2.CiliumNode].Stop" subsys=hive
level=info msg="Stop hook executed" duration="27.518µs" function="*resource.resource[*k8s.io/api/core/v1.Node].Stop" subsys=hive
level=info msg="Stop hook executed" duration="109.035µs" function="client.(*compositeClientset).onStop" subsys=hive
level=info msg="Stopped gops server" address="127.0.0.1:9890" subsys=gops
level=info msg="Stop hook executed" duration="472.955µs" function="gops.registerGopsHooks.func2 (cell.go:51)" subsys=hive
level=fatal msg="failed to start: daemon creation failed: unable to setup device manager: protocol not supported" subsys=daemon
```

```yaml
level=info msg="successfully acquired lease kube-system/cilium-operator-resource-lock" subsys=klog
level=info msg="Leading the operator HA deployment" subsys=cilium-operator-generic
level=info msg="Start hook executed" duration="5.426µs" function="*resource.resource[*github.com/cilium/cilium/pkg/k8s/apis/cilium.io/v2alpha1.CiliumLoadBalancerIPPool].Start" subsys=hive
level=info msg="Start hook executed" duration="20.795µs" function="*resource.resource[*github.com/cilium/cilium/pkg/k8s/slim/k8s/api/core/v1.Service].Start" subsys=hive
level=info msg="Starting LB IPAM" subsys=lbipam
level=info msg="LB-IPAM in pre-init phase, waiting for pools" subsys=lbipam
level=info msg="Start hook executed" duration="304.032µs" function="*lbipam.LBIPAM.Start" subsys=hive
level=info msg="Initializing IPAM" mode=cluster-pool subsys=cilium-operator-generic
level=info msg="Starting ClusterPool IP allocator" ipv4CIDRs="[10.0.0.0/8]" ipv6CIDRs="[]" subsys=ipam-allocator-clusterpool
level=info msg="Removing Cilium Node Taints or Setting Cilium Is Up Condition for Kubernetes Nodes" k8sNamespace=kube-system label-selector="k8s-app=cilium" remove-cilium-node-taints=true set-cilium-is-up-condition=true subsys=cilium-operator-generic
level=info msg="Starting to synchronize CiliumNode custom resources" subsys=cilium-operator-generic
level=info msg="Starting to garbage collect stale CiliumNode custom resources" subsys=watchers
level=info msg="Garbage collected status/nodes in Cilium Network Policies found=0, gc=0" subsys=cilium-operator-generic
level=info msg="Garbage collected status/nodes in Cilium Clusterwide Network Policies found=0, gc=0" subsys=cilium-operator-generic
level=info msg="CiliumNodes caches synced with Kubernetes" subsys=cilium-operator-generic
level=info msg="Starting to garbage collect stale CiliumEndpoint custom resources" subsys=cilium-operator-generic
level=info msg="Starting CRD identity garbage collector" interval=15m0s subsys=cilium-operator-generic
level=info msg="Starting CNP derivative handler" subsys=cilium-operator-generic
level=info msg="Starting CCNP derivative handler" subsys=cilium-operator-generic
level=info msg="Initialization complete" subsys=cilium-operator-generic
level=info msg="Start hook executed" duration=315.098452ms function="cmd.(*legacyOnLeader).onStart" subsys=hive
```

```yaml
sudo tail /var/log/syslog
Apr 18 05:28:34 rukbat containerd[15119]: time="2023-04-18T05:28:34.275405098Z" level=error msg="StopPodSandbox for \"a936d96c25bcae13dbea32ecf1afec783f178fcbbe6871418db6b58718b300b2\" failed" error="failed to destroy network for sandbox \"a936d96c25bcae13dbea32ecf1afec783f178fcbbe6871418db6b58718b300b2\": plugin type=\"cilium-cni\" name=\"cilium\" failed (delete): unable to connect to Cilium daemon: failed to create cilium agent client after 30.000000 seconds timeout: Get \"http:///var/run/cilium/cilium.sock/v1/config\": dial unix /var/run/cilium/cilium.sock: connect: no such file or directory\nIs the agent running?"
Apr 18 05:28:34 rukbat kubelet[15642]: E0418 05:28:34.276311   15642 remote_runtime.go:205] "StopPodSandbox from runtime service failed" err=<
Apr 18 05:28:34 rukbat kubelet[15642]: #011rpc error: code = Unknown desc = failed to destroy network for sandbox "a936d96c25bcae13dbea32ecf1afec783f178fcbbe6871418db6b58718b300b2": plugin type="cilium-cni" name="cilium" failed (delete): unable to connect to Cilium daemon: failed to create cilium agent client after 30.000000 seconds timeout: Get "http:///var/run/cilium/cilium.sock/v1/config": dial unix /var/run/cilium/cilium.sock: connect: no such file or directory
Apr 18 05:28:34 rukbat kubelet[15642]: #011Is the agent running?
Apr 18 05:28:34 rukbat kubelet[15642]:  > podSandboxID="a936d96c25bcae13dbea32ecf1afec783f178fcbbe6871418db6b58718b300b2"
Apr 18 05:28:34 rukbat kubelet[15642]: E0418 05:28:34.276448   15642 kuberuntime_manager.go:1312] "Failed to stop sandbox" podSandboxID={Type:containerd ID:a936d96c25bcae13dbea32ecf1afec783f178fcbbe6871418db6b58718b300b2}
Apr 18 05:28:34 rukbat kubelet[15642]: E0418 05:28:34.276603   15642 kuberuntime_manager.go:1038] "killPodWithSyncResult failed" err="failed to \"KillPodSandbox\" for \"fc22ffed-b2a8-49e0-a4f9-492c8cded486\" with KillPodSandboxError: \"rpc error: code = Unknown desc = failed to destroy network for sandbox \\\"a936d96c25bcae13dbea32ecf1afec783f178fcbbe6871418db6b58718b300b2\\\": plugin type=\\\"cilium-cni\\\" name=\\\"cilium\\\" failed (delete): unable to connect to Cilium daemon: failed to create cilium agent client after 30.000000 seconds timeout: Get \\\"http:///var/run/cilium/cilium.sock/v1/config\\\": dial unix /var/run/cilium/cilium.sock: connect: no such file or directory\\nIs the agent running?\""
Apr 18 05:28:34 rukbat kubelet[15642]: E0418 05:28:34.276727   15642 pod_workers.go:1281] "Error syncing pod, skipping" err="failed to \"KillPodSandbox\" for \"fc22ffed-b2a8-49e0-a4f9-492c8cded486\" with KillPodSandboxError: \"rpc error: code = Unknown desc = failed to destroy network for sandbox \\\"a936d96c25bcae13dbea32ecf1afec783f178fcbbe6871418db6b58718b300b2\\\": plugin type=\\\"cilium-cni\\\" name=\\\"cilium\\\" failed (delete): unable to connect to Cilium daemon: failed to create cilium agent client after 30.000000 seconds timeout: Get \\\"http:///var/run/cilium/cilium.sock/v1/config\\\": dial unix /var/run/cilium/cilium.sock: connect: no such file or directory\\nIs the agent running?\"" pod="kube-system/coredns-5d78c9869d-xdl8j" podUID=fc22ffed-b2a8-49e0-a4f9-492c8cded486
Apr 18 05:28:35 rukbat containerd[15119]: time="2023-04-18T05:28:35.251476353Z" level=info msg="StopPodSandbox for \"c7fc422ea08da63c4adb4623ce672a43ecfe34142ba18515ea2f6022cbd81f71\""
Apr 18 05:28:35 rukbat containerd[15119]: time="2023-04-18T05:28:35.253078159Z" level=info msg="StopPodSandbox for \"a936d96c25bcae13dbea32ecf1afec783f178fcbbe6871418db6b58718b300b2\""
ubuntu@rukbat:~$ sudo tail /var/log/syslog
Apr 18 05:29:36 rukbat kubelet[15642]: E0418 05:29:36.594072   15642 remote_runtime.go:205] "StopPodSandbox from runtime service failed" err=<
Apr 18 05:29:36 rukbat kubelet[15642]: #011rpc error: code = Unknown desc = failed to destroy network for sandbox "c7fc422ea08da63c4adb4623ce672a43ecfe34142ba18515ea2f6022cbd81f71": plugin type="cilium-cni" name="cilium" failed (delete): unable to connect to Cilium daemon: failed to create cilium agent client after 30.000000 seconds timeout: Get "http:///var/run/cilium/cilium.sock/v1/config": dial unix /var/run/cilium/cilium.sock: connect: no such file or directory
Apr 18 05:29:36 rukbat kubelet[15642]: #011Is the agent running?
Apr 18 05:29:36 rukbat kubelet[15642]:  > podSandboxID="c7fc422ea08da63c4adb4623ce672a43ecfe34142ba18515ea2f6022cbd81f71"
Apr 18 05:29:36 rukbat kubelet[15642]: E0418 05:29:36.594261   15642 kuberuntime_manager.go:1312] "Failed to stop sandbox" podSandboxID={Type:containerd ID:c7fc422ea08da63c4adb4623ce672a43ecfe34142ba18515ea2f6022cbd81f71}
Apr 18 05:29:36 rukbat kubelet[15642]: E0418 05:29:36.594181   15642 kuberuntime_manager.go:1312] "Failed to stop sandbox" podSandboxID={Type:containerd ID:a936d96c25bcae13dbea32ecf1afec783f178fcbbe6871418db6b58718b300b2}
Apr 18 05:29:36 rukbat kubelet[15642]: E0418 05:29:36.594470   15642 kuberuntime_manager.go:1038] "killPodWithSyncResult failed" err="failed to \"KillPodSandbox\" for \"e74974da-78a5-43cc-a49e-e474bcf858ae\" with KillPodSandboxError: \"rpc error: code = Unknown desc = failed to destroy network for sandbox \\\"c7fc422ea08da63c4adb4623ce672a43ecfe34142ba18515ea2f6022cbd81f71\\\": plugin type=\\\"cilium-cni\\\" name=\\\"cilium\\\" failed (delete): unable to connect to Cilium daemon: failed to create cilium agent client after 30.000000 seconds timeout: Get \\\"http:///var/run/cilium/cilium.sock/v1/config\\\": dial unix /var/run/cilium/cilium.sock: connect: no such file or directory\\nIs the agent running?\""
Apr 18 05:29:36 rukbat kubelet[15642]: E0418 05:29:36.594555   15642 kuberuntime_manager.go:1038] "killPodWithSyncResult failed" err="failed to \"KillPodSandbox\" for \"fc22ffed-b2a8-49e0-a4f9-492c8cded486\" with KillPodSandboxError: \"rpc error: code = Unknown desc = failed to destroy network for sandbox \\\"a936d96c25bcae13dbea32ecf1afec783f178fcbbe6871418db6b58718b300b2\\\": plugin type=\\\"cilium-cni\\\" name=\\\"cilium\\\" failed (delete): unable to connect to Cilium daemon: failed to create cilium agent client after 30.000000 seconds timeout: Get \\\"http:///var/run/cilium/cilium.sock/v1/config\\\": dial unix /var/run/cilium/cilium.sock: connect: no such file or directory\\nIs the agent running?\""
Apr 18 05:29:36 rukbat kubelet[15642]: E0418 05:29:36.594621   15642 pod_workers.go:1281] "Error syncing pod, skipping" err="failed to \"KillPodSandbox\" for \"e74974da-78a5-43cc-a49e-e474bcf858ae\" with KillPodSandboxError: \"rpc error: code = Unknown desc = failed to destroy network for sandbox \\\"c7fc422ea08da63c4adb4623ce672a43ecfe34142ba18515ea2f6022cbd81f71\\\": plugin type=\\\"cilium-cni\\\" name=\\\"cilium\\\" failed (delete): unable to connect to Cilium daemon: failed to create cilium agent client after 30.000000 seconds timeout: Get \\\"http:///var/run/cilium/cilium.sock/v1/config\\\": dial unix /var/run/cilium/cilium.sock: connect: no such file or directory\\nIs the agent running?\"" pod="kube-system/coredns-5d78c9869d-t77fh" podUID=e74974da-78a5-43cc-a49e-e474bcf858ae
Apr 18 05:29:36 rukbat kubelet[15642]: E0418 05:29:36.594698   15642 pod_workers.go:1281] "Error syncing pod, skipping" err="failed to \"KillPodSandbox\" for \"fc22ffed-b2a8-49e0-a4f9-492c8cded486\" with KillPodSandboxError: \"rpc error: code = Unknown desc = failed to destroy network for sandbox \\\"a936d96c25bcae13dbea32ecf1afec783f178fcbbe6871418db6b58718b300b2\\\": plugin type=\\\"cilium-cni\\\" name=\\\"cilium\\\" failed (delete): unable to connect to Cilium daemon: failed to create cilium agent client after 30.000000 seconds timeout: Get \\\"http:///var/run/cilium/cilium.sock/v1/config\\\": dial unix /var/run/cilium/cilium.sock: connect: no such file or directory\\nIs the agent running?\"" pod="kube-system/coredns-5d78c9869d-xdl8j" podUID=fc22ffed-b2a8-49e0-a4f9-492c8cded486
```

```yaml
ubuntu@rukbat:~$ cilium sysdump
🔍 Collecting sysdump with cilium-cli version: v0.13.2, args: [sysdump]
🔮 Detected Cilium installation in namespace "kube-system"
🔮 Detected Cilium operator in namespace "kube-system"
🔍 Collecting Kubernetes nodes
🔍 Collect Kubernetes nodes
🔍 Collecting Kubernetes pods
🔍 Collecting Kubernetes events
🔍 Collecting Kubernetes namespaces
🔍 Collecting Kubernetes services
🔍 Collecting Kubernetes pods summary
🔍 Collecting Kubernetes endpoints
🔍 Collecting Kubernetes network policies
🔍 Collecting Cilium network policies
🔍 Collecting Kubernetes leases
🔍 Collecting Cilium egress NAT policies
🔍 Collecting Cilium cluster-wide network policies
🔍 Collecting Cilium local redirect policies
🔍 Collecting Cilium Egress Gateway policies
🔍 Collecting Cilium identities
🔍 Collecting Cilium endpoints
🔍 Collecting Cilium nodes
🔍 Collect Kubernetes version
🔍 Collecting Cilium Node Configs
🔍 Collecting Ingresses
🔍 Collecting CiliumClusterwideEnvoyConfigs
🔍 Collecting CiliumEnvoyConfigs
🔍 Collecting Cilium BGP Peering Policies
🔍 Collecting Cilium etcd secret
🔍 Collecting Cilium LoadBalancer IP Pools
🔍 Collecting the Cilium configuration
🔍 Collecting the Cilium daemonset(s)
🔍 Collecting the Hubble daemonset
🔍 Collecting the Hubble Relay configuration
🔍 Collecting the Hubble Relay deployment
🔍 Collecting the Hubble UI deployment
🔍 Collecting the Cilium operator deployment
🔍 Collecting the 'clustermesh-apiserver' deployment
🔍 Collecting the CNI configuration files from Cilium pods
🔍 Collecting gops stats from Hubble pods
🔍 Collecting gops stats from Cilium pods
🔍 Collecting gops stats from Hubble Relay pods
⚠️ Deployment "clustermesh-apiserver" not found in namespace "kube-system" - this is expected if 'clustermesh-apiserver' isn't enabled
🔍 Collecting the CNI configmap
🔍 Collecting bugtool output from Cilium pods
🔍 Collecting profiling data from Cilium pods
⚠️ Deployment "hubble-ui" not found in namespace "kube-system" - this is expected if Hubble UI is not enabled
⚠️ Deployment "hubble-relay" not found in namespace "kube-system" - this is expected if Hubble is not enabled
🔍 Collecting logs from Cilium pods
🔍 Collecting logs from Cilium operator pods
🔍 Collecting logs from 'clustermesh-apiserver' pods
🔍 Collecting logs from Hubble pods
🔍 Collecting logs from Hubble Relay pods
🔍 Collecting bugtool output from Tetragon pods
🔍 Collecting logs from Hubble UI pods
🔍 Collecting platform-specific data
🔍 Collecting kvstore data
🔍 Collecting Hubble flows from Cilium pods
I0418 05:33:42.912633   32164 request.go:690] Waited for 1.143212642s due to client-side throttling, not priority and fairness, request: GET:https://rukbat.sagittarius.gasy.africa:6443/api/v1/namespaces/kube-system/pods?labelSelector=k8s-app%3Dcilium
⚠️ Container "cilium-agent" for pod "cilium-d22pl" in namespace "kube-system" is not running. Trying EphemeralContainer or separate Pod instead...
⚠️ EphemeralContainer "sysdump-1681796022" on pod "cilium-d22pl" in namespace "kube-system" never reached Running status (falling back to separate Pod)
⚠️ Container "cilium-agent" on pod "cilium-d22pl" in namespace "kube-system" is not running. Creating exec Pod.
⚠️ The following tasks failed, the sysdump may be incomplete:
⚠️ [12] Collecting Cilium egress NAT policies: failed to collect Cilium egress NAT policies: the server could not find the requested resource
⚠️ [13] Collecting Cilium Egress Gateway policies: failed to collect Cilium Egress Gateway policies: the server could not find the requested resource (get ciliumegressgatewaypolicies.cilium.io)
⚠️ [14] Collecting Cilium local redirect policies: failed to collect Cilium local redirect policies: the server could not find the requested resource (get ciliumlocalredirectpolicies.cilium.io)
⚠️ [20] Collecting CiliumClusterwideEnvoyConfigs: failed to collect CiliumClusterwideEnvoyConfigs: the server could not find the requested resource (get ciliumclusterwideenvoyconfigs.cilium.io)
⚠️ [21] Collecting CiliumEnvoyConfigs: failed to collect CiliumEnvoyConfigs: the server could not find the requested resource (get ciliumenvoyconfigs.cilium.io)
⚠️ [22] Collecting Cilium BGP Peering Policies: failed to collect Cilium BGP Peering policies: the server could not find the requested resource (get ciliumbgppeeringpolicies.cilium.io)
⚠️ [23] Collecting Cilium LoadBalancer IP Pools: failed to collect Cilium LoadBalancer IP Pools: the server could not find the requested resource (get ciliumbgppeeringpolicies.cilium.io)
⚠️ [28] Collecting the Hubble Relay configuration: failed to collect the Hubble Relay configuration: configmaps "hubble-relay-config" not found
⚠️ cniconflist-cilium-d22pl: unable to upgrade connection: container not found ("cilium-agent")
⚠️ gops-cilium-d22pl-memstats: failed to list processes "cilium-d22pl" ("cilium-agent") in namespace "kube-system": unable to upgrade connection: container not found ("cilium-agent")
⚠️ gops-cilium-d22pl-stack: failed to list processes "cilium-d22pl" ("cilium-agent") in namespace "kube-system": unable to upgrade connection: container not found ("cilium-agent")
⚠️ gops-cilium-d22pl-stats: failed to list processes "cilium-d22pl" ("cilium-agent") in namespace "kube-system": unable to upgrade connection: container not found ("cilium-agent")
⚠️ gops-cilium-d22pl-pprof-heap: failed to list processes "cilium-d22pl" ("cilium-agent") in namespace "kube-system": unable to upgrade connection: container not found ("cilium-agent")
⚠️ gops-cilium-d22pl-pprof-cpu: failed to list processes "cilium-d22pl" ("cilium-agent") in namespace "kube-system": unable to upgrade connection: container not found ("cilium-agent")
⚠️ hubble-flows-cilium-d22pl: failed to collect hubble flows for "cilium-d22pl" in namespace "kube-system": command terminated with exit code 1: failed to connect to 'unix:///var/run/cilium/hubble.sock': connection error: desc = "transport: error while dialing: dial unix /var/run/cilium/hubble.sock: connect: no such file or directory"

⚠️ Please note that depending on your Cilium version and installation options, this may be expected
🗳 Compiling sysdump
✅ The sysdump has been saved to /home/ubuntu/cilium-sysdump-20230418-053341.zip
```

```
/opt/cni/bin/cilium-cni
```
> Returns
```yaml
Cilium CNI plugin 1.13.1 a6be57eb 2023-03-15T19:39:01+01:00 go version go1.19.6 linux/arm64
CNI protocol versions supported: 0.1.0, 0.2.0, 0.3.0, 0.3.1, 0.4.0, 1.0.0
```
