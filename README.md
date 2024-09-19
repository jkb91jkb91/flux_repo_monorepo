# 2 Clusters and 2 Namespaces


# KIND TWO CLUSTERS
kind create cluster --name production --config kind-config-master-worker.yaml  
flux install  
kubectl create ns production

kind create cluster --name staging --config kind-config-master-worker.yaml  
flux install  
kubectl create ns staging


# Tree

```
./
├── apps/
│   ├── base/
│   │   └── busybox/
│   │       ├── charts/
│   │       ├── kustomization.yaml     <<<< resources :- release.yaml
│   │       ├── release.yaml
│   │       └── repository.yaml
│   ├── production/
│   │   └── kustomization.yaml         <<<< resources :- ../base/busybox, namespace: production(OVERWRITES)
│   └── staging/
│       └── kustomization.yaml         <<<< resources :- ../base/busybox, namespace: staging(OVERWRITES)
├── clusters/
│   ├── production/
│   │   ├── flux-system/
│   │   │   ├── gotk-components.yaml
│   │   │   ├── gotk-sync.yaml
│   │   │   └── kustomization.yaml
│   │   └── apps.yaml                   <<<< KUSTOMIZATION FILE path=./apps/production
│   └── staging/
│       ├── flux-system/
│       │   ├── gotk-components.yaml
│       │   ├── gotk-sync.yaml
│       │   └── kustomization.yaml
│       ├── apps.yaml                   <<<< KUSTOMIZATION FILE path=./apps/staging
│       └── infra-controllers.yaml
└── infrastructure/
    └── controllers/
        └── dashboard.yaml
```

# Bootstrap staging

flux bootstrap github --owner=jkb91jkb91 --repository=flux_repo_monorepo --path=clusters/staging --branch=TwoClusters_per_TwoNamespaces  --personal  

```
├── clusters/
│   └── production/
│       ├── flux-system/
│       │   ├── gotk-components.yaml
│       │   ├── gotk-sync.yaml
│       │   └── kustomization.yaml
```
# Bootstrap production

flux bootstrap github --owner=jkb91jkb91 --repository=flux_repo_monorepo --path=clusters/staging --branch=TwoClusters_per_TwoNamespaces  --personal  

```
├── clusters/
│   └── staging/
│       ├── flux-system/
│       │   ├── gotk-components.yaml
│       │   ├── gotk-sync.yaml
│       │   └── kustomization.yaml
```


# Check Helmreleases
CLUSTER STAGING >> kubectl get helmreleases -n staging  

CLUSTER PRODUCTION >> kubectl get helmreleases -n production  
