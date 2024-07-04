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

```bash
root@k8s-controller:/home/ubuntu# kubectl -n kubernetes-dashboard create token dashboard-admin
eyJhbGciOiJSUzI1NiIsImtpZCI6IlJmckEzbGhLTW5Sb211UEhrWUl4U1otWlE3VmdIYzdaUGhtdXJYWWxfQmMifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiXSwiZXhwIjoxNzIwMDYxMjMxLCJpYXQiOjE3MjAwNTc2MzEsImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsIiwia3ViZXJuZXRlcy5pbyI6eyJuYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsInNlcnZpY2VhY2NvdW50Ijp7Im5hbWUiOiJkYXNoYm9hcmQtYWRtaW4iLCJ1aWQiOiJkZDliOGU1NC1kMTJiLTRiMDEtOThmOC1kZjE4NWRjMTBmMTcifX0sIm5iZiI6MTcyMDA1NzYzMSwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50Omt1YmVybmV0ZXMtZGFzaGJvYXJkOmRhc2hib2FyZC1hZG1pbiJ9.RDgxuJsTWnZmbGkEF8UgEARfkYSZ1aTvjVa9jVcUiTl1RJV8Tda0piceivsD3BQ92VVpKpnRkTdmD6mco0Y7CjHxvSNZIDgpHMAi8QgsX4HCMxBPDw0EqcbKoJCdChq-NiPO0dr62eA6GDxWHXYs9MlkCfnBIOc4MjyiJc7MO1VNb_y3L25tRcVjYzP4PLS9PGOU80HMy7H7XFDfwLp1f81Dgt9bNY8Rvei4qE3VdcihpPPF4mOsyKRWHnBQmyW-JMMUXlC9fbX80H4k9jHiRnKrOvnRIUXsVNDY6cYarcvUMSDtSmGZo9cs95rgW0qqtk3-6bLuaUt9FXcgCSsS_w
```