# rpi4-k8s
RaspberryPi 4 running a Kubernetes Cluster

# pi4-cluster



# :wheel_of_dharma: Kubernetes on Metal

L'exercice dans cette section est de préparer sa grappe (cluster) que vous installerez sur du métal.

## :book: Table of Content

| Chapters                                  | Descriptions                                                       |
|-------------------------------------------|--------------------------------------------------------------------|
| [:o2: Naming](#o2-naming)                 | Creating and naming own cluster                                    |
| [:a: The preparation](#a-the-preparation) | Preparer a node                                                    |
| [:b: The components](#b-the-components)   | Install the components forming the cluster (tools and services)    |
| [:ab: The plans](#ab-the-plans)           | Deploy `kubernetes` control and data plans                         |
| [:joystick: Controlling the cluster](#joystick-controlling-the-cluster) |  Controlling the cluster remotely from a user station |
| [:floppy_disk: Data storing ](#floppy_disk-data-storing) | Store data on disks synchronized by a Storage Area Network (SAN) |
| [:rocket: The services](#rocket-the-services) | Enable applications to be delivered through services |
| [:chains: The network Infrastructure](#chains-network-infrastructure) | Defines the environment where the cluster evolves |


## :o2: Nommage

:busts_in_silhouette: Group Activity

Your cluster includes 4 nodes (servers) that you will first need to name

- [ ] Name your cluster :writing_hand:	 
- [ ] Create your DNS Zone with your domain provider (NS Record).
- [ ] After determining the name of your cluster:

:sagittarius: Name: sagittarius.gasy.africa

| Node Type     | Hostname                        |  IP                | Specs                 |
|---------------|---------------------------------|--------------------|-----------------------|
| control plane | rukbat.sagittarius.gasy.africa  | `192.168.1.200/24` | 4GB Ram,      4cores  |
| data plane    | nunki.sagittarius.gasy.africa   | `192.168.1.201/24` | 4GB Ram,      4cores  |
| data plane    | alnasl.sagittarius.gasy.africa  | `192.168.1.202/24` | 4GB Ram,      4cores  |
| data plane    | ascella.sagittarius.gasy.africa | `192.168.1.203/24` | 4GB Ram,      4cores  |

## :a: The preparation

:bust_in_silhouette: Individual Activity

#### :round_pushpin: The Node

- [ ] Its name
- [ ] Its `swap`
- [ ] Its `bridge`
- [ ] Its extra `modules` (Raspberry Pi)

[Preparation](.docs/Preparation.md) :writing_hand:	


## :b: The components

:bust_in_silhouette: Individual Activity

<img src="images/kube-cluster-component-topology.png" width=50% height=50% ></img>

#### :round_pushpin: Installing tools

- [ ] Instal tools [:ice_cube: kubetools](.doc/kube-tools.md) :writing_hand: enabling the cluster management, like `kubeadm` and `kubectl`

#### :round_pushpin: Installing services

- [ ] Install the [CNI](https://kubernetes.io/docs/concepts/cluster-administration/networking/) (Container Network Interface) managed by the Service [:droplet: Kubelet](.doc/kubelet.md) :writing_hand:	 

- [ ] Install the [CRI](https://kubernetes.io/docs/setup/production-environment/container-runtimes/) (Container Runtime Interface)  defined by the [OCI](https://opencontainers.org) and managed by the Services:

| Services                                                  | 
|-----------------------------------------------------------|
| [:whale: Docker](.doc/docker.md) :writing_hand:           |
| [:whale2: Containerd](.doc/containerd.md) :writing_hand:	|

## :ab: Les plans

:busts_in_silhouette: activité en groupe

##### :control_knobs: Le plan de contrôle (control plane)

:warning: Assurez vous que votre domaine a tous les noms de serveurs (noeuds) liés à votre grappe

- [ ] Installer le [plan de contrôle](.doc/control-plane.md) :writing_hand:	 
- [ ] Initialiser le réseau de gousses (pod network) en utilisant le [CNI plugin](.doc/cni-plugin.md) :writing_hand: Cilium

:star2: Mais qu'est-ce qu'un [manifeste](.doc/manifest.md) :writing_hand: ?

##### :abacus: Les plan de données (data plane)

- [ ] Permettre aux autres noeux de [joindre](.doc/data-plane.md) :writing_hand: la grappe.


## :joystick: Contrôller la grappe

:busts_in_silhouette: activité en groupe

:round_pushpin: Installer les [:toolbox: outils](.doc/kube-tools-pc.md) :writing_hand: sur son PC pour gérer la grappe

| outil     | Description          |
|-----------|----------------------|
| `kubectl` | Contrôller la grappe |
| :package: `helm`    | Gestionnaire de librairies (Package Manager) de Kubernetes |
| :level_slider: `lens`    | IDE permettant la gestion de Kubernetes |

:round_pushpin:  Mettre le fichier de `configuration` en contexte pour permettre la gestion de la grappe à distance

- [ ] Installer le fichier de configuration dans un [contexte](.doc/contexts.md) :writing_hand:	 `Kubernetes`

:round_pushpin:  Tester sa connexion à la grappe

- [ ] Vérifier que le context courant pointe bien sur `kubernetes-admin@kubernetes`

```
k config get-contexts
```
> Returns :
```yaml
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   
```

- [ ] Vérifier que la grappe est complète

```
k get nodes
```
> Returns :
```yaml
NAME     STATUS   ROLES           AGE   VERSION
nunki    Ready    <none>          18h   v1.27.1
rukbat   Ready    control-plane   22h   v1.27.1
```

- [ ] Visualiser la grappe dans :level_slider: `Lens`

:warning: Attention à ne pas installer d'applications, il manque la partie stockage.

## :cl: La classe de stockage (Storage Class)

#### :roll_of_paper: Le périphérique (Block Device)

:bust_in_silhouette: activité individuelle à appliquer à partir des :abacus: plans de données: 

Le but de l'exercice est de créer un disque ou plutôt un volume logique et de le mettre en réseau avec `iSCSI`


- [ ] Installer le Service [:minidisc: iSCSI](.doc/iscsi) :writing_hand:	permettant une implémentation [SAN](https://en.wikipedia.org/wiki/Storage_area_network) du protocole d'[`open-iscsi`](http://www.open-iscsi.com/)
- [ ] Créer le volume logique [:cd: lvm](.doc/iscsi/lvm.md) :writing_hand:	appellé `iscsi-lv`
- [ ] Préparer le [périphérique](.doc/iscsi/blockdevice.md) en mode block et l'enregistrer dans un fichier `blockdevice-xxxx-xxx-xxx.md` du répertoire de votre grappe.

#### :floppy_disk: Le stockage 

[:bookmark: Container Storage](https://landscape.cncf.io/card-mode?category=cloud-native-storage&grouping=category)

:busts_in_silhouette: activité en groupe à appliquer à partir du :control_knobs: plan de contrôle: 

:round_pushpin: Le modèle de stockage de kubernetes 

<img src="images/kube-storage-model.png" width="382" height="189"></img>

Le standard Kubernetes permet aux founisseurs d'infrastructure d'utiliser leur propre moteur de stockage pour conteneur ou encore `CS` (Container Storage). Pour cela, la norme Kubernetes fournit une interface [CSI](https://kubernetes-csi.github.io/docs). Cette interface permet d'utiliser des [Polices](https://kubernetes-csi.github.io/docs/drivers.html) ou `Plugin` en fonction de l'environnement info-nuagique ou métal.

Dans notre environnement, nous allons choisir [openEBS](https://openebs.io) et son `Moteur de Stockage` [cStor](https://openebs.io/docs/user-guides/cstor) comme `CSI Plugin`

<img src="images/1-config-sequence.svg" width="657" height="145"> </img>

:round_pushpin:  [Installer](.doc/openebs/install.md):pinching_hand: le `CSI Plugin` [openEBS](https://openebs.io)

:round_pushpin:  Créer la **classe de stockage (sc)** `standard` 

- [ ] Installer les périphériques préalablement définis ci-dessus 
- [ ] Créer d'un réservoir de stockage `pool` avec les :three: périphériques
- [ ] Créer la **classe de stockage (sc)** `standard` avec le réservoir de stockage `pool`

Utiliser les [operations d'administration](.doc/openebs/adminop.md):pinching_hand:. suivante pour la création de la  **classe de stockage (sc)** `standard`

:round_pushpin:  Vous pouvez maintenant utiliser la **classe de stockage (sc)** `standard` définie par `défaut`

:bulb: Vérifier que la classe de stockage par **défaut** est bien **standard**

```
kubectl get storageclasses        
```
> Retourne :
```yaml
NAME                 PROVISIONER            RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
openebs-device       openebs.io/local       Delete          WaitForFirstConsumer   false                  19m
openebs-hostpath     openebs.io/local       Delete          WaitForFirstConsumer   false                  19m
standard (default)   cstor.csi.openebs.io   Delete          Immediate              true                   2m35s
```


## :rocket: Les Services

[:bookmark: Service](https://kubernetes.io/fr/docs/concepts/services-networking/service/)


:busts_in_silhouette: activité en groupe

##### :control_knobs: Le plan de contrôle (control plane)

- [ ]  [Installer](.doc/openelb.md):pinching_hand: [openelb](https://openelb.io/). 
- [ ] 

## :clapper: Les Applications

:bust_in_silhouette: activité individuelle

Chaque étudiant aura à installer son application dans la grappe

- [ ]  Installer les applications avec [LENS](https://k8slens.dev/)
- [ ] Activer :rocket: le service en appliquant les annotations reliés à l'équilibreur de charge `LoadBalancer` Porter en fonction des examples de [Configuration](https://github.com/CollegeBoreal/Tutoriels/tree/main/2.MicroServices/3.Orchestration/1.Kubernetes/C.Cluster/0.Local/6.K8s/apps) d'applications suivantes
- [ ] Attacher l'application à votre nom de domaine

## :chains: L'infrastructure réseau

- [ ]  [Réseau Local](.doc/network.md):pinching_hand:

# References


