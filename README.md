# ONE CLUSTER + 2 Namespaces(staging+production)




# 1
We have only one cluster that have 2 Kustomization.yaml file
clusters/production_and_staging


FIRST KUSTOMIZATION FILE  >> apps_prod.yaml    >>> point  path: ./apps/production    <<< Count from main repo  
SECOND KUSTOMIZATION FILE >> apps_staging.yaml >>> point  path: ./apps/staging       <<< Count from main repo  


repo/ ├── base/ │ ├── deployment.yaml │ ├── kustomization.yaml │ └── service.yaml ├── flux-system/ │ ├── gotk-components.yaml │ ├── gotk-sync.yaml │ └── kustomization.yaml ├── production/ │ └── kustomization.yaml ├── staging/ │ └── kustomization.yaml └── README.md
