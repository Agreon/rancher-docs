---
title: Allowing Unsupported Storage Drivers
---

<head>
  <link rel="canonical" href="https://ranchermanager.docs.rancher.com/how-to-guides/advanced-user-guides/enable-experimental-features/unsupported-storage-drivers"/>
</head>

This feature allows you to use types for storage providers and provisioners that are not enabled by default.

To enable or disable this feature, refer to the instructions on [the main page about enabling experimental features.](../../../pages-for-subheaders/enable-experimental-features.md)

Environment Variable Key | Default Value | Description
---|---|---
 `unsupported-storage-drivers` | `false` | This feature enables types for storage providers and provisioners that are not enabled by default.

### Types for Persistent Volume Plugins that are Enabled by Default
Below is a list of storage types for persistent volume plugins that are enabled by default. When enabling this feature flag, any persistent volume plugins that are not on this list are considered experimental and unsupported:

Name | Plugin
--------|----------
Amazon EBS Disk |  `aws-ebs`
AzureFile | `azure-file`
AzureDisk | `azure-disk`
Google Persistent Disk | `gce-pd`
Longhorn | `flex-volume-longhorn`
VMware vSphere Volume |  `vsphere-volume`
Local | `local`
Network File System | `nfs`
hostPath | `host-path`

### Types for StorageClass that are Enabled by Default
Below is a list of storage types for a StorageClass that are enabled by default. When enabling this feature flag, any persistent volume plugins that are not on this list are considered experimental and unsupported:

Name | Plugin
--------|--------
Amazon EBS Disk |  `aws-ebs`
AzureFile | `azure-file`
AzureDisk | `azure-disk`
Google Persistent Disk | `gce-pd`
Longhorn | `flex-volume-longhorn`
VMware vSphere Volume | `vsphere-volume`
Local | `local`