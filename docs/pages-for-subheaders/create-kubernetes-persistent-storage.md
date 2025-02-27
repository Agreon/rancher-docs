---
title: "Kubernetes Persistent Storage: Volumes and Storage Classes"
description: "Learn about the two ways with which you can create persistent storage in Kubernetes: persistent volumes and storage classes"
---

<head>
  <link rel="canonical" href="https://ranchermanager.docs.rancher.com/pages-for-subheaders/create-kubernetes-persistent-storage"/>
</head>

When deploying an application that needs to retain data, you'll need to create persistent storage. Persistent storage allows you to store application data external from the pod running your application. This storage practice allows you to maintain application data, even if the application's pod fails.

The documents in this section assume that you understand the Kubernetes concepts of persistent volumes, persistent volume claims, and storage classes. For more information, refer to the section on [how storage works.](../how-to-guides/new-user-guides/manage-clusters/create-kubernetes-persistent-storage/manage-persistent-storage/about-persistent-storage.md)

### Prerequisites

To set up persistent storage, the `Manage Volumes` [role](../how-to-guides/new-user-guides/authentication-permissions-and-global-configuration/manage-role-based-access-control-rbac/cluster-and-project-roles.md#project-role-reference) is required.

If you are provisioning storage for a cluster hosted in the cloud, the storage and cluster hosts must have the same cloud provider.

For provisioning new storage with Rancher, the cloud provider must be enabled. For details on enabling cloud providers, refer to [this page.](../pages-for-subheaders/set-up-cloud-providers.md)

For attaching existing persistent storage to a cluster, the cloud provider does not need to be enabled.

### Setting up Existing Storage

The overall workflow for setting up existing storage is as follows:

1. Set up your persistent storage. This may be storage in an infrastructure provider, or it could be your own storage.
2. Add a persistent volume (PV) that refers to the persistent storage.
3. Add a persistent volume claim (PVC) that refers to the PV.
4. Mount the PVC as a volume in your workload.

For details and prerequisites, refer to [this page.](../how-to-guides/new-user-guides/manage-clusters/create-kubernetes-persistent-storage/manage-persistent-storage/set-up-existing-storage.md)

### Dynamically Provisioning New Storage in Rancher

The overall workflow for provisioning new storage is as follows:

1. Add a StorageClass and configure it to use your storage provider. The StorageClass could refer to storage in an infrastructure provider, or it could refer to your own storage.
2. Add a persistent volume claim (PVC) that refers to the storage class.
3. Mount the PVC as a volume for your workload.

For details and prerequisites, refer to [this page.](../how-to-guides/new-user-guides/manage-clusters/create-kubernetes-persistent-storage/manage-persistent-storage/dynamically-provision-new-storage.md)

### Longhorn Storage

[Longhorn](https://longhorn.io/) is a lightweight, reliable and easy-to-use distributed block storage system for Kubernetes.

Longhorn is free, open source software. Originally developed by Rancher Labs, it is now being developed as a sandbox project of the Cloud Native Computing Foundation. It can be installed on any Kubernetes cluster with Helm, with kubectl, or with the Rancher UI.

If you have a pool of block storage, Longhorn can help you provide persistent storage to your Kubernetes cluster without relying on cloud providers. For more information about Longhorn features, refer to the [documentation.](https://longhorn.io/docs/latest/what-is-longhorn/)

Rancher v2.5 simplified the process of installing Longhorn on a Rancher-managed cluster. For more information, see [Cloud Native Storage with Longhorn](../integrations-in-rancher/longhorn/longhorn.md).

### Provisioning Storage Examples

We provide examples of how to provision storage with [NFS,](../how-to-guides/new-user-guides/manage-clusters/provisioning-storage-examples/nfs-storage.md) [vSphere,](../how-to-guides/new-user-guides/manage-clusters/provisioning-storage-examples/vsphere-storage.md) and [Amazon's EBS.](../how-to-guides/new-user-guides/manage-clusters/provisioning-storage-examples/persistent-storage-in-amazon-ebs.md)

### GlusterFS Volumes

In clusters that store data on GlusterFS volumes, you may experience an issue where pods fail to mount volumes after restarting the `kubelet`. For details on preventing this from happening, refer to [this page.](../how-to-guides/new-user-guides/manage-clusters/create-kubernetes-persistent-storage/manage-persistent-storage/about-glusterfs-volumes.md)

### iSCSI Volumes

In [Rancher Launched Kubernetes clusters](../pages-for-subheaders/launch-kubernetes-with-rancher.md) that store data on iSCSI volumes, you may experience an issue where kubelets fail to automatically connect with iSCSI volumes. For details on resolving this issue, refer to [this page.](../how-to-guides/new-user-guides/manage-clusters/create-kubernetes-persistent-storage/manage-persistent-storage/install-iscsi-volumes.md)

### hostPath Volumes
Before you create a hostPath volume, you need to set up an [extra_bind](https://rancher.com/docs/rke/latest/en/config-options/services/services-extras/#extra-binds/) in your cluster configuration. This will mount the path as a volume in your kubelets, which can then be used for hostPath volumes in your workloads.

### Migrating vSphere Cloud Provider from In-tree to Out-of-tree

Kubernetes is moving away from maintaining cloud providers in-tree. vSphere has an out-of-tree cloud provider that can be used by installing the vSphere cloud provider and cloud storage plugins.

For instructions on how to migrate from the in-tree vSphere cloud provider to out-of-tree, and manage the existing VMs post migration, refer to [this page.](../how-to-guides/new-user-guides/kubernetes-clusters-in-rancher-setup/set-up-cloud-providers/configure-out-of-tree-vsphere.md)

### Related Links

- [Kubernetes Documentation: Storage](https://kubernetes.io/docs/concepts/storage/)
