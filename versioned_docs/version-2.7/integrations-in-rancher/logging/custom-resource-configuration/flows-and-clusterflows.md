---
title: Flows and ClusterFlows
---

<head>
  <link rel="canonical" href="https://ranchermanager.docs.rancher.com/integrations-in-rancher/logging/custom-resource-configuration/flows-and-clusterflows"/>
</head>

See the [Logging operator documentation](https://kube-logging.github.io/docs/configuration/flow/) for the full details on how to configure  `Flows` and `ClusterFlows`.

See [Rancher Integration with Logging Services: Troubleshooting](../../../pages-for-subheaders/logging.md#The-Logging-Buffer-Overloads-Pods) for how to resolve memory problems with the logging buffer.

## Flows

A `Flow` defines which logs to collect and filter and which output to send the logs to.

The `Flow` is a namespaced resource, which means logs will only be collected from the namespace that the `Flow` is deployed in.

`Flows` can be configured by filling out forms in the Rancher UI.

For more details about the `Flow` custom resource, see [FlowSpec.](https://kube-logging.github.io/docs/configuration/crds/v1beta1/flow_types/)

### Matches

Match statements are used to select which containers to pull logs from.

You can specify match statements to select or exclude logs according to Kubernetes labels, container and host names. Match statements are evaluated in the order they are defined and processed only until the first matching select or exclude rule applies.

Matches can be configured by filling out the `Flow` or `ClusterFlow` forms in the Rancher UI.

For detailed examples on using the match statement, see the [official documentation on log routing.](https://kube-logging.github.io/docs/configuration/log-routing/)

### Filters

You can define one or more filters within a `Flow`. Filters can perform various actions on the logs, such as adding data, transforming the logs, or parsing values from the records. The filters in the `Flow` are applied in the same order they appear in the definition.

For a list of filters supported by the Logging operator, see [the official documentation on Fluentd filters](https://kube-logging.github.io/docs/configuration/plugins/filters/).

Filters need to be configured in YAML.

### Outputs

This `Output` will receive logs from the `Flow`. Because the `Flow` is a namespaced resource, the `Output` must reside in same namespace as the `Flow`.

`Outputs` can be referenced when filling out the `Flow` or `ClusterFlow` forms in the Rancher UI.

## ClusterFlows

Matches, filters and `Outputs` are configured for `ClusterFlows` in the same way that they are configured for `Flows`. The key difference is that the `ClusterFlow` is scoped at the cluster level and can configure log collection across all namespaces.

`ClusterFlows` can be configured by filling out forms in the Rancher UI.

After `ClusterFlow` selects logs from all namespaces in the cluster, logs from the cluster will be collected and logged to the selected `ClusterOutput`.

## YAML Example

The following example `Flow` transforms the log messages from the default namespace and sends them to an S3 `Output`:

```yaml
apiVersion: logging.banzaicloud.io/v1beta1
kind: Flow
metadata:
  name: flow-sample
  namespace: default
spec:
  filters:
    - parser:
        remove_key_name_field: true
        parse:
          type: nginx
    - tag_normaliser:
        format: ${namespace_name}.${pod_name}.${container_name}
  localOutputRefs:
    - s3-output
  match:
    - select:
        labels:
          app: nginx
```
