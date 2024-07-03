@TODO:

download `https://github.com/kubernetes/dashboard/blob/master/charts/kubernetes-dashboard/values.yaml`

modify `dashboard/charts/kubernetes-dashboard/values.yaml`
```
......
  ingress:
    enabled: true
    hosts:
      # Keep 'localhost' host only if you want to access Dashboard using 'kubectl port-forward ...' on:
      # https://localhost:8443
      - localhost
      - dashboard.ingress.com
    # - kubernetes.dashboard.domain.com
    ingressClassName: nginx
......
```

add repo and install
```bash
helm repo add kubernetes-dashboard https://kubernetes.github.io/dashboard/
helm upgrade --install kubernetes-dashboard \
     kubernetes-dashboard/kubernetes-dashboard \
     --create-namespace --namespace kubernetes-dashboard \
     -f dashboard/charts/kubernetes-dashboard/values.yaml
```

create service account
```bash
kubectl apply -f - <<EOF
---
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: dashboard-admin
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: dashboard-admin-cluster-role
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: dashboard-admin
    namespace: kubernetes-dashboard
EOF
```

generate token for service account
```bash
kubectl -n kubernetes-dashboard create token dashboard-admin
```