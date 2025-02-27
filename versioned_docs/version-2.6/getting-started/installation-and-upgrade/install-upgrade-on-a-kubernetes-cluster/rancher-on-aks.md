---
title: Installing Rancher on Azure Kubernetes Service
---

<head>
  <link rel="canonical" href="https://ranchermanager.docs.rancher.com/getting-started/installation-and-upgrade/install-upgrade-on-a-kubernetes-cluster/rancher-on-aks"/>
</head>

This page covers how to install Rancher on Microsoft's Azure Kubernetes Service (AKS).

The guide uses command line tools to provision an AKS cluster with an ingress. If you prefer to provision your cluster using the Azure portal, refer to the [official documentation](https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough-portal).

If you already have an AKS Kubernetes cluster, skip to the step about [installing an ingress.](#5-install-an-ingress) Then install the Rancher Helm chart following the instructions on [this page.](../../../pages-for-subheaders/install-upgrade-on-a-kubernetes-cluster.md#install-the-rancher-helm-chart)

## Prerequisites

:::caution

Deploying to Microsoft Azure will incur charges.

:::

- [Microsoft Azure Account](https://azure.microsoft.com/en-us/free/): A Microsoft Azure Account is required to create resources for deploying Rancher and Kubernetes.
- [Microsoft Azure Subscription](https://docs.microsoft.com/en-us/azure/cost-management-billing/manage/create-subscription#create-a-subscription-in-the-azure-portal): Use this link to follow a tutorial to create a Microsoft Azure subscription if you don't have one yet.
- [Micsoroft Azure Tenant](https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-create-new-tenant): Use this link and follow instructions to create a Microsoft Azure tenant.
- Your subscription has sufficient quota for at least 2 vCPUs. For details on Rancher server resource requirements, refer to [this section](../../../pages-for-subheaders/installation-requirements.md)
- When installing Rancher with Helm in Azure, use the L7 load balancer to avoid networking issues. For more information, refer to the documentation on [Azure load balancer limitations](https://docs.microsoft.com/en-us/azure/load-balancer/components#limitations).

## 1. Prepare your Workstation

Install the following command line tools on your workstation:

- The Azure CLI, **az:** For help, refer to these [installation steps.](https://docs.microsoft.com/en-us/cli/azure/)
- **kubectl:** For help, refer to these [installation steps.](https://kubernetes.io/docs/tasks/tools/#kubectl)
- **helm:** For help, refer to these [installation steps.](https://helm.sh/docs/intro/install/)

## 2. Create a Resource Group

After installing the CLI, you will need to log in with your Azure account.

```
az login
```

Create a [resource group](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/manage-resource-groups-portal) to hold all relevant resources for your cluster. Use a location that applies to your use case.

```
az group create --name rancher-rg --location eastus
```

## 3. Create the AKS Cluster

To create an AKS cluster, run the following command. Use a VM size that applies to your use case. Refer to [this article](https://docs.microsoft.com/en-us/azure/virtual-machines/sizes) for available sizes and options. When choosing a Kubernetes version, be sure to first consult the [support matrix](https://rancher.com/support-matrix/) to find the highest version of Kubernetes that has been validated for your Rancher version.

:::note

If you're updating from an older version of Kubernetes, to Kubernetes v1.22 or above, you also need to [update](https://kubernetes.github.io/ingress-nginx/user-guide/k8s-122-migration/) ingress-nginx.

:::

```
az aks create \
  --resource-group rancher-rg \
  --name rancher-server \
  --kubernetes-version <VERSION> \
  --node-count 3 \
  --node-vm-size Standard_D2_v3
```

The cluster will take some time to be deployed.

## 4. Get Access Credentials

After the cluster is deployed, get the access credentials.

```
az aks get-credentials --resource-group rancher-rg --name rancher-server
```

This command merges your cluster's credentials into the existing kubeconfig and allows `kubectl` to interact with the cluster.

## 5. Install an Ingress

The cluster needs an Ingress so that Rancher can be accessed from outside the cluster. Installing an Ingress requires allocating a public IP address. Ensure you have sufficient quota, otherwise it will fail to assign the IP address. Limits for public IP addresses are applicable at a regional level per subscription.

To make sure that you choose the correct Ingress-NGINX Helm chart, first find an `Ingress-NGINX version` that's compatible with your Kubernetes version in the [Kubernetes/ingress-nginx support table](https://github.com/kubernetes/ingress-nginx#supported-versions-table). 

Then, list the Helm charts available to you by running the following command:

```
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm search repo ingress-nginx -l
```

The `helm search` command's output contains an `APP VERSION` column. The versions under this column are equivalent to the `Ingress-NGINX version` you chose earlier. Using the app version, select a chart version that bundles an app compatible with your Kubernetes install. For example, if you have Kubernetes v1.23, you can select the v4.5.2 Helm chart, since Ingress-NGINX v1.6.4 comes bundled with that chart, and v1.6.4 is compatible with Kubernetes v1.23. When in doubt, select the most recent compatible version.

Now that you know which Helm chart `version` you need, run the following command. It installs an `nginx-ingress-controller` with a Kubernetes load balancer service:

```
helm upgrade --install \
  ingress-nginx ingress-nginx/ingress-nginx \
  --namespace ingress-nginx \
  --set controller.service.type=LoadBalancer \
  --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-health-probe-request-path"=/healthz \
  --set controller.service.externalTrafficPolicy=Local \
  --version 4.5.2 \
  --create-namespace
```

## 6. Get Load Balancer IP

To get the address of the load balancer, run:

```
kubectl get service ingress-nginx-controller --namespace=ingress-nginx
```

The result should look similar to the following:

```
NAME                       TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)
 AGE
ingress-nginx-controller   LoadBalancer   10.0.116.18    40.31.180.83   80:31229/TCP,443:31050/TCP
 67s
```

Save the `EXTERNAL-IP`.

## 7. Set up DNS

External traffic to the Rancher server will need to be directed at the load balancer you created.

Set up a DNS to point at the `EXTERNAL-IP` that you saved. This DNS will be used as the Rancher server URL.

There are many valid ways to set up the DNS. For help, refer to the [Azure DNS documentation](https://docs.microsoft.com/en-us/azure/dns/)

## 8. Install the Rancher Helm Chart

Next, install the Rancher Helm chart by following the instructions on [this page.](../../../pages-for-subheaders/install-upgrade-on-a-kubernetes-cluster.md#install-the-rancher-helm-chart) The Helm instructions are the same for installing Rancher on any Kubernetes distribution.

Use that DNS name from the previous step as the Rancher server URL when you install Rancher. It can be passed in as a Helm option. For example, if the DNS name is `rancher.my.org`, you could run the Helm installation command with the option `--set hostname=rancher.my.org`.

**_New in v2.6.7_**

When installing Rancher on top of this setup, you will also need to pass the value below into the Rancher Helm install command in order to set the name of the ingress controller to be used with Rancher's ingress resource:

```
--set ingress.ingressClassName=nginx
```

Refer [here for the Helm install command](../../../pages-for-subheaders/install-upgrade-on-a-kubernetes-cluster.md#5-install-rancher-with-helm-and-your-chosen-certificate-option) for your chosen certificate option.
