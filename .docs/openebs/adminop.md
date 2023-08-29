# OpenEBS - Administration Operations

:control_knobs: On the control plane

## :a: Block Devices - BlockDevice CR (Custom Resource)

- [ ] Create devices :roll_of_paper:

> Execute `kubectl` commands referring to `blockdevice-****-****-****-****.md` files

* Create one file per node and put it in the cluster directory following the below pattern:

- [ ] The file name should be in the form `blockdevice-xxxx-xxxx-xxx-xxx.yaml`

- [ ] Execute the file (replace with the correct file name `blockdevice-xxxx-xxxx-xxx-xxx.yaml`)

```
kubectl apply --namespace openebs --filename blockdevice-c3b2e1a8-85b0-fc4e-8caf-5ba8c2fd4444.yaml
```
> blockdevice.openebs.io/blockdevice-c3b2e1a8-85b0-fc4e-8caf-5ba8c2fd4444 created

:warning: Make sure the file has the correct `iscsi-lv` device information

:bulb: There should be :three: files to execute

- [ ] Check device creation

> blockdevices or bd

```
kubectl get blockdevices --namespace openebs   
```
> Returns: 
<pre>
NAME                                               NODENAME   SIZE          CLAIMSTATE   STATUS   AGE
blockdevice-c3b2e1a8-85b0-fc4e-8caf-5ba8c2fd4444   nunki      42949672960   Unclaimed    Active   11s
</pre>

> blockdeviceclaims or bdc

```
kubectl get blockdeviceclaims.openebs.io --namespace openebs
```
> No resources found in openebs namespace.

## :b: Storage Pool

:round_pushpin: Create `cStor Storage Pool` (csp)

- [ ] edit the configuration file below changing the `block device` device information

```yaml
apiVersion: cstor.openebs.io/v1
kind: CStorPoolCluster
metadata:
 name: cstor-storage
 namespace: openebs
spec:
 pools:
   - nodeSelector:
       kubernetes.io/hostname: "bellatrix" # ME CHANGER VITE
     dataRaidGroups:
       - blockDevices:
           - blockDeviceName: "blockdevice-385b4ca0-8647-4d15-aec8-6175e9a23cf7" # ME CHANGER VITE
     poolConfig:
       dataRaidGroupType: "stripe"

   - nodeSelector:
       kubernetes.io/hostname: "saiph" # ME CHANGER VITE
     dataRaidGroups:
       - blockDevices:
           - blockDeviceName: "blockdevice-3fa7d473-d0f1-4532-bcd4-a402241eeff1" # ME CHANGER VITE
     poolConfig:
       dataRaidGroupType: "stripe"

   - nodeSelector:
       kubernetes.io/hostname: "rigel" # ME CHANGER VITE
     dataRaidGroups:
       - blockDevices:
           - blockDeviceName: "blockdevice-7e848c90-cca2-4ef4-9fdc-90cff05d5bb5" # ME CHANGER VITE
     poolConfig:
       dataRaidGroupType: "stripe"
---
```

:round_pushpin: Sauveguarder le fichier `cspc-single.yaml`

- [ ] après avoir modifier les périphériques sauvegarder sous le nom `cspc-single.yaml` dans le répertoire de votre grappe

- [ ] Exécuter la commande `kubectl` à partir du fichier.

:round_pushpin: Save `cspc-single.yaml` file

- [ ] after editing the devices save the file under the name `cspc-single.yaml` in your cluster directory

- [ ] Execute the `kubectl` command from the file.

```
kubectl apply --filename cspc-single.yaml
```

:round_pushpin: Check that devices move to `Claimed` state

```
$ kubectl get blockdevices --namespace openebs
```
> Returns:
```yaml
NAME                                               NODENAME    SIZE        CLAIMSTATE   STATUS   AGE
blockdevice-23e1292d-32f5-4528-8f7f-3abaee070a03   bellatrix   102687672   Claimed      Active   16m
blockdevice-3fa7d473-d0f1-4532-bcd4-a402241eeff1   saiph       102687672   Claimed      Active   16m
blockdevice-7e848c90-cca2-4ef4-9fdc-90cff05d5bb5   rigel       102687672   Claimed      Active   16m
```

## :ab: [Storage Classes](https://kubernetes.io/docs/concepts/storage/storage-classes/)

:round_pushpin: Créer la **Class de Stockage** `standard` 

- [ ] après avoir modifié la valeur du champ `ReplicaCount` au nombre de noeuds sur la grappe (idéalement :three:)

:round_pushpin: Create the `standard` **Storage Class**

- [ ] after editing the `ReplicaCount` value field to the number of nodes on the cluster (ideally :three:)

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: standard
provisioner: cstor.csi.openebs.io
allowVolumeExpansion: true
parameters:
  cas-type: cstor
  # cstorPoolCluster should have the name of the CSPC
  cstorPoolCluster: cstor-storage
  # replicaCount should be <= no. of CSPI
  replicaCount: "3"
---
```

- [ ] save as `csi-cstor-sc.yaml` in your cluster directory
- [ ] Execute the `kubectl` command from the file.

```
kubectl apply --filename csi-cstor-sc.yaml
```
> storageclass.storage.k8s.io/standard created

:round pushpin: The default `standard` storage class

- [ ] Check the `standard` storage class

```
kubectl get storageclass standard
```
> Returns :
<pre>
NAME                        PROVISIONER                                                RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
standard                    cstor.csi.openebs.io                                       Delete          Immediate              true                   4s
</pre>

- [ ] Apply default storage to **standard**

```
kubectl patch storageclass standard -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```
> storageclass.storage.k8s.io/standard patched


# [:back:](../../#floppy_disk-storage)

# References

- [ ] [OpenEBS: cStor storage engine on KVM](https://technology.amis.nl/platform/openebs-cstor-storage-engine-on-kvm)
- [ ] [Mayastor is fastest open source storage, Intel says. So where are the numbers?](https://blocksandfiles.com/2021/03/08/intel-says-mayastor-is-fastest-open-source-storage)
- [ ] [What do you see when you put container storage firms into the sorting triangle?](https://blocksandfiles.com/2021/03/05/data-storage-news-digest-container-storage/)

- [ ] [cStor Operators - Quick Guide](https://github.com/openebs/cstor-operators/blob/develop/docs/quick.md)
    - [ ] [SPC based cStor Guide - deprecated](https://openebs.io/docs/deprecated/spc-based-cstor)

- [ ] [CSTOR Storage Engine Components](https://github.com/openebs/openebsctl/tree/develop/docs/cstor)
- [ ] [OpenEBSCTL is a kubectl plugin to manage OpenEBS storage components.](https://github.com/openebs/openebsctl)
```
   $ kubectl krew list
   PLUGIN   VERSION
   krew     v0.4.3
   openebs  v0.5.0
```

:bulb: A required privilege is not held by the client.

```
PS> kubectl krew install openebs
Updated the local copy of plugin index.
Installing plugin: openebs
Installed plugin: openebs
\
 | Use this plugin:
 |      kubectl openebs
 | Documentation:
 |      https://github.com/openebs/openebsctl
 | Caveats:
 | \
 |  | * This plugin works with newer OpenEBS releases, where volumes are
 |  |   provisioned by CSI.
 |  | * For resources that are not in default namespace, you must specify
 |  |   -n/--namespace explicitly (the current namespace setting is not
 |  |   yet used).
 | /
/
[31;1mWARNING: [0mYou installed plugin "openebs" from the krew-index plugin repository.
   These plugins are not audited for security by the Krew maintainers.
   Run them at your own risk.
```

```
filterconfigs:
  - key: os-disk-exclude-filter
    name: os disk exclude filter
    state: true
    exclude: "/,/etc/hosts,/boot"
  - key: vendor-filter
    name: vendor filter
    state: true
    include: ""
    exclude: "CLOUDBYT,OpenEBS"
  - key: path-filter
    name: path filter
    state: true
    include: ""
    exclude: "loop,fd0,sr0,/dev/ram,/dev/dm-,/dev/md,/dev/rbd,/dev/zd"
```

