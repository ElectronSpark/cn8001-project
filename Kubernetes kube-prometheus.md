# Kubernetes Kube-Prometheus

## installation

Its git repository: https://github.com/prometheus-operator/kube-prometheus


```bash
sed -e "s/- --bind-address=127.0.0.1/- --bind-address=0.0.0.0/" -i /etc/kubernetes/manifests/kube-controller-manager.yaml
sed -e "s/- --bind-address=127.0.0.1/- --bind-address=0.0.0.0/" -i /etc/kubernetes/manifests/kube-scheduler.yaml
```

Clone kube-prometheus repository.

```bash
git clone https://github.com/prometheus-operator/kube-prometheus
cd kube-prometheus/
```

```bash
kubectl apply --server-side -f manifests/setup
kubectl wait \
	--for condition=Established \
	--all CustomResourceDefinition \
	--namespace=monitoring
kubectl apply -f manifests/
```

```bash
kubectl apply -f - <<EOF
---
apiVersion: v1
kind: Secret
metadata:
  name: basic-auth
  namespace: monitoring
data: 
  auth: dXNlcjokYXByMSRpdjlUU20uZiRSdHUuSm5IRFMuQUx5eUVOZEN1TkYxCg==
type: Opaque
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  namespace: monitoring
  name: prometheus-ingress
  annotations:
    nginx.ingress.kubernetes.io/auth-type: basic
    nginx.ingress.kubernetes.io/auth-secret: basic-auth
    nginx.ingress.kubernetes.io/auth-realm: 'Authentication Required'
spec:
  ingressClassName: "nginx"
  rules:
  - host: grafana.ingress.com
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: grafana
            port: 
              name: http
  - host: prometheus.ingress.com
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: prometheus-k8s
            port: 
              name: web
  - host: alertmanager.ingress.com
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: alertmanager-main
            port: 
              name: web
EOF
```