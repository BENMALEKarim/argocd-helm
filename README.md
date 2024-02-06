# argocd-helm



### Demo scenario

#### Create stagging-namespace

```bash
kubectl create ns stagging-namespace
```

#### Deploy app1 in stagging-namespace

```bash
export APP_NAME=mock-payment
export NAMESPACE=stagging-namespace
export ENV=stagging
```

```bash
helm upgrade --install -n ${NAMESPACE} ${APP_NAME}-${ENV} \
--values environments/${ENV}-values.yaml \
--values apps/${APP_NAME}/environments/${ENV}-values.yaml \
apps/${APP_NAME}/
```
#### Check app1 release is deployed in stagging-namespace

```bash
helm list --namespace ${NAMESPACE}
```

#### Check app1 resources are deployed in stagging-namespace

```bash
kubectl get deployment -n ${NAMESPACE}
kubectl get cm -n ${NAMESPACE}
kubectl get service -n ${NAMESPACE}

```

#### Curl app1 in stagging-namespace

```bash
export PORT=5000
export SERVICE_IP=$(kubectl get svc -n ${NAMESPACE} -l app=${APP_NAME} -ojsonpath='{.items[0].spec.clusterIP}')
curl -s http://${SERVICE_IP}:${PORT} -w "\n"
```


#### Update app1 image tag in stagging-namespace

```bash
vi apps/${APP_NAME}/environments/${ENV}-values.yaml
```

#### Upgrade app1 image in stagging-namespace

```bash
helm upgrade --install -n ${NAMESPACE} ${APP_NAME}-${ENV} \
--values environments/${ENV}-values.yaml \
--values apps/${APP_NAME}/environments/${ENV}-values.yaml \
apps/${APP_NAME}/
```

#### Curl ${APP_NAME} in stagging-namespace after upgrade

```bash
curl -s http://${SERVICE_IP}:${PORT} -w "\n"
```

#### Rollback app1 in to previous version stagging-namespace

```bash
helm rollback -n ${NAMESPACE} ${APP_NAME}-${ENV}
```

#### Check app1-stagging release values

```bash
helm get values -n ${NAMESPACE} ${APP_NAME}-${ENV}
```

#### Curl app1 in stagging-namespace after rollback

```bash
curl -s http://${SERVICE_IP}:${PORT} -w "\n"
```

#### Issue: in my GitRepo stored a verison but in my Cluster another version !!!

```bash
cat apps/${APP_NAME}/environments/${ENV}-values.yaml
```

#### Clean everything

```bash
helm uninstall -n ${NAMESPACE} ${APP_NAME}-${ENV}
kubectl delete ns ${NAMESPACE}
```

### END OF DEMO

### Install ESO

```bash

helm repo add external-secrets https://charts.external-secrets.io

helm install external-secrets \
   external-secrets/external-secrets \
    -n external-secrets \
    --create-namespace \
    --set installCRDs=true

kubectl create secret generic awssm-secret --from-file=./access-key --from-file=./secret-access-key -n prod-namespace

```