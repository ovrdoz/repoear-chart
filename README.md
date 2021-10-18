# Repoear Helm Chart

This Helm chart is a lightweight way to configure and run our [Repoear Docker image][] the simple way to make gitops.

- [Requirements](#requirements)
- [Installing](#installing)
<!--   - [Install released version using Helm repository](#install-released-version-using-helm-repository) -->
  - [Install development version using master branch](#install-development-version-using-master-branch)
- [Usage notes](#usage-notes)
- [Configuration](#configuration)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->
<!-- Use this to update TOC: -->
<!-- docker run --rm -it -v $(pwd):/usr/src jorgeandrada/doctoc --github -->

## Requirements

* Kubernetes >= 1.14
* [Helm][] >= 2.17.0

## Installing

<!--### Install released version using Helm repository

* Add the Repoear Helm charts repo:
`helm repo add Repoear https://ovrdoz.github.com/repoear`

* Install it:
  - with Helm 3: `helm install repoear ovrdoz/repoear`
  - with Helm 2 (deprecated): `helm install --name repoear ovrdoz/repoear` -->

### Install development version using master branch

* Clone the git repo: `git clone git@github.com:ovrdoz/repoear-chart.git`

* Install it:
  - with Helm 3: `helm install repoear ./repoear-chart  
  - with Helm 2 (deprecated): `helm install --name repoear ./repoear-chart  

## Usage notes

* The chart deploys a StatefulSet and by default will do an automated rolling
update of your cluster. It does this by waiting for the cluster health to become
green after each instance is updated. If you prefer to update manually you can
set `OnDelete` [updateStrategy][].
* We have designed this chart to be very un-opinionated about how to configure
Repoear. It exposes ways to set environment variables and mount secrets inside
of the container. Doing this makes it much easier for this chart to support
multiple versions with minimal changes.
* `config.yml` configuration files can be set either by a ConfigMap using
`repoearConfig` in `values.yml`, however Repoear Docker image can't mix both methods as
defining settings with environment variables causes `config.yml` to be
modified in place while using ConfigMap bind-mount the same file (more details
in this [note][]).
* An ingress is provided that can be used to expose the HTTP port. This can be
useful for the [http input plugin][], for instance.


## Configuration

| Parameter                 | Description                                                                                                                                                                                                                          | Default                               |
|---------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------|
| `antiAffinityTopologyKey` | The [anti-affinity][] topology key]. By default this will prevent multiple Repoear nodes from running on the same Kubernetes node                                                                                                   | `kubernetes.io/hostname`              |
| `antiAffinity`            | Setting this to hard enforces the [anti-affinity][] rules. If it is set to soft it will be done "best effort". Other values will be ignored                                                                                          | `hard`                                |
| `fullnameOverride`        | Overrides the full name of the resources. If not set the name will default to " `.Release.Name` - `.Values.nameOverride or .Chart.Name` "                                                                                            | `""`                                  |
| `hostAliases`             | Configurable [hostAliases][]                                                                                                                                                                                                         | `[]`                                  |
| `httpPort`                | The http port that Kubernetes will use for the healthchecks and the service                                                                                                                                                          | `9600`                                |
| `imagePullPolicy`         | The Kubernetes [imagePullPolicy][] value                                                                                                                                                                                             | `IfNotPresent`                        |
| `imagePullSecrets`        | Configuration for [imagePullSecrets][] so that you can use a private registry for your image                                                                                                                                         | `[]`                                  |
| `imageTag`                | The Repoear Docker image tag                                                                                                                                                                                                        | `1.0.0`                      |
| `image`                   | The Repoear Docker image                                                                                                                                                                                                            | `docker.io/ovrdoz/repoear` |
| `ingress`                 | Configurable [ingress][] for external access to Repoear HTTP port.                                                                                                                                                                  | see [values.yaml][]                   |
| `lifecycle`               | Allows you to add lifecycle configuration. See [values.yaml][] for an example of the formatting                                                                                                                                      | `{}`                                  |
| `livenessProbe`           | Configuration fields for the liveness [probe][]                                                                                                                                                                                      | see [values.yaml][]                   |
| `repoearConfig`          | Allows you to add any config files in `/usr/share/repoear/config/` such as `repoear.yml` and `log4j2.properties` See [values.yaml][] for an example of the formatting                                                              | `{}`                                  |
| `nameOverride`            | Overrides the chart name for resources. If not set the name will default to `.Chart.Name`                                                                                                                                            | `""`                                  |
| `nodeAffinity`            | Value for the [node affinity settings][]                                                                                                                                                                                             | `{}`                                  |
| `podAffinity`             | Value for the [pod affinity settings][]                                                                                                                                                                                             | `{}`                                  |
| `nodeSelector`            | Configurable [nodeSelector][] so that you can target specific nodes for your Repoear cluster                                                                                                                                        | `{}`                                  |
| `persistence`             | Enables a persistent volume for Repoear data                                                                                                                                                                                        | see [values.yaml][]                   |
| `readinessProbe`          | Configuration fields for the readiness [probe][]                                                                                                                                                                                     | see [values.yaml][]                   |
| `replicas`                | Kubernetes replica count for the StatefulSet (i.e. how many pods)                                                                                                                                                                    | `1`                                   |
| `resources`               | Allows you to set the [resources][] for the StatefulSet                                                                                                                                                                              | see [values.yaml][]                   |
| `schedulerName`           | Name of the [alternate scheduler][]                                                                                                                                                                                                  | `""`                                  |
| `secrets`                 | Allows you easily create a secret from as variables or file. For add secrets from file, add suffix `.filepath` to the key of secret key. The value will be encoded to base64. Useful for store certificates and other secrets.       | See [values.yaml][]                   |
| `securityContext`         | Allows you to set the [securityContext][] for the container                                                                                                                                                                          | see [values.yaml][]                   |
| `service`                 | Configurable [service][] to expose the Repoear service.                                                                                                                                                                             | see [values.yaml][]                   |
| `terminationGracePeriod`  | The [terminationGracePeriod][] in seconds used when trying to stop the pod                                                                                                                                                           | `120`                                 |
| `updateStrategy`          | The [updateStrategy][] for the StatefulSet. By default Kubernetes will wait for the cluster to be green after upgrading each pod. Setting this to `OnDelete` will allow you to manually delete each pod during upgrades              | `RollingUpdate`                       |
| `volumeClaimTemplate`     | Configuration for the [volumeClaimTemplate for StatefulSets][]. You will want to adjust the storage (default `1Gi` ) and the `storageClassName` if you are using a different storage class                                          | see [values.yaml][]                   |


[Repoear Docker image]: https://hub.docker.com/repository/docker/ovrdoz/repoear
[alternate scheduler]: https://kubernetes.io/docs/tasks/administer-cluster/configure-multiple-schedulers/#specify-schedulers-for-pods
[annotations]: https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/
[anti-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[deploys statefulsets serially]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/#pod-management-policies
[environment variables]: https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/#using-environment-variables-inside-of-your-config
[environment from variables]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/#configure-all-key-value-pairs-in-a-configmap-as-container-environment-variables
[helm]: https://helm.sh
[hostAliases]: https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/
[imagePullPolicy]: https://kubernetes.io/docs/concepts/containers/images/#updating-images
[imagePullSecrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-pod-that-uses-your-secret
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[kubernetes secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[maxUnavailable]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/#specifying-a-poddisruptionbudget
[node affinity settings]: https://kubernetes.io/docs/tasks/configure-pod-container/assign-pods-nodes-using-node-affinity/
[pod affinity settings]: https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity
[nodeSelector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#nodeselector
[priorityClass]: https://kubernetes.io/docs/concepts/configuration/pod-priority-preemption/#priorityclass
[probe]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/
[resources]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[updateStrategy]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[securityContext]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/#set-the-security-context-for-a-pod
[service]: https://kubernetes.io/docs/concepts/services-networking/service/
[terminationGracePeriod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/#termination-of-pods
[tolerations]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[volumeClaimTemplate for statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/#stable-storage