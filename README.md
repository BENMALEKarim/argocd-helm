# argocd-helm

export PORT=5000
export NAMESPACE=dev-namespace
export APP_NAME=app1
export SERVICE_IP=$(kubectl get svc -n ${NAMESPACE} -l app=${APP_NAME} -ojsonpath='{.items[0].spec.clusterIP}')
curl -s http://${SERVICE_IP}:${PORT} -w "\n"

export POD_NAME=$(kubectl get pod -n ${NAMESPACE} -l app=${APP_NAME} -ojsonpath='{.items[0].metadata.name}')

kubectl exec -n prod-namespace ${POD_NAME} -- env 