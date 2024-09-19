# 2 Clusters and 2 Namespaces


# KIND CLUSTERS
kind create cluster --name production --config kind-config-master-worker.yaml  
flux install  

kind create cluster --name staging --config kind-config-master-worker.yaml  
flux install  


# Tree

```
./
├── apps/
│   ├── base/
│   │   └── busybox/
│   │       ├── charts/
│   │       ├── kustomization.yaml
│   │       ├── release.yaml
│   │       └── repository.yaml
│   ├── production/
│   │   └── kustomization.yaml
│   └── staging/
│       └── kustomization.yaml
├── clusters/
│   ├── production/
│   │   ├── flux-system/
│   │   │   ├── gotk-components.yaml
│   │   │   ├── gotk-sync.yaml
│   │   │   └── kustomization.yaml
│   │   └── apps.yaml
│   └── staging/
│       ├── flux-system/
│       │   ├── gotk-components.yaml
│       │   ├── gotk-sync.yaml
│       │   └── kustomization.yaml
│       ├── apps.yaml
│       └── infra-controllers.yaml
└── infrastructure/
    └── controllers/
        └── dashboard.yaml
```

# Bootstrap staging

flux bootstrap github --owner=jkb91jkb91 --repository=flux_training_delete --path=clusters/staging --branch=TwoClusters_per_TwoNamespaces  --personal  

```
├── clusters/
│   └── production/
│       ├── flux-system/
│       │   ├── gotk-components.yaml
│       │   ├── gotk-sync.yaml
│       │   └── kustomization.yaml
```
# Bootstrap production

flux bootstrap github --owner=jkb91jkb91 --repository=flux_training_delete --path=clusters/staging --branch=TwoClusters_per_TwoNamespaces  --personal  

```
├── clusters/
│   └── staging/
│       ├── flux-system/
│       │   ├── gotk-components.yaml
│       │   ├── gotk-sync.yaml
│       │   └── kustomization.yaml
```
