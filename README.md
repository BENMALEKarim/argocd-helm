# argocd-helm

export PORT=5000
export NAMESPACE=dev-namespace
export APP_NAME=app1
export SERVICE_IP=$(kubectl get svc -n ${NAMESPACE} -l app=${APP_NAME} -ojsonpath='{.items[0].spec.clusterIP}')
curl -s http://${SERVICE_IP}:${PORT} -w "\n"

export POD_NAME=$(kubectl get pod -n ${NAMESPACE} -l app=${APP_NAME} -ojsonpath='{.items[0].metadata.name}')

kubectl exec -n prod-namespace ${POD_NAME} -- env 

helm repo add external-secrets https://charts.external-secrets.io

helm install external-secrets \
   external-secrets/external-secrets \
    -n external-secrets \
    --create-namespace \
    --set installCRDs=true

kubectl create secret generic awssm-secret --from-file=./access-key --from-file=./secret-access-key -n prod-namespace

### Demo scenario

#### Create stagging-namespace

```bash
    kubectl create ns stagging-namespace
```

#### Deploy app1 in stagging-namespace

```bash
    helm upgrade --install -n stagging-namespace app1-stagging --values environments/stagging-values.yaml --values apps/app1/environments/stagging-values.yaml
```
#### Check app1 release is deployed in stagging-namespace

```bash
    helm list --namespace stagging-namespace

```

#### Check app1 resources are deployed in stagging-namespace

```bash
    kubectl get deployment -n stagging-namespace
    kubectl get cm -n stagging namespace
    kubectl get service -n stagging-namespace

```

#### Curl app1 in stagging-namespace

```bash
    export PORT=5000
    export NAMESPACE=stagging-namespace
    export APP_NAME=app1
    export SERVICE_IP=$(kubectl get svc -n ${NAMESPACE} -l app=${APP_NAME} -ojsonpath='{.items[0].spec.clusterIP}')
    curl -s http://${SERVICE_IP}:${PORT} -w "\n"

```


#### Update app1 image tag in stagging-namespace

```bash
    vi apps/app1/environments/stagging-values.yaml
```

#### Upgrade app1 image in stagging-namespace

```bash
    helm upgrade --install -n stagging-namespace app1-stagging --values environments/stagging-values.yaml --values apps/app1/environments/stagging-values.yaml
```