---
title: NeuVector Integration
---

<head>
  <link rel="canonical" href="https://ranchermanager.docs.rancher.com/integrations-in-rancher/neuvector"/>
</head>

### NeuVector Integration in Rancher

[NeuVector 5.x](https://open-docs.neuvector.com/) is an open-source container-centric security platform that is integrated with Rancher. NeuVector offers real-time compliance, visibility, and protection for critical applications and data during runtime. NeuVector provides a firewall, container process/file system monitoring, security auditing with CIS benchmarks, and vulnerability scanning. For more information on Rancher security, please see the [security documentation](../pages-for-subheaders/rancher-security.md).

NeuVector can be enabled through a Helm chart that may be installed either through **Apps** or through the **Cluster Tools** button in the Rancher UI. Once the Helm chart is installed, users can easily [deploy and manage NeuVector clusters within Rancher](https://open-docs.neuvector.com/deploying/rancher#deploy-and-manage-neuvector-through-rancher-apps-marketplace).

### Installing NeuVector with Rancher

The Harvester Helm Chart is used to manage access to the NeuVector UI in Rancher where users can navigate directly to deploy and manage their NeuVector clusters.

**To navigate to and install the NeuVector chart through Apps:**

1. Click **☰ > Cluster Management**.
1. On the Clusters page, go to the cluster where you want to deploy NeuVector, and click **Explore**.
1. Go to **Apps > Charts**, and install **NeuVector** from the chart repo.
1. Different cluster types require different container runtimes. When configuring Helm chart values, go to the **Container Runtime** section, and select your runtime in accordance with the cluster type. Finally, click **Install** again.

Some examples are as follows:

   - RKE1: `docker`
   - K3s and RKE2: `k3scontainerd`
   - AKS: `containerd` for v1.19 and up
   - EKS: `docker` for v1.22 and below; `containerd` for v1.23 and up
   - GKE: `containerd` (see the [Google docs](https://cloud.google.com/kubernetes-engine/docs/concepts/using-containerd) for more)

    :::note

    Only one container runtime engine may be selected at a time during installation.

    :::

**To navigate to and install the NeuVector chart through Cluster Tools:**

1. Click **☰ > Cluster Management**.
1. On the Clusters page, go to the cluster where you want to deploy NeuVector, and click **Explore**.
1. Click on **Cluster Tools** at the bottom of the left navigation bar.
1. Repeat step 4 above to select your container runtime accordingly, then click **Install** again.

### Accessing NeuVector from the Rancher UI

1. Navigate to the cluster explorer of the cluster where NeuVector is installed. In the left navigation bar, click **NeuVector**.
1. Click the external link to go to the NeuVector UI. Once the link is selected, users must accept the `END USER LICENSE AGREEMENT` to access the NeuVector UI.

### Uninstalling NeuVector from the Rancher UI

**To uninstall from Apps:**

1. Click **☰ > Cluster Management**.
1. Under **Apps**, click **Installed Apps**.
1. Under `cattle-neuvector-system`, select both the NeuVector app (and the associated CRD if desired), then click **Delete**.

**To uninstall from Cluster Tools:**

1. Click **☰ > Cluster Management**.
1. Click on **Cluster Tools** at the bottom-left of the screen, then click on the trash can icon under the NeuVector chart. Select `Delete the CRD associated with this app` if desired, then click **Delete**.

### GitHub Repository

The NeuVector project is available [here](https://github.com/neuvector/neuvector).

### Documentation

The NeuVector documentation is [here](https://open-docs.neuvector.com/).

### Architecture

The NeuVector security solution contains four types of security containers: Controllers, Enforcers, Managers, and Scanners. A special container called an All-in-One is also provided to combine the Controller, Enforcer, and Manager functions all in one container, primarily for Docker-native deployments. There is also an Updater which, when run, will update the CVE database.

- **Controller:** Manages the NeuVector Enforcer container; provides REST APIs for the management console.
- **Enforcer:** Enforces security policies.
- **Manager:** Provides a web-UI and CLI console to manage the NeuVector platform.
- **All-in-One:** Includes the Controller, Enforcer, and Manager.
- **Scanner:** Performs the vulnerability and compliance scanning for images, containers, and nodes.
- **Updater:** Updates the CVE database for Neuvector (when run); redeploys scanner pods.

<figcaption>**NeuVector Security Containers:**</figcaption>

![NeuVector Security Containers](/img/neuvector-security-containers.png)

<figcaption>**NeuVector Architecture:**</figcaption>

![NeuVector Architecture](/img/neuvector-architecture.png)

To learn more about NeuVector's architecture, please refer [here](https://open-docs.neuvector.com/basics/overview#architecture).

### CPU and Memory Allocations

Below are the minimum recommended computing resources for the NeuVector chart installation in a default deployment. Note that the resource limit is not set.

| Container   |   CPU - Request  |   Memory - Request  |
|------------|--------|---------|
| Controller   |  3 (1GB 1vCPU needed per controller) | *
| Enforcer     |  On all nodes (500MB .5vCPU) | 1GB
| Manager      |  1 (500MB .5vCPU) | *
| Scanner      |  3 (100MB .5vCPU) | *

\* Minimum 1GB of memory total required for Controller, Manager, and Scanner containers combined.


### Hardened Cluster Support - Calico and Canal

<Tabs>
<TabItem value="RKE1">

  - All NeuVector components are deployable if PSP is set to true.

  You will need to set additional configuration for your hardened cluster environment as follows:

  1. Click **☰ > Cluster Management**.
  1. Go to the cluster that you created and click **Explore**.
  1. In the left navigation bar, click **Apps**.
  1. Install (or upgrade to) NeuVector version `100.0.1+up2.2.2`.

  - Under **Edit Options** > **Other Configuration**, enable **Pod Security Policy** by checking the box (no other config needed):

      ![Enable PSP for RKE1 Hardened Cluster](/img/psp-nv-rke.png)

  1. Click **Install** at the bottom-right to complete.


</TabItem>
<TabItem value="RKE2">

  - NeuVector components Controller and Enforcer are deployable if PSP is set to true.


  **Applicable to NeuVector chart version 100.0.0+up2.2.0 only:**

  - For Manager, Scanner, and Updater components, additional configuration is required as shown below:

  ```
  kubectl patch deploy neuvector-manager-pod -n cattle-neuvector-system --patch '{"spec":{"template":{"spec":{"securityContext":{"runAsUser": 5400}}}}}'
  kubectl patch deploy neuvector-scanner-pod -n cattle-neuvector-system --patch '{"spec":{"template":{"spec":{"securityContext":{"runAsUser": 5400}}}}}'
  kubectl patch cronjob neuvector-updater-pod -n cattle-neuvector-system --patch '{"spec":{"jobTemplate":{"spec":{"template":{"spec":{"securityContext":{"runAsUser": 5400}}}}}}}'
  ```
  <br/>

  You will need to set additional configuration for your hardened cluster environment.

  >**Note:** You must update your config in both RKE2 and K3s hardened clusters as shown below.

  1. Click **☰ > Cluster Management**.
  1. Go to the cluster that you created and click **Explore**.
  1. In the left navigation bar, click **Apps**.
  1. Install (or upgrade to) NeuVector version `100.0.1+up2.2.2`.

  - Under **Edit Options** > **Other Configuration**, enable **Pod Security Policy** by checking the box. Note that you must also enter a value greater than `zero` for `Manager runAsUser ID`, `Scanner runAsUser ID`, and `Updater runAsUser ID`:

      ![Enable PSP for RKE2 and K3s Hardened Clusters](/img/psp-nv-rke2.png)

  1. Click **Install** at the bottom-right to complete.

</TabItem>
</Tabs>


### SELinux-enabled Cluster Support - Calico and Canal

To enable SELinux on RKE2 clusters, follow the steps below:

- NeuVector components Controller and Enforcer are deployable if PSP is set to true.


**Applicable to NeuVector chart version 100.0.0+up2.2.0 only:**

- For Manager, Scanner, and Updater components, additional configuration is required as shown below:

```
kubectl patch deploy neuvector-manager-pod -n cattle-neuvector-system --patch '{"spec":{"template":{"spec":{"securityContext":{"runAsUser": 5400}}}}}'
kubectl patch deploy neuvector-scanner-pod -n cattle-neuvector-system --patch '{"spec":{"template":{"spec":{"securityContext":{"runAsUser": 5400}}}}}'
kubectl patch cronjob neuvector-updater-pod -n cattle-neuvector-system --patch '{"spec":{"jobTemplate":{"spec":{"template":{"spec":{"securityContext":{"runAsUser": 5400}}}}}}}'
```

### Cluster Support in an Air-Gapped Environment

- All NeuVector components are deployable on a cluster in an air-gapped environment without any additional configuration needed.


### Support Limitations

* Only admins and cluster owners are currently supported.

* Fleet multi-cluster deployment is not supported.

* NeuVector is not supported on a Windows cluster.


### Other Limitations

* Currently, NeuVector feature chart installation fails when a NeuVector partner chart already exists. To work around this issue, uninstall the NeuVector partner chart and reinstall the NeuVector feature chart.

* Sometimes when the controllers are not ready, the NeuVector UI is not accessible from the Rancher UI. During this time, controllers will try to restart, and it takes a few minutes for the controllers to be active.

* Container runtime is not auto-detected for different cluster types when installing the NeuVector chart. To work around this, you can specify the runtime manually.

