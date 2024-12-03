# ONE CLUSTER + 2 Namespaces(staging+production)

0.) Kind  
1.) Tree  
2.) cluster with two namespaces (kustomization prod and kustomization staging)  
3.) apps  
4.) flux-system  
5.) Debugging  
6.) Add new Kustomization to clusters/production_and_staging and follow by path       <<< whole folder  
6.) Add new Kustomization to clusters/production_and_staging  and follow by resources <<< one file  

# IMPORTANT INFO

1.) While doing this command
```
flux bootstrap github --owner=jkb91jkb91 --repository=flux_repo_monorepo --branch=monorepo_one_cluster --path=clusters/production_and_staging --personal  

```
This is important >>> 
```
--path=clusters/production_and_staging
```
This not only means flux-system will be created under this path BUT MOST IMPORATANT IS THAT HERE flux will look for Kustomization files  
apps_prod.yaml >>> kustomization   and GitRepository  
apps_staging.yaml >> kustomization and GitRepository  

Let's say you want to add another file here like:
apps_kustomization.yaml  >> You have to only add this file here . NOT UNDER flux-system/kustomization.yaml << you dont need do anything here.  

2.) path vs infrastructure
Zwroc uwage ze:
path >> jest wzgledne WZGLEDEM POCZATKU REPOZYTORIUM w kustomization  
resources >> jest wzgledne wzgledem pliku Kustomization  

# 0 Kind One Cluster
kind create cluster --name my-cluster --config kind-config-master-worker.yaml  
flux install  
IMPORTANT >>
kubectl create ns production  
kubectl create ns staging  

# 1.) Tree  

```
./
├── apps/
│   ├── base/
│   │   └── busybox/
│   ├── production/
│   │   └── kustomization.yaml      <<<< resources :- ../base/busybox, namespace: production(OVERWRITES)
│   └── staging/
│       └── kustomization.yaml      <<<< resources :- ../base/busybox, namespace: staging(OVERWRITES)
├── clusters/
│   └── production_and_staging/
│       ├── flux-system/
|           ├──   gotk-components.yaml
│           ├──   gotk-sync.yaml
│           └──   kustomization.yaml
│       ├── apps_prod.yaml           <<<< KUSTOMIZATION file prod    path=./apps/production
│       └── apps_staging.yaml        <<<< KUSTOMIZATION file staging path=./apps/staging
├── infrastructure/
│   └── controllers/
│       └── dashboard.yaml
└── README.md
```


# 2.) cluster with two namespaces (kustomization prod and kustomization staging)  
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


# 3.) apps  

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
Under base we have busybox full Helm Chart  
We also have to folders with another kustomization files  
-production/kustomizaton.yaml  
-staging/kustomization.yaml  
IMPORTANT >> namespace value WILL BE OVERWRITTEN IN base/busybox by what it in these kustomization files  

```
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
namespace: production
resources:
  - ../base/busybox
```

```
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
namespace: staging
resources:
  - ../base/busybox
```

# 4 flux-system in the end generation

```
├── clusters/
│   └── production_and_staging/
│       ├── flux-system/
|           ├──   gotk-components.yaml
│           ├──   gotk-sync.yaml
│           └──   kustomization.yaml
```
flux bootstrap github --owner=jkb91jkb91 --repository=flux_repo_monorepo --branch=monorepo_one_cluster --path=clusters/production_and_staging--personal  


# 5 Debugging  
```

kubectl logs -n flux-system deployment/flux-controller
kubectl logs -n flux-system deployment/helm-controller
kubectl logs -n flux-system deployment/source-controller

```

Pobranie zmian z repo szbkie
```
flux reconcile source git flux-system -n flux-system
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
# 6 Add infrastructure follow to Flux  

path >> oznacza sciezke jaka ma byc sledzona tutaj  
```
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: dashboard-kustomization
  namespace: flux-system
spec:
  interval: 5m
  path: ./infrastructure/controllers
  sourceRef:
    kind: GitRepository
    name: flux-system
  prune: true
  wait: true
  timeout: 5m0s
```


```
flux reconcile source git flux-system -n flux-system
```

