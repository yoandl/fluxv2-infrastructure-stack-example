# fluxv2-monitoring-stack

Repository structure

```
├── README.md
├── clusters # Cluster configuration 
│   └── staging
│       ├── flux-system
│       ├── infrastructure.yaml
│       └── sources.yaml
├── infrastructure
│   ├── base # Contains apps for infrastructure
│   │   ├── filebeat
│   │   │   ├── kustomization.yaml
│   │   │   ├── namespace.yaml
│   │   │   └── release.yaml
│   │   ├── ingress-controller
│   │   │   ├── kustomization.yaml
│   │   │   ├── namespace.yaml
│   │   │   └── release.yaml
│   │   ├── prometheus-operator
│   │   │   ├── kustomization.yaml
│   │   │   ├── namespace.yaml
│   │   │   └── release.yaml
│   │   └── redis
│   │       ├── kustomization.yaml
│   │       ├── namespace.yaml
│   │       └── release.yaml
│   └── staging # contains Custom environment configuration
│       ├── kustomization.yaml
│       ├── kustomizeconfig.yaml
│       └── values  # Custom values.yaml for staging environment
│           ├── values-ingress-nginx.yaml
│           ├── values-prometheus-operator.yaml
│           └── values-redis.yaml
└── sources # Contains helm repository sources informations
    ├── bitnami.yaml
    ├── elastic.yaml
    ├── ingress-nginx.yaml
    ├── kustomization.yaml
    ├── prometheus-community.yaml
    └── stable.yaml

```
