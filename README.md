# argocd-helm
curl -s http://$(kubectl get svc -n dev-namespace app3-service -ojsonpath='{.spec.clusterIP}'):5000 -w "\n"