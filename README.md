# ONE CLUSTER + 2 Namespaces(staging+production)




# 1
We have only one cluster that have 2 Kustomization.yaml file
clusters/production_and_staging


FIRST KUSTOMIZATION FILE  >> apps_prod.yaml    >>> point  path: ./apps/production    <<< Count from main repo  
SECOND KUSTOMIZATION FILE >> apps_staging.yaml >>> point  path: ./apps/staging       <<< Count from main repo  

```
./ ├── apps/
   │ ├── base/ │
     │ └── busybox/ │ ├── production/ │ │ └── kustomization.yaml │ └── staging/ │ └── kustomization.yaml ├── clusters/ │ └── production_and_staging/ │ ├── flux-system/ │ ├── apps_prod.yaml │ └── apps_staging.yaml ├── infrastructure/ │ └── controllers/ │ └── dashboard.yaml
```
