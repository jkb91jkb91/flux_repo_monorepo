# ONE CLUSTER + 2 Namespaces(staging+production)



#1.) clusters/production_and_staging  
FIRST KUSTOMIZATION FILE  >> apps_prod.yaml    >>> point  path: ./apps/production    <<< Count from main repo  
SECOND KUSTOMIZATION FILE >> apps_staging.yaml >>> point  path: ./apps/staging       <<< Count from main repo  

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

#KUSTOMIZATION app_prod.yaml
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

#KUSTOMIZATION app_staging
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
