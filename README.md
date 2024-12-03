# 2 Clusters and 2 Namespaces (Helm Charts(busybox,grafana) + common manifests(pod,service))


# KIND TWO CLUSTERS
kind create cluster --name production --config kind-config-master-worker.yaml  
~~flux install~~    >>> NOT NEEDED IF BOOTSTRAP WILL BE LATER LIKE IN THIS CASE
kubectl create ns production

kind create cluster --name staging --config kind-config-master-worker.yaml  
~~flux install~~   >>> NOT NEEDED IF BOOTSTRAP WILL BE LATER LIKE IN THIS CASE
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


# How to add new helm chart to base
cd base && mkdir grafana_app
cd grafana_app && helm create grafana
cd grafana && helm pull bitnami/grafana --untar

CHANGE production/kustomization.yaml and staging/kustomization.yaml
```
./
├── apps/
│   ├── base/
│   │   └── grafana_app/
│   │       ├── charts/
│   │       ├── kustomization.yaml     <<<< resources :- release.yaml
│   │       ├── release.yaml           <<<<
│   │       └── repository.yaml
│   ├── production/
│   │   └── kustomization.yaml         <<<< resources :- ../base/grafana_app, namespace: production(OVERWRITES)
│   └── staging/
│       └── kustomization.yaml         <<<< resources :- ../base/grafana_app, namespace: staging(OVERWRITES)
```

# How to add new app based on manifests to base
cd base && mkdir apache

```
./
├── apps/
│   ├── base/
│   │   └── apache/
│   │       ├── kustomization.yaml     <<<< resources: - deployment.yaml, - service.yaml
│   │       ├── deployment.yaml        
│   │       └── service.yaml
│   ├── production/
│   │   └── kustomization.yaml         <<<< resources :- ../base/apache, namespace: production(OVERWRITES)
│   └── staging/
│       └── kustomization.yaml         <<<< resources :- ../base/apache, namespace: staging(OVERWRITES)
```

#  Debugging  
```

kubectl logs -n flux-system deployment/flux-controller
kubectl logs -n flux-system deployment/helm-controller
kubectl logs -n flux-system deployment/source-controller

```

Delete flux-system
```
kubectl get namespace flux-system -o json > flux-system.json


"finalizers": [
    "kubernetes"
]

kubectl replace --raw "/api/v1/namespaces/flux-system/finalize" -f flux-system.json

kubectl delete namespace flux-system --force --grace-period=0

```
