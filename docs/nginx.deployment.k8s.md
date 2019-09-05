# Nginx Deployment on k8s

Nginx is service is forward our requests to other services in the k8s.

To deploy nginx first create secrets

```
kubectl create secret generic nginx-conf --from-file=./nginx.conf --insecure-skip-tls-verify
```

then deploy nginx on k8s

```
kubectl create secret generic nginx-conf --from-file=./nginx.conf --insecure-skip-tls-verify
```

