# fluxv2-infrastructure-stack-example

This project is inspired by https://github.com/fluxcd/flux2-kustomize-helm-example and some experience with Helm and Kubernetes.

Some usefull documentations : 

Flux V2 : https://toolkit.fluxcd.io/get-started/

Kubernetes : https://kubernetes.io/docs/home/

Helm : https://helm.sh/docs/

Kustomize : https://kubectl.docs.kubernetes.io/installation/kustomize/


This configuration is based on using 2 clusters (production, staging).
The goal is to have all "must have" application installed with Flux2 and Helm with officials/recommended repos.

Installed apps are : 

- Metrics apps : (Prometheus)
- Dashboard to see those metrics  (Grafana)
- Logs collector for nodes (Fluent-bit or Filebeat)
- App to store those logs (Elasticsearch)
- Dashboard to see those logs (Kibana)
- Ingress controller (ingress-nginx)  

> You should keep in mind what all of those apps are doing and look at their documentations carefully.
>
> This documentation is here to help you having a nice fleet of the basics using Helm and Flux2.

Why using FLux2 for all of these : 

FLux2 and Helm answer multiple questions you can ask yourself when you work in a company with a kubernetes infrastructure : 
- Where do I store all my "infrastructure" configuration ? 
- Why do I have to deploy those apps myself ?
- How to manage all of those apps on multiple clusters ? 
- How to upgrade those apps easily 
- How can I save my infrastructure with versionning 
- What are the softwares that I should always install and how to do it with multiple environments


Repository structure

```
├── README.md
├── clusters # Cluster configuration 
│   ├── staging # cluster environment config
│   └── production # cluster environment config
├── infrastructure
│   ├── base # Contains apps for infrastructure
│   │   ├── App # Application installed
│   └── staging # contains Custom environment configuration
│       └── values  # Custom values.yaml for staging environment
└── sources # Contains helm repository sources informations
```


# Installation Flux2 : 

Please refer to this documentation :

https://toolkit.fluxcd.io/guides/installation/

# Flux monitoring

## Using flux tools : 
Please refer to this documentation :

https://toolkit.fluxcd.io/guides/monitoring/

## "Manual" installation (wich is prefered here because we install Prometheus and Grafana with Helm)

As flux Documentation says : 

You can download their dashboard here : 
https://github.com/fluxcd/flux2/tree/main/manifests/monitoring/grafana/dashboards


# Infrastructure applications : 

## Values files :

```
.
├── production
│   ├── kustomization.yaml 
│   ├── kustomizeconfig.yaml 
│   └── values
│       ├── values-ingress-nginx.yaml
│       ├── values-prometheus-operator.yaml
└── staging
    ├── kustomization.yaml
    ├── kustomizeconfig.yaml
    └── values
        ├── values-ingress-nginx.yaml
        ├── values-prometheus-operator.yaml
```

Kustomize values : 
This section contains usage of configMapGenerator to centralize those files
```
configMapGenerator:
  - name: app-name
    files:
      - values.yaml=./values/values-app-name.yaml
    namespace: app-namespace
# You have to replace values with app_name and namespace value and add a list foreach apps you want to add
```

Kustomizeconfig enable HelmRelease values from ConfigMaps: 

```
nameReference:
- kind: ConfigMap
  version: v1
  fieldSpecs:
  - path: spec/valuesFrom/name
    kind: HelmRelease
- kind: Secret
  version: v1
  fieldSpecs:
  - path: spec/valuesFrom/name
    kind: HelmRelease
```

## Helm sources : 

We use here all most used or official sources.
Sources are in folder /sources.



## Apps :

This is just an example of what's work for a nice configuration with "must have" apps on your cluster.
You can use whatever you want instead of those apps (Loki, traefik, ... ). 
Some other configuration can/will be added here later.

### Namespaces :

Monitoring namespace contains all monitoring stack (prometheus, elasticsearch, grafana...). 
Ops namespace contains all ops tools like nginx-ingress.

### Dashboards : 
#### Grafana : 

Helm repo (grafana) : https://github.com/grafana/helm-charts/tree/main/charts/grafana

#### Kibana : 

Helm repo (elastic) : https://github.com/elastic/helm-charts/tree/master/kibana

--- 

### Logs-storage : 
#### Elasticsearch

Helm repo (elastic): https://github.com/elastic/helm-charts/tree/master/elasticsearch

#### Prometheus

Helm repo (prometheus-community) :  https://github.com/prometheus-community/helm-charts

--- 

### Ingress-controler : 
#### ingress-nginx :

Helm repo (ingress-nginx) : https://github.com/kubernetes/ingress-nginx/tree/master/charts/ingress-nginx
Why using Fluent-bit ? 

--- 

### Logs-collector : 
#### Fluent-bit

Helm repo (stable) : https://github.com/helm/charts/tree/master/stable/fluent-bit 

This chart is based on stable helm repository.
Fluent-bit is lightweight and usefull if you want to send your logs to multiple output (https://docs.fluentbit.io/manual/pipeline/outputs)

#### Filebeat

Helm repo : https://github.com/elastic/helm-charts/tree/master/kibana

This chart is on Elastic HelmRepository.
You can use it if you prefer staying with Elastic softwares.
Outputs : https://www.elastic.co/guide/en/beats/filebeat/current/configuring-output.html

--- 

# How to use this project : 

What you should know : 

> - You should not add this repository as a "gitsource" but fork it to your own git repository to avoid production trouble based on modifications on these repo.
> - I recommand you to use this on a fresh install of kubernetes or backup/remove all of your apps you already have before installing this. (you can use existing volumes inside your values files if the related project allows it)
> - Do not use it on a production environment without testing it localy / on a poc cluster first.
> - Use it at your own risks.

## prerequisite : 

- Fork this to your git repo
- Create token to connect Flux to your git repo.
- Whitelist your cluster on your git repo (if needed).
- Read some documentations about flux V2 and at least do the quick install.
- Read some documentations / helm charts of the installed applications.
- Install Flux on your computer (or use it with Docker, this project will not explain how to do it).

## Installation process : 

Example on staging with github : 

```
flux bootstrap github \
    --context=staging \
    --owner=${GITHUB_USER} \
    --repository=${GITHUB_REPO} \
    --branch=main \
    --personal \
    --path=clusters/staging
```

Example on production with gitlab on a folder named infrastructure inside the gitlab root directory : 

```
export GITLAB_TOKEN=<gitlab-token>
flux bootstrap gitlab \
    --context=production \
    --owner=infrastructure \
    --repository=flux-infrastructure-stack \
    --branch=main \
    --path=clusters/production \
    --token-auth --verbose
```