kubectl delete deployment -l app=redis
kubectl delete service -l app=redis
kubectl delete deployment frontend
kubectl delete service frontend



kubectl port-forward svc/frontend -n default 8080:443
kubectl port-forward svc/frontend 8080:80

