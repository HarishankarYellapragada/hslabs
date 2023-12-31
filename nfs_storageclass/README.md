# NFS-Client-Provisioner Setup Guide

This guide walks you through the process of adding an NFS client provisioner to your Kubernetes cluster.

## Table of Contents
- [Prerequisites](#prerequisites)
- [Add Helm Repository](#add-helm-repository)
- [Install the Chart](#install-the-chart)
- [Verify Storage Class](#verify-storage-class)
- [Set Default Storage Class](#set-default-storage-class)

---

## Prerequisites

![image](https://github.com/HarishankarYellapragada/hslabs/assets/14257200/eb8f4dcf-9ac5-463c-a569-eafae73f6f41)

- Kubernetes cluster up and running
- Helm installed
- `kubectl` installed and configured to interact with your cluster

---

## Add Helm Repository

Firstly, add the required Helm repository to fetch the nfs-client-provisioner charts.

Open your terminal and execute the following command:

```
helm repo add nfs-subdir-external-provisioner https://kubernetes-sigs.github.io/nfs-subdir-external-provisioner/
```

For more information, check the project [here on GitHub](https://github.com/kubernetes-sigs/nfs-subdir-external-provisioner).

---

## Install the Chart

Run the following command to install the nfs-client-provisioner. Replace the `nfs.server` and `nfs.path` with your specific settings.

```
helm install nfs-subdir-external-provisioner nfs-subdir-external-provisioner/nfs-subdir-external-provisioner \
    --set nfs.server=192.168.178.140 \
    --set nfs.path=/volume1/CLOUDNATIVE1
```

---

## Verify Storage Class

Confirm that the storage class has been added by running the following command:

```
kubectl get storageclass
```

Note that the added storage class will not be the default initially.

---

## Set Default Storage Class

To set the nfs-client-provisioner storage class as the default, first change the current default class to non-default.

```
kubectl patch storageclass local-path -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'
```

Now, set the nfs-client-provisioner as the default class.

```
kubectl patch storageclass nfs-client -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

Verify that the changes are applied by running:

```
kubectl get storageclass
```

You should now see `(default)` after the nfs-client storage class.


## If NFS Storage mount is disconnected
```
esxcli storage nfs list
Volume Name  Host       Share                Vmknic  Accessible  Mounted  Connections  Read-Only   isPE  Hardware Acceleration
-----------  ---------  -------------------  ------  ----------  -------  -----------  ---------  -----  ---------------------
Synology     x.x.x.x    /volume1/folder       None         false    false            1      false  false  Unknown

---
```
Use these two Commands 
```
esxcli storage nfs remove -v Synology
esxcli storage nfs add -H x.x.x.x -s /volume1/folder -v Synology
```



Feel free to contribute to this guide by submitting a pull request.
