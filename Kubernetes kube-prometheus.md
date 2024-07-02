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
kubectl label node k8s-node1 ingress=on

helm install ingress-nginx ingress-nginx/ingress-nginx \
  --namespace ingress-nginx --create-namespace \
  --set controller.deploy.kind=DaemonSet \
  --set controller.nodeSelector.ingress="on" \
  --set controller.service.type=ClusterIP \
  --set controller.hostNetwork=true \
  --set controller.dnsPolicy=ClusterFirstWithHostNet \
  --set controller.admissionWebhooks.enabled=false
```

```bash
kubectl apply -f - <<EOF
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  namespace: monitoring
  name: prometheus-ingress
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
              number: 3000
  - host: prometheus.ingress.com
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: prometheus-k8s
            port: 
              number: 9090
  - host: alertmanager.ingress.com
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: alertmanager-main
            port: 
              number: 9093
EOF
```