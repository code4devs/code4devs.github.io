# Istio-control-plane Helm Chart

This is a consolidated chart installs all required components from Istio Control Plane required for Ambient Mode. 

This helm chart is replica of istio-ambient helm chart. 


## Setup Repo Info

```console
helm repo add code4devs https://code4devs.github.io/charts/
helm repo update
```

_See [helm repo](https://helm.sh/docs/helm/helm_repo/) for command documentation._

## Installing the Chart

This chart will use namespace name as 'istio-system' to standardize naming standards across all components. 

To install the chart with the release name `istio-control-panel`:

```console
kubectl create namespace istio-system
helm install istio-control-plane code4devs/istio-control-plane --namespace istio-system
```
OR 
```
helm install istio-control-plane code4devs/istio-control-plane --namespace istio-system --create-namespace
```

## Uninstalling the Chart

To uninstall/delete the `istio-control-plane` deployment:

```console
helm delete istio-control-plane --namespace istio-system
kubectl get crd -oname | grep --color=never 'istio.io' | xargs kubectl delete
kubectl delete namespace istio-system
```

## Configuration

To view support configuration options and documentation, run:

```console
helm show values code4devs/istio-control-plane
```
## Dependencies

By default this chart installs all dependent charts from `istio/ambient`.
 

### Examples

#### Configuring mesh configuration settings

Any [Mesh Config](https://istio.io/latest/docs/reference/config/istio.mesh.v1alpha1/) options can be configured like below:

```yaml
meshConfig:
  accessLogFile: /dev/stdout
```

Istio ambient mode can be enabled using configuration below: 

```yaml
global:
  profile: ambient

istiod:
  profile: ambient

cni:
  enabled: true
  profile: ambient

ztunnel:
  enabled: true
  profile: ambient
```
