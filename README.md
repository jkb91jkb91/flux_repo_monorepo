# ONE CLUSTER + 2 Namespaces(staging+production)


#1.) Tree
#2.) cluster with two namespaces (kustomization prod and kustomization staging)
#3.) apps



#1.) Tree  

```
./
├── apps/
│   ├── base/
│   │   └── busybox/
│   ├── production/
│   │   └── kustomization.yaml
│   └── staging/
│       └── kustomization.yaml
├── clusters/
│   └── production_and_staging/
│       ├── flux-system/
│       ├── apps_prod.yaml
│       └── apps_staging.yaml
├── infrastructure/
│   └── controllers/
│       └── dashboard.yaml
└── README.md
```


#2.) cluster with two namespaces (kustomization prod and kustomization staging)  
KUSTOMIZATION file pointing to namespace production >>app_prod.yaml  
IMPORTANT >> path is count from BEGINNING of the repo  


```
├── clusters/
│   └── production_and_staging/
│       ├── flux-system/
│       ├── apps_prod.yaml
│       └── apps_staging.yaml
```

```
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: appsprod
  namespace: flux-system
spec:
  interval: 10m0s
  path: ./apps/production
  sourceRef:
    kind: GitRepository
    name: flux-system
  prune: true
  wait: true
  timeout: 5m0s
```

KUSTOMIZATION file pointing to namespace staging >> app_prod.yaml  
IMPORTANT >> path is count from BEGINNING of the repo  
```
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: appsstaging
  namespace: flux-system
spec:
  interval: 10m0s
  path: ./apps/staging
  sourceRef:
    kind: GitRepository
    name: flux-system
  prune: true
  wait: true
  timeout: 5m0s
```


#3.) apps  

```
./
├── apps/
│   ├── base/
│   │   └── busybox/
│   ├── production/
│   │   └── kustomization.yaml
│   └── staging/
│       └── kustomization.yaml
```
