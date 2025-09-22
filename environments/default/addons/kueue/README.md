# Kueue GPU Node Pool Resources

This directory contains a Helm chart that dynamically generates Kueue resources for GPU node pools based on cluster metadata.

## How it works

1. **Cluster Metadata**: The ApplicationSet reads the `gpu_node_pools` annotation from cluster metadata, which contains a comma-separated list of GPU node pool names.

2. **Helm Templating**: The Helm templates in the `templates/` directory generate the following resources for each GPU node pool:
   - `ResourceFlavor` - Defines the node pool characteristics
   - `AdmissionCheck` - Controls admission to the queue
   - `ProvisioningRequestConfig` - Configures dynamic provisioning
   - `ClusterQueue` - Defines the cluster-wide queue
   - `LocalQueue` - Provides namespace-scoped access to the cluster queue

3. **Resource Naming**: Each resource is suffixed with the node pool name to ensure uniqueness.

## Configuration

### Cluster Metadata
Add the following annotation to your cluster configuration:
```yaml
annotations:
  gpu_node_pools: "gpu-pool-1,gpu-pool-2,ml-benchmark-gpu"
```

### Values Configuration
The `values.yaml` file allows you to configure:
- Enable/disable GPU node pool resource generation
- Default resource quotas for GPU pools
- Additional Kueue resources

## Generated Resources

For a GPU node pool named `gpu-pool-1`, the following resources will be created:
- `ResourceFlavor/gpu-pool-gpu-pool-1`
- `AdmissionCheck/dws-prov-gpu-pool-1`
- `ProvisioningRequestConfig/dws-config-gpu-pool-1`
- `ClusterQueue/dws-cluster-queue-gpu-pool-1`
- `LocalQueue/gpu-queue-gpu-pool-1`

## ApplicationSet Integration

The ApplicationSet (`addons-kueue-appset.yaml`) has been configured with dual sources:
1. The main Kueue Helm chart from the registry
2. This custom Helm chart that generates GPU node pool resources

The `gpu_node_pools` cluster annotation is automatically injected into the Helm values as `gpuNodePools.pools`.

## Files

- `Chart.yaml` - Helm chart metadata
- `values.yaml` - Chart configuration values with GPU node pool settings
- `templates/` - Helm templates for dynamic resource generation
  - `_helpers.tpl` - Template helper functions
  - `gpu-nodepool-resources.yaml` - Main template that generates all Kueue resources
