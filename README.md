# FileStash

[FileStash](https://www.filestash.app/) is a front-end for multiple file storage
locations which simplifies access to data. Supported backends include S3,
(S)FTP, MinIO, Git, MySQL, Dropbox, and Google Drive.

## Prerequisites

- This chart has only been tested on Kubernetes 1.18+, but should work on 1.14+
- Recent versions of Helm 3 are supported

## Installing the Chart

Currently, this chart does not have a corresponding repository, and is instead
consumed by [Flux](https://fluxcd.io)
[`GitRepository`](https://fluxcd.io/docs/guides/helmreleases/#git-repository)
object. The `GitRepository` object is then referenced as a source for a
[`HelmRelease`](https://fluxcd.io/docs/guides/helmreleases/#define-a-helm-release).

An example `GitRepository` looks like the following:
```yaml
---
apiVersion: source.toolkit.fluxcd.io/v1beta1
kind: GitRepository
metadata:
  name: filestash-helm
  namespace: flux-system
spec:
  interval: 10m0s
  ref:
    branch: main
  url: https://gitlab.rickhenry.uk/rjh/filestash-helm
```

and an example `HelmRelease` that utilises it looks like this:
```yaml
---
apiVersion: helm.toolkit.fluxcd.io/v2beta1
kind: HelmRelease
metadata:
  name: filestash
  namespace: filestash
spec:
  releaseName: filestash
  chart:
    spec:
      sourceRef:
        kind: GitRepository
        namespace: flux-system
        name: filestash-helm
      chart: .

  interval: 15m0s

  values: {}

# vim: ts=2 sw=2 et sts=2 ft=yaml
```

The default configuration will deploy nothing but a deployment and it's
corresponding service. No ingress - or any other configuration - is deployed.

## Uninstalling the Chart

Simply remove the `HelmRelease` and `GitRepository` objects from your Flux
control repository.
## Upgrading

### 0.1.x
All releases under 0.1.x are liable to change with little notice, depending on
functionality changes required by the deployment used by the developer(s).

## Configuration

The following table lists the configurable parameters for this chart and their default values.

| Parameter                                       | Description                                                           | Default                                     |
| ------------------------------------------------|-----------------------------------------------------------------------|---------------------------------------------|
| `affinity`                                      | Affinity settings for pod assignment                                  | `{}`                                        |
| `autoscaling.enabled`                           | Whether to enable the HorizontalPodAutoscaler                         | `false`                                     |
| `autoscaling.maxReplicas`                       | Maximum number of replicas when autoscaling is enabled                | `100`                                       |
| `autoscaling.minReplicas`                       | Minimum number of replicas when autoscaling is enabled                | `1`                                         |
| `autoscaling.targetCPUUtilizationPercentage`    | Target CPU utilisation percentage for autoscaling                     | `80`                                        |
| `autoscaling.targetMemoryUtilizationPercentage` | Target memory utilisation percentage for autoscaling                  | `null`                                      |
| `fullnameOverride`                              | An override for the full release name                                 | `""`                                        |
| `image.pullPolicy`                              | FileStash container image pull policy                                 | `Always`                                    |
| `image.repository`                              | FileStash container image repository                                  | `machines/filestash`                        |
| `image.tag`                                     | FileStash container image tag                                         | `""`                                        |
| `imagePullSecrets`                              | Array of maps for [Image Pull Secrets]                                | `[]`                                        |
| `ingress.annotations`                           | Any annotations that should be applied to the `Ingress` resource      | `{}`                                        |
| `ingress.className`                             | IngressClass name for the created `Ingress` resource                  | `""`                                        |
| `ingress.enabled`                               | Whether an `Ingress` resource should be automatically created         | `false`                                     |
| `ingress.hosts`                                 | Hosts and paths for the `Ingress` resource                            | `[{host:"chart-example.local",paths[{path:"/",pathType"ImplementationSpecific}]}]` |
| `ingress.tls`                                   | TLS settings for the `Ingress` resource                               | `[]`                                        |
| `nameOverride`                                  | Override the application name (`filestash`) used throughout the chart | `""`                                        |
| `nodeSelector`                                  | Node labels for pod assignment                                        | `{}`                                        |
| `persistence.accessMode`                        | Access mode for the volume                                            | `ReadWriteOnce`                             |
| `persistence.enabled`                           | Enable storage persistence for configuration                          | `false`                                     |
| `persistence.existingClaim`                     | Use an existing `PersistentVolumeClaim` instead of creating one       | `""`                                        |
| `persistence.selector`                          | Set the selector for PVs, if desired                                  | `{}`                                        |
| `persistence.size`                              | Size of persistent volume to request                                  | `32Mi`                                      |
| `persistence.storageClass`                      | Set the storage class of the PVC (use `-` to disable provisioning)    | `""`                                        |
| `persistence.subPath`                           | Mount a sub-path of the volume into the container, not the root       | `""`                                        |
| `podAnnotations`                                | Map of any annotations that should be added to the pod(s)             | `{}`                                        |
| `podSecurityContext`                            | [SecurityContext] for the FileStash pod(s)                            | `{}` (see [`values.yaml`])                  |
| `replicaCount`                                  | The desired number of FileStash pods                                  | `1`                                         |
| `resources`                                     | Configure resource requests or limits for FileStash                   | `{}`                                        |
| `securityContext`                               | [SecurityContext] for the FileStash container(s)                      | `{}` (see [`values.yaml`])                  |
| `service.port`                                  | [Service] port for the FileStash service                              | `80`                                        |
| `service.type`                                  | [Service] type for the FileStash service                              | `ClusterIP`                                 |
| `tolerations`                                   | Toleration labels for pod assignment                                  | `[]`                                        |

[Image Pull Secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/
[SecurityContext]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[Service]: https://kubernetes.io/docs/concepts/services-networking/service/
[`values.yaml`]: ./values.yaml

Specify each parameter using the `--set key=value[,key=value]` argument to `helm
install` or provide a YAML file containing the values for the above parameters.
See [`values.yaml`](./values.yaml) for an authoritative reference with
documentation.

## License

> The following notice applies to all files contained within this Helm Chart and
> the Git repository which contains it:
>
> Copyright 2021 Rick Henry
>
> Licensed under the Apache License, Version 2.0 (the "License");
> you may not use this file except in compliance with the License.
> You may obtain a copy of the License at
>
>     http://www.apache.org/licenses/LICENSE-2.0
>
> Unless required by applicable law or agreed to in writing, software
> distributed under the License is distributed on an "AS IS" BASIS,
> WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
> See the License for the specific language governing permissions and
> limitations under the License.

