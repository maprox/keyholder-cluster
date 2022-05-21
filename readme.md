# K8s cluster setup for keyholder

## K3D

### create cluster
```bash
k3d cluster create -c cluster.yaml
```

### install cert manager
```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.7.1/cert-manager.yaml
```

### create pods
```bash
kubectl create namespace keyholder
kubectl apply -f cluster/secrets/
kubectl apply -f cluster/pods/
kubectl apply -f cluster/
```

---

### Rancher
```bash
helm repo add rancher-latest https://releases.rancher.com/server-charts/latest
helm repo update
kubectl create namespace cattle-system
helm install rancher rancher-latest/rancher \
  --debug \
  --namespace cattle-system \
  --set hostname=rancher.sunsay.ru \
  --set bootstrapPassword=admin \
  --set proxy=http://${proxy_host} \
  --set noProxy=127.0.0.0/8\\,10.0.0.0/8\\,cattle-system.svc\\,172.16.0.0/12\\,192.168.0.0/16\\,.svc\\,.cluster.local \
  --set ingress.tls.source=letsEncrypt \
  --set letsEncrypt.email=z@sunsay.ru \
  --set letsEncrypt.ingress.class=traefik
kubectl -n cattle-system rollout status deploy/rancher
```

### if doesn't start immediately run this one
```
kubectl rollout restart deployment -n cattle-system rancher
```
