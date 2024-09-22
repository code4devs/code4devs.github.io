# Istio-all Helm Chart

This is a consolidated chart installs all required components from Istio, along with minimal Kiali and prometheus, in a single helm installation. 

You can customize the installation by overriding values from dependent istio, kiali and prometheus values.

## Setup Repo Info

```console
helm repo add code4devs https://code4devs.github.io/charts/
helm repo update
```

_See [helm repo](https://helm.sh/docs/helm/helm_repo/) for command documentation._

## Installing the Chart

This chart will use namespace name as 'istio-system' to standardize naming standards across all components. 

To install the chart with the release name `istio-all`:

```console
kubectl create namespace istio-system
helm install istio-all code4devs/istio-all --namespace istio-system
```
OR 
```
helm install istio-all code4devs/istio-all --namespace istio-system --create-namespace
```

## Uninstalling the Chart

To uninstall/delete the `istio-all` deployment:

```console
helm delete istio-all --namespace istio-system
kubectl get crd -oname | grep --color=never 'istio.io' | xargs kubectl delete
kubectl delete namespace istio-system
```

## Configuration

To view support configuration options and documentation, run:

```console
helm show values code4devs/istio-all
```
## Dependencies

By default this chart installs all dependent charts from `istio`, `kiali` & `prometheus` helm charts.
 

### Examples

#### Configuring mesh configuration settings

Any [Mesh Config](https://istio.io/latest/docs/reference/config/istio.mesh.v1alpha1/) options can be configured like below:

```yaml
meshConfig:
  accessLogFile: /dev/stdout
```

Any [ingress-gateway] options can be configured like below: 

```yaml
ingressgateway:
  service:
    annotations: 
      ...
```


Only [kiali-server](https://kiali.io/docs/installation/installation-guide/install-with-helm/#standalone-kiali-installation) will be installed and options can be configured like below: 

```yaml 
kiali: 
  deployment: 
     ...
```    

Minimal [prometheus] will be installed as part of this chart by disabling `alertmanager`, `kube-state-metrics`, `prometheus-node-exporter` and `prometheus-pushgateway`.  These options can be configured by providing necessary values like below: 


```yaml 
prometheus: 
  alertmanager:
    enabled: true
  ...
```  
