---
title: Managing Node Templates
---

<head>
  <link rel="canonical" href="https://ranchermanager.docs.rancher.com/reference-guides/user-settings/manage-node-templates"/>
</head>

When you provision a cluster [hosted by an infrastructure provider](../../pages-for-subheaders/use-new-nodes-in-an-infra-provider.md), [node templates](../../pages-for-subheaders/use-new-nodes-in-an-infra-provider.md#node-templates) are used to provision the cluster nodes. These templates use Docker Machine configuration options to define an operating system image and settings/parameters for the node. You can create node templates in two contexts:

- While [provisioning a node pool cluster](../../pages-for-subheaders/use-new-nodes-in-an-infra-provider.md).
- At any time, from your [user settings](../../pages-for-subheaders/user-settings.md).

When you create a node template, it is bound to your user profile. Node templates cannot be shared among users. You can delete stale node templates that you no longer user from your user settings.

## Creating a Node Template

1. Click **☰ > Cluster Management**.
1. Click **RKE1 Configuration > Node Templates**.
1. Click **Add Template**.
1. Select one of the cloud providers available. Then follow the instructions on screen to configure the template.

**Result:** The template is configured. You can use the template later when you [provision a node pool cluster](../../pages-for-subheaders/use-new-nodes-in-an-infra-provider.md).

## Updating a Node Template

1. Click **☰ > Cluster Management**.
1. Click **RKE1 Configuration > Node Templates**.
1. Choose the node template that you want to edit and click the **⋮ > Edit**.

    :::note

    The default `active` [node drivers](../../how-to-guides/new-user-guides/authentication-permissions-and-global-configuration/about-provisioning-drivers/manage-node-drivers.md) and any node driver, that has fields marked as `password`, are required to use [cloud credentials](../../pages-for-subheaders/use-new-nodes-in-an-infra-provider.md#cloud-credentials).

    :::

1. Edit the required information and click **Save**.

**Result:** The node template is updated. All node pools using this node template will automatically use the updated information when new nodes are added.

## Cloning Node Templates

When creating new node templates from your user settings, you can clone an existing template and quickly update its settings rather than creating a new one from scratch. Cloning templates saves you the hassle of re-entering access keys for the cloud provider.

1. Click **☰ > Cluster Management**.
1. Click **RKE1 Configuration > Node Templates**.
1. Find the template you want to clone. Then select **⋮ > Clone**.
1. Complete the rest of the form.

**Result:** The template is cloned and configured. You can use the template later when you [provision a node pool cluster](../../pages-for-subheaders/use-new-nodes-in-an-infra-provider.md).

## Deleting a Node Template

When you no longer use a node template, you can delete it from your user settings.

1. Click **☰ > Cluster Management**.
1. Click **RKE1 Configuration > Node Templates**.
1. Select one or more template from the list. Then click **Delete**. Confirm the delete when prompted.
