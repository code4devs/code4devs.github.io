# istio-data-plane Helm Chart

This is an consolidated helm chart to install necessary components in **Ambient** based Istio service mesh  such as Ingress Gateway, Egress Gateway and Kiali, Prometheus for Monitoring purposes. 

This chart also inluded with Kubernetes Gateway API v1.2.1.

Ingress gateway is based on Kubenertes Gateway API and required HPA and poddisruptionbudget. Consumers should be able to define required Routing configuration, on top of this Ingress Gateway. 

Egress gateway is based on Ambient Waypoint proxy. It also provides ability to define egress routing configuration, with or without TLS origination for egress routes.

You can customize the installation by overriding values from dependent istio, kiali and prometheus values.
 

## Setup Repo Info

```console
helm repo add code4devs https://code4devs.github.io/charts/
helm repo update
```

_See [helm repo](https://helm.sh/docs/helm/helm_repo/) for command documentation._

## Installing the Chart

**Prerequisite:** Istio Data plane should be installed using **Ambient Mode**

This chart will use namespace name as 'istio-system' to standardize naming standards across all components. 

To install the chart with the release name `istio-data-plane`:

```console
helm install istio-data-plane code4devs/istio-data-plane --namespace istio-system
```

## Uninstalling the Chart

To uninstall/delete the `istio-data-plane` deployment:

```console
helm delete istio-data-plane --namespace istio-system
```

## Configuration

To view support configuration options and documentation, run:

```console
helm show values code4devs/istio-data-plane
```
## Dependencies

By default this chart installs all dependent charts from  `kiali` & `prometheus` helm charts.
 

### Example Vaues for Ingress Gateway 

```yaml

namespace: istio-ingress
nameoverride: "ingress-gateway"  

#Define gateway listerners and their configuration based on 
gateway: 
  listeners: 
    - name: http
      hostname: "*.example.com"
      protocol: HTTP
      port: 80
      allowedRoutes:
        namespaces:
          from: All

    - name: https
      port: 443
      protocol: HTTPS
      hostname: "*.example.com"

#Defines Infrastucture labels and annotations required for Ingress gateway       
infrastructure:
  annotations:
    ....
  labels:
    ....

# Auto scaling settings
autoscaling:
  enabled: true
  minReplicas: 1
  maxReplicas: 3
  targetCPUUtilizationPercentage: 80
  targetMemoryUtilizationPercentage: 80

#PDB Settings
podDisruptionBudget:
    minAvailable: 1
    unhealthyPodEvictionPolicy: AlwaysAllow      
```

### Example Vaues for Egress Gateway 

```yaml
namespace: istio-egress

gateway: 
  name: egress-waypoint
  for: all  # waypoint associated with 'service', 'workload', 'all'
  
infrastructure:
  annotations:
    ....
  labels:
    ....

#Auto Scalling settings
autoscaling:
  enabled: false
  minReplicas: 1
  maxReplicas: 2
  targetCPUUtilizationPercentage: 80
  targetMemoryUtilizationPercentage: 80

#PDB Settings
podDisruptionBudget:
  minAvailable: 1
  unhealthyPodEvictionPolicy: AlwaysAllow

#Egress Routing configuration 
egress:
  certs: 
    enabled: true 
    secretName: egress-waypoint-certs
    certFile: ./cert.pem
    keyFile: ./key.pem
    caFile: ./ca.pem
  routes:
  - name: route1
    protocol: https
    targetPort: 443  #used for TLS originization port from egress waypoint 
    port: 80
    hostnames: 
    - egress.route1.com
    - egress.route2.com
    #Either principals based on Istio standards or Namespaces required for proper authorization policies
    principals: 
    - cluster.local/ns/app-ns/sa/app-sa    
    # namespaces:
    #   - app-backend
    tlsMode: SIMPLE  # TLS Originaiton mode 
    caCert: /var/certs/ca-cert.pem  # CA Cert file 
    credentialName: egress-waypoint-certs  # Certificate using secrete 
```


Only [kiali-server](https://kiali.io/docs/installation/installation-guide/install-with-helm/#standalone-kiali-installation) will be installed and options can be configured like below: 

```yaml 
kiali: 
  deployment: 
     ...
```    

Minimal [prometheus](https://istio.io/latest/docs/ops/integrations/prometheus/) will be installed as part of this chart by disabling `alertmanager`, `kube-state-metrics`, `prometheus-node-exporter` and `prometheus-pushgateway`.  These options can be configured by providing necessary values like below: 

```yaml 
prometheus: 
  alertmanager:
    enabled: true
  ...
```  