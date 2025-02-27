---
title: Applying Pod Security Policies to Projects
---

<head>
  <link rel="canonical" href="https://ranchermanager.docs.rancher.com/how-to-guides/advanced-user-guides/manage-projects/manage-pod-security-policies"/>
</head>

:::note

These cluster options are only available for [clusters in which Rancher has launched Kubernetes](../../../pages-for-subheaders/launch-kubernetes-with-rancher.md).

:::

You can always assign a pod security policy (PSP) to an existing project if you didn't assign one during creation.

### Prerequisites

- Create a Pod Security Policy within Rancher. Before you can assign a default PSP to an existing project, you must have a PSP available for assignment. For instruction, see [Creating Pod Security Policies](../../new-user-guides/authentication-permissions-and-global-configuration/create-pod-security-policies.md).
- Assign a default Pod Security Policy to the project's cluster. You can't assign a PSP to a project until one is already applied to the cluster. For more information, see [the documentation about adding a pod security policy to a cluster](../../new-user-guides/manage-clusters/add-a-pod-security-policy.md). 

### Applying a Pod Security Policy

1. In the upper left corner, click **☰ > Cluster Management**.
1. On the **Clusters** page, go to the cluster where you want to move a namespace and click **Explore**.
1. Click **Cluster > Projects/Namespaces**.
1. Find the project that you want to add a PSP to. From that project, select **⋮ > Edit Config**.
1. From the **Pod Security Policy** drop-down, select the PSP you want to apply to the project.
  Assigning a PSP to a project will:

  - Override the cluster's default PSP.
  - Apply the PSP to the project.
  - Apply the PSP to any namespaces you add to the project later.

1. Click **Save**.

**Result:** The PSP is applied to the project and any namespaces added to the project.

:::note

Any workloads that are already running in a cluster or project before a PSP is assigned will not be checked to determine if they comply with the PSP. Workloads would need to be cloned or upgraded to see if they pass the PSP.

:::