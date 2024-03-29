## OpenEBS - LVM-LocalPV CSI Driver
[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2Fopenebs%2Flvm-localpv.svg?type=shield)](https://app.fossa.io/projects/git%2Bgithub.com%2Fopenebs%2Flvm-localpv?ref=badge_shield)
[![CII Best Practices](https://bestpractices.coreinfrastructure.org/projects/3523/badge)](https://bestpractices.coreinfrastructure.org/en/projects/4548)
[![Slack](https://img.shields.io/badge/chat!!!-slack-ff1493.svg?style=flat-square)](https://kubernetes.slack.com/messages/openebs)
[![Community Meetings](https://img.shields.io/badge/Community-Meetings-blue)](https://hackmd.io/yJb407JWRyiwLU-XDndOLA?view)
[![Go Report](https://goreportcard.com/badge/github.com/openebs/lvm-localpv)](https://goreportcard.com/report/github.com/openebs/lvm-localpv)
[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2Fopenebs%2Flvm-localpv.svg?type=shield)](https://app.fossa.com/projects/git%2Bgithub.com%2Fopenebs%2Flvm-localpv?ref=badge_shield)


| [![Linux LVM2](https://github.com/openebs/website/blob/main/website/public/images/png/LVM_logo_1.png "Linux LVM2")](https://github.com/openebs/website/blob/main/website/public/images/png/LVM_logo_1.png) | The OpenEBS LVM-LocalPV Data-Engine is a mature and well deployed production grade CSI driver for dynamically provisioning Node Local Volumes into a K8s cluster utilizing the LINUX LVM2 Data / storage Mgmt stack as the storage backend. It integrates LVM2 into the OpenEBS platform and exposes many LVM2 services and capabilities.   |
| :---  | :--- |
<BR>

## Overview
LVM-LocalPV CSI Driver becasme GA in August 2021 (with the release v0.8.0). It is now a a very mature product and a core component of the OpenEBS storage platform.
Due to the major adoption of LVM-LocalPV (+50,000 users), this Data-Engine is now being unified and integrated into the core OpenEBS Storage platform; instead of being maintained as an external Data-Engine within our project.

Our [2024 Roadmap is here](https://github.com/openebs/openebs/blob/main/ROADMAP.md). It defines a rich set of new featrues, which covers the integration of LVM-LocalPV into the core OpenEBS platform.<br>
Please review this roadmp and feel free to pass back any feedback on it, as well as recommend and suggest new ideas regarding LVM-LocalPV. We welcome all your feedback.
<br>
<BR>

> **LVM-LocalPV is very popular** : Live OpenEBS systems actively report back product metrics every day, to our Global Anaytics metrics engine (unless disabled by the user).
> Here are our key project popularity metrics as of: 01 Mar 2024 <BR>
>
> :rocket: &nbsp; OpenEBS is the #1 deployed Storage Platform for Kubernetes <BR>
> :zap: &nbsp; LVM-LocalPV is the 3rd most deployed Data-Engine within the platform <BR>
> :sunglasses: &nbsp; LVM-LocalPV has +50,000 Daily Acive Users <BR>
> :sunglasses: &nbsp; LVM-LocalPV has +120,000 Global instllations <BR>
> :floppy_disk: &nbsp; +49 Million OpenEBS Volumes have been deployed globally <BR>
> :tv: &nbsp; We have +8 Million Global OpenEBS installations <BR>
> :star: &nbsp; We are the [#1 GitHub Star ranked](https://github.com/openebs/website/blob/main/website/public/images/png/github_star-history-2024_Feb_1.png) K8s Data Storage platform <BR>

<BR>

## Dev Activity dashboard
![Alt](https://repobeats.axiom.co/api/embed/baab8c2a9d1606494ab32714cbf91b65845a6001.svg "Repobeats analytics image")


## Project info
The orignal v1.0 dev roadmap [is here ](https://github.com/orgs/openebs/projects/30). This tracks our base historical engineering development work and is now somewhat out of date. We will be publish an updated 2024 Unified Roadmp soon, as ZFS-LoalPV is now being integrated and unified into the core OpenEBS storage platform.<BR>

<BR>

## Usage and Deployment

## Prerequisites


> [!IMPORTANT]
> Before installing the LVM-LocalPV driver please make sure your Kubernetes Cluster meets the following prerequisites:
> 1. All the nodes must have LVM2 utils package installed
> 2. All the nodes must have dm-snapshot Kernel Module loaded - (Device Mapper Snapshot)
> 4. You have access to install RBAC components into kube-system namespace. The OpenEBS LVM driver components are installed in kube-system namespace to allow them to be flagged as system critical components.
<BR>

> [!NOTE]
> - Full LVM2 dynamic provisioning is now supported <BR>
> - All Volume Groups (VG), Physical Volumes (PV) and Logical Volumes (LV) willbe dynamically provisionsed for you by OpenEBS <BR>
> - There is need  to manually provison any PV, VG or LV's  <BR>
> - Dynamic RAID provisioning is still in development. RAID LV's must be deployed via the manual pre configuiration method <BR>

### Supported System

> | Name | Version |
> | :--- | :--- |
> | K8S | 1.20+ |
> | Distro | Alpine, Arch, CentOS, Debian, Fedora, NixOS, SUSE, Talos, RHEL, Ubuntu |
> | Kernel | oldest supported kernel is 2.6 |
> | LVM2 | 2.03.21 |
> | Min RAM | LVM2 is a kernel native module. It is very efficent and fast. It has no strict memory requirements |
> Stability | LVM2 is extremly stable and very mature. The Kernel was released ~2005. It exists in most LINUX distros |
<BR>
<BR>

## Setup

Find the disk which you want to use for the LVM-LocalPV. Note: For testing you can use the loopback device.

```
truncate -s 1024G /tmp/disk.img
sudo losetup -f /tmp/disk.img --show
```

> [!NOTE]
> - This is the old maual config process <BR>
> - LVM-LocalPV will num dynamically provision the VG fro you <BR>
> - The PV, VG and LV names will be dynamically provisioned by OpenEBS LVM-LocalPV as K8s unique entities (for safety, you cannot provide your own PV, VG or LV names)

Create the Volume group on all the nodes, which will be used by the LVM2 Driver for provisioning the volumes

```
sudo pvcreate /dev/loop0
sudo vgcreate lvmvg /dev/loop0       ## here lvmvg is the volume group name to be created
```
<BR>
<BR>

## Installation

Install the latest release of OpenEBS LVM2 LVM-LocalPV driver by running the following command. Note: All nodes must be running the same verison of LVM-LocalPV, LMV2, device-mapper & dm-snapshot.

```
$ kubectl apply -f https://openebs.github.io/charts/lvm-operator.yaml
```

If you want to fetch a versioned manifest, you can use the manifests for a
specific OpenEBS release version, for example:

```
$ kubectl apply -f https://raw.githubusercontent.com/openebs/charts/gh-pages/versioned/3.0.0/lvm-operator.yaml
```

**NOTE:** For some Kubernetes distributions, the `kubelet` directory must be changed at all relevant places in the YAML powering the operator (both the `openebs-lvm-controller` and `openebs-lvm-node`).

- For `microk8s`, we need to change the kubelet directory to `/var/snap/microk8s/common/var/lib/kubelet/`, we need to replace `/var/lib/kubelet/` with `/var/snap/microk8s/common/var/lib/kubelet/` at all the places in the operator yaml and then we can apply it on microk8s.

- For `k0s`, the default directory (`/var/lib/kubelet`) should be changed to `/var/lib/k0s/kubelet`.

- For `RancherOS`, the default directory (`/var/lib/kubelet`) should be changed to `/opt/rke/var/lib/kubelet`.

Verify that the LVM driver Components are installed and running using below command :

```
$ kubectl get pods -n kube-system -l role=openebs-lvm
```

Depending on number of nodes, you will see one lvm-controller pod and lvm-node daemonset running
on the nodes.

```
NAME                       READY   STATUS    RESTARTS   AGE
openebs-lvm-controller-0   5/5     Running   0          35s
openebs-lvm-node-54slv     2/2     Running   0          35s
openebs-lvm-node-9vg28     2/2     Running   0          35s
openebs-lvm-node-qbv57     2/2     Running   0          35s

```
Once LVM driver is successfully installed, we can provision volumes.

### Deployment


#### 1. Create a Storage class

```
$ cat sc.yaml

apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: openebs-lvmpv
parameters:
  storage: "lvm"
  volgroup: "lvmvg"
provisioner: local.csi.openebs.io
```

Check the doc on [storageclasses](docs/storageclasses.md) to know all the supported parameters for LVM-LocalPV

##### VolumeGroup Availability

If LVM volume group is available on certain nodes only, then make use of topology to tell the list of nodes where we have the volgroup available.
As shown in the below storage class, we can use allowedTopologies to describe volume group availability on nodes.

```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: openebs-lvmpv
allowVolumeExpansion: true
parameters:
  storage: "lvm"
  volgroup: "lvmvg"
provisioner: local.csi.openebs.io
allowedTopologies:
- matchLabelExpressions:
  - key: kubernetes.io/hostname
    values:
      - lvmpv-node1
      - lvmpv-node2
```

The above storage class tells that volume group "lvmvg" is available on nodes lvmpv-node1 and lvmpv-node2 only. The LVM driver will create volumes on those nodes only.

Please note that the provisioner name for LVM driver is "local.csi.openebs.io", we have to use this while creating the storage class so that the volume provisioning/deprovisioning request can come to LVM driver.

#### 2. Create the PVC

```
$ cat pvc.yaml

kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: csi-lvmpv
spec:
  storageClassName: openebs-lvmpv
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 4Gi
```

Create a PVC using the storage class created for the LVM driver.

#### 3. Deploy the application

Create the deployment yaml using the pvc backed by LVM storage.

```
$ cat fio.yaml

apiVersion: v1
kind: Pod
metadata:
  name: fio
spec:
  restartPolicy: Never
  containers:
  - name: perfrunner
    image: openebs/tests-fio
    command: ["/bin/bash"]
    args: ["-c", "while true ;do sleep 50; done"]
    volumeMounts:
       - mountPath: /datadir
         name: fio-vol
    tty: true
  volumes:
  - name: fio-vol
    persistentVolumeClaim:
      claimName: csi-lvmpv
```

After the deployment of the application, we can go to the node and see that the lvm volume is being used
by the application for reading/writting the data and space is consumed from the LVM. Please note that to check the provisioned volumes on the node, we need to run `pvscan --cache` command to update the lvm cache and then we can use lvdisplay and all other lvm commands on the node.

#### 4. Deprovisioning

for deprovisioning the volume we can delete the application which is using the volume and then we can go ahead and delete the pv, as part of deletion of pv this volume will also be deleted from the volume group and data will be freed.

```
$ kubectl delete -f fio.yaml
pod "fio" deleted
$ kubectl delete -f pvc.yaml
persistentvolumeclaim "csi-lvmpv" deleted
```

Features
---

- [x] Access Modes
    - [x] ReadWriteOnce
    - ~~ReadOnlyMany~~
    - ~~ReadWriteMany~~
- [x] Volume modes
    - [x] `Filesystem` mode
    - [x] [`Block`](docs/raw-block-volume.md) mode
- [x] Supports fsTypes: `ext4`, `btrfs`, `xfs`
- [x] Volume metrics
- [x] Topology
- [x] [Snapshot](docs/snapshot.md)
- [ ] Clone
- [x] [Volume Resize](docs/resize.md)
- [x] [Thin Provision](docs/thin_provision.md)
- [ ] Backup/Restore
- [ ] Ephemeral inline volume

### Limitation
- Resize of volumes with snapshot is not supported


## License
[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2Fopenebs%2Flvm-localpv.svg?type=large)](https://app.fossa.com/projects/git%2Bgithub.com%2Fopenebs%2Flvm-localpv?ref=badge_large)
