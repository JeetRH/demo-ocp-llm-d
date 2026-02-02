# To copy a oc-mirror file from local to a bastion host
rsync -Pav ./oc-mirror.rhel9.tar.gz lab-user@bastion.sxxpx.sandbox7856.opentlc.com:~/

# To copy the mapping.txt file from bastion host to local


# Notes for a Disconnected install

Create `isc.yaml` - edit the copy for your needs

```sh
[ -d scratch ] || mkdir scratch
cp gitops/disconnected/isc*.yaml scratch/

# edit scratch/isc.yaml
```

# IMP

# To get pull-secret.txt use link https://docs.redhat.com/en/documentation/openshift_container_platform/4.20/html/disconnected_environments/installing-mirroring-disconnected

# To get package channels: 
# RHOAI: oc get packagemanifest rhods-operator -n openshift-marketplace -o jsonpath='{.status.channels[*].name}'
# ServiceMesh: oc get packagemanifest servicemeshoperator3 -n openshift-marketplace -o jsonpath='{.status.channels[*].name}'
# NFD: oc get packagemanifest nfd -n openshift-marketplace -o jsonpath='{.status.channels[*].name}'
# Nvidia GPU: oc get packagemanifest gpu-operator-certified -n openshift-marketplace -o jsonpath='{.status.channels[*].name}'
# RH Connectivity: oc get packagemanifest rhcl-operator -n openshift-marketplace -o jsonpath='{.status.channels[*].name}'
# RH Build of Kueue: oc get packagemanifest kueue-operator -n openshift-marketplace -o jsonpath='{.status.channels[*].name}'


Create `mapping.txt`

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
