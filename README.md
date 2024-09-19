#2 Clusters and 2 Namespaces


# KIND CLUSTERS
kind create cluster --name production --config kind-config-master-worker.yaml
flux install

kind create cluster --name staging --config kind-config-master-worker.yaml
flux install
