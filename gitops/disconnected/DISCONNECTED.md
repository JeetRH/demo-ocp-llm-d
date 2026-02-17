To copy a oc-mirror file from local to a bastion host
- rsync -Pav ./oc-mirror.rhel9.tar.gz lab-user@bastion.sxxpx.sandbox7856.opentlc.com:~/

We need a pull secret in order to retrieve the images from registry.redhat.io. To get pull-secret.txt use the link 
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.20/html/disconnected_environments/installing-mirroring-disconnected#installation-adding-registry-pull-secret_installing-mirroring-disconnected

To get package channels: 
- RHOAI: oc get packagemanifest rhods-operator -n openshift-marketplace -o jsonpath='{.status.channels[*].name}'
- ServiceMesh: oc get packagemanifest servicemeshoperator3 -n openshift-marketplace -o jsonpath='{.status.channels[*].name}'
- NFD: oc get packagemanifest nfd -n openshift-marketplace -o jsonpath='{.status.channels[*].name}'
- Nvidia GPU: oc get packagemanifest gpu-operator-certified -n openshift-marketplace -o jsonpath='{.status.channels[*].name}'
- RH Connectivity: oc get packagemanifest rhcl-operator -n openshift-marketplace -o jsonpath='{.status.channels[*].name}'
- RH Build of Kueue: oc get packagemanifest kueue-operator -n openshift-marketplace -o jsonpath='{.status.channels[*].name}'


To run oc-mirror and get the list of images use the following instructions:

Create `isc.yaml` - edit the copy for your needs and copy it in the scratch folder

```sh
[ -d scratch ] || mkdir scratch
cp gitops/disconnected/isc*.yaml scratch/
```

```sh
REGISTRY=registry.redhat.io

# NOTE: replace with 'quay.io' if oc mirror loses its mind
# REGISTRY=quay.io

oc-mirror \
  -c scratch/isc-np.yaml \
  --workspace file:///${PWD}/scratch/oc-mirror \
  docker://"${REGISTRY}" \
  --v2 \
  --dry-run \
  --authfile scratch/pull-secret.txt
```

Create `images.txt` - a list of images to copy

```sh
DATE=$(date +%Y-%m-%d)
sed '
  s@^docker://@@g
  s@=docker://'"${REGISTRY}"'.*@@g
  /localhost/d' \
    scratch/oc-mirror/working-dir/dry-run/mapping.txt \
    > scratch/images-"${DATE}".txt
```
