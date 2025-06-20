# Rede Lokal

## Install Binaries
Install ArgoCD CLI from the [release page](https://github.com/argoproj/argo-cd/releases/).

## Bootstrap Dev
Create a new cluster:
```
kind cluster create --name redelokal-local
kind export kubeconfig --name redelokal-local
```

Install ArgoCD:
```
helm upgrade --install argocd argo/argo-cd --namespace argocd --create-namespace -f argocd/values.yaml --version 8.0.17
```


Create the ArgoCD Application:
```

```

Create the Miniflux Secrets:
```

```



Create the ArgoCD app:
```
kubectl apply -f apps/app-of-apps-local.yaml
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



TODO
- Deletar os BDs do miniflux
- Criar banco/role miniflux manualmente e documentar
- Alterar a secret do miniflux para incluir dados de acesso ao banco


Delete it:
```
kubectl delete secret -n argocd argocd-initial-admin-secret
```


argocd account update-password

# TODO
- Longhorn
- Metrics
- Postgresql operator to managed databases for apps
    - Copy correct pg password (do not hardcode)
- Miniflux
    - Create PostgreSQL user / database
    - Create Miniflux deployment app with Helm
    - Expose Miniflux on ingress
    - Add DNS entry to Miniflux
    - Add certificate
- Dev env
- README
- Baixar binários

```
KUBESEAL_VERSION='0.30.0' # Set this to, for example, KUBESEAL_VERSION='0.30.0'
curl -OL "https://github.com/bitnami-labs/sealed-secrets/releases/download/v${KUBESEAL_VERSION:?}/kubeseal-${KUBESEAL_VERSION:?}-linux-amd64.tar.gz"
tar -xvzf kubeseal-${KUBESEAL_VERSION:?}-linux-amd64.tar.gz kubeseal
sudo install -m 755 kubeseal ./bin/kubeseal
```
