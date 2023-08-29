# OpenEBS :package:

- [ ] [ Installation and Getting Started ](https://github.com/openebs/openebs#installation-and-getting-started)

## :o2: Installation

#### :round_pushpin: using `openebs operator`

```
kubectl apply -f https://openebs.github.io/charts/openebs-operator.yaml
```

#### :round_pushpin: using helm :package:

- [ ] Install the repository

```
helm repo add openebs https://openebs.github.io/charts
```
> "openebs" has been added to your repositories

- [ ] Update repositories

```
helm repo update
```
> Returns :
```yaml
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "bitnami" chart repository
Update Complete. ⎈Happy Helming!⎈
```

- [ ] Install `openebs` in the `openebs` namespace by enabling `CSTOR`

```
helm install openebs --namespace openebs openebs/openebs --set cstor.enabled=true --create-namespace
```
> Returns :
```yaml
NAME: openebs
LAST DEPLOYED: Sun Apr 16 10:27:51 2023
NAMESPACE: openebs
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Successfully installed OpenEBS.

Check the status by running: kubectl get pods -n openebs

The default values will install NDM and enable OpenEBS hostpath and device
storage engines along with their default StorageClasses. Use `kubectl get sc`
to see the list of installed OpenEBS StorageClasses.

**Note**: If you are upgrading from the older helm chart that was using cStor
and Jiva (non-csi) volumes, you will have to run the following command to include
the older provisioners:

helm upgrade openebs openebs/openebs \
        --namespace openebs \
        --set legacy.enabled=true \
        --reuse-values

For other engines, you will need to perform a few more additional steps to
enable the engine, configure the engines (e.g. creating pools) and create 
StorageClasses. 

For example, cStor can be enabled using commands like:

helm upgrade openebs openebs/openebs \
        --namespace openebs \
        --set cstor.enabled=true \
        --reuse-values

For more information, 
- view the online documentation at https://openebs.io/docs or
- connect with an active community on Kubernetes slack #openebs channel.
```

## :o: Check the installation

```
helm ls --namespace openebs
```
> Returns :
```yaml 
NAME   	NAMESPACE	REVISION	UPDATED                                	STATUS  	CHART        	APP VERSION
openebs	openebs  	1       	2023-04-16 10:27:51.952551904 +0000 UTC	deployed	openebs-3.5.0	3.5.0      
```

# [:back:](../../README.md#floppy_disk-storage)
