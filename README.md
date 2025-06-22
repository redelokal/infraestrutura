# Rede Lokal

## Install Binaries
Install ArgoCD CLI from the [release page](https://github.com/argoproj/argo-cd/releases/).

## Bootstrap Dev
Create a new cluster:
```
kind create cluster --name redelokal-local
kind export kubeconfig --name redelokal-local
```

Install ArgoCD:
```
helm upgrade --install argocd argo/argo-cd --namespace argocd --create-namespace -f argocd/values.yaml --version 8.0.17
```

Create the ArgoCD Application:
```
kubectl apply -f app/app-of-apps-local.yaml
```

Create the Miniflux Secrets:
```
export MINIFLUX_ADMIN_USERNAME=<username>
export MINIFLUX_ADMIN_PASSWORD=<password>
export MINIFLUX_DB_USERNAME=miniflux # hardcoded
export MINIFLUX_DB_PASSWORD=<db-password>
export MINIFLUX_DB_DATABASE=miniflux # hardcoded
export MINIFLUX_DB_HOSTNAME=postgresql-01-rw.postgresql.svc.cluster.local # hardcoded

# Create the Secret for the database
kubectl create secret generic \
  miniflux-auth \
  --namespace postgresql \
  --from-literal=username=$MINIFLUX_DB_USERNAME \
  --from-literal=password=$MINIFLUX_DB_PASSWORD

# Create the Secret for the application
kubectl create secret generic \
  miniflux-auth \
  --namespace miniflux \
  --from-literal=ADMIN_USERNAME=$MINIFLUX_ADMIN_USERNAME \
  --from-literal=ADMIN_PASSWORD=$MINIFLUX_ADMIN_PASSWORD \
  --from-literal=DATABASE_URL="host=$MINIFLUX_DB_HOSTNAME user=$MINIFLUX_DB_USERNAME password=$MINIFLUX_DB_PASSWORD dbname=$MINIFLUX_DB_DATABASE sslmode=disable"
```

Connect to ArgoCD:
```
kubectl port-forward svc/argocd-server -n argocd 9051:443
```

Get initial ArgoCD password:
```
argocd admin initial-password -n argocd
```

Login into ArgoCD:
```
argocd login localhost:9051
```



Install the App of Apps:
```
.bin/argocd app create apps-of-apps \
    --dest-namespace argocd \
    --dest-server https://kubernetes.default.svc \
    --repo https://github.com/redelokal/infraestrutura.git \
    --path apps/apps
```


Delete it:
```
kubectl delete secret -n argocd argocd-initial-admin-secret
```


argocd account update-password

# TODO
- Longhorn
- Metrics
- Dev env
    - SVC TYPE LB
    - StorageClass cnpg
- README
- Baixar binários

```
KUBESEAL_VERSION='0.30.0' # Set this to, for example, KUBESEAL_VERSION='0.30.0'
curl -OL "https://github.com/bitnami-labs/sealed-secrets/releases/download/v${KUBESEAL_VERSION:?}/kubeseal-${KUBESEAL_VERSION:?}-linux-amd64.tar.gz"
tar -xvzf kubeseal-${KUBESEAL_VERSION:?}-linux-amd64.tar.gz kubeseal
sudo install -m 755 kubeseal ./bin/kubeseal
```
