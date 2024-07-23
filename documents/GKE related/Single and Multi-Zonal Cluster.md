# Zonal GKE Cluster

Zonal Cluster has only a single control plane in a single zone. GKE manages the control plane and system components when using standard mode.

## Single Zonal Cluster

A single Control plane and all work nodes run in one zone. If the zone running the cluster expriences an outage, the whole cluster would become unavailable.

The following CLI command creates a single-zonal cluster, where its control plane and work nodes locates in `us-central1-a`.

```bash
gcloud container clusters create single-zonal \
    --release-channel stable \
    --zone us-central1-a \
    --node-locations us-central1-a
```

![](../../images/gke_zonal_cluster/single_zonal_cluster_structure.drawio.png)

## Multi Zonal Cluster

Control plane in a single zone. Work nodes in multiple zones. Workloads keep running during an outage of a zone. However, if the zone running the control plane shutsdown, the whole cluster become unconfigurable untill its control plane restores.

The follwing CLI command creates a muti-zonal cluster, where its control plane locates in zone `us-central1-a`, and its work nodes locate in `us-central1-a`, `us-central1-b`.

```bash
gcloud container clusters create multi-zonal \
    --release-channel stable \
    --zone us-central1-a \
    --node-locations us-central1-a,us-central1-b \
    --num-nodes 2
```

When I was trying to create a multi regional cluster in 3 zones with 3 nodes each zone, and the GCloud complained an `Insufficient regional quota`. However, since my account is on free trial, and free trial accounts have limited quota during their trial period, I have to specify the number of nodes per zone to 2.

![](../../images/gke_zonal_cluster/multi_zonal_cluster_structure.drawio.png)

## Zonal Outage Demo

获得之前创建的 multi-zonal cluster 的 credential

```bash
gcloud container clusters get-credentials multi-zonal --region us-central1
```

创建并进入目录 `zonal_outage`

```bash
mkdir zonal_outage && cd zonal_outage
```

1. 创建一个 Docker 镜像
首先，创建一个 Dockerfile 来构建你的 Flask 应用的 Docker 镜像。

```Dockerfile
Copy code
# 使用官方的 Python 镜像作为基础镜像
FROM python:3.12-slim

# 设置工作目录
WORKDIR /app

# 复制当前目录的内容到工作目录
COPY . .

# 安装依赖
RUN pip install --no-cache-dir flask

# 设置环境变量
ENV FLASK_APP=app.py

# 暴露端口
EXPOSE 5000

# 运行 Flask 应用
CMD ["flask", "run", "--host=0.0.0.0"]
```

首先，确保你已经启用了 Artifact Registry API：

```bash
gcloud services enable artifactregistry.googleapis.com
```

然后创建一个 Docker 仓库：

```bash
gcloud artifacts repositories create my-repo --repository-format=docker --location=us-central1 --description="My Docker repository"
```

配置 Docker 以使用 Artifact Registry：

```bash
gcloud auth configure-docker us-central1-docker.pkg.dev
```

执行以下命令获得当前的 Project ID

```bash
gcloud config get-value project
```

将输出的 Project ID 写入环境变量 `GCP_PROJECT_ID`

```bash
export GCP_PROJECT_ID=atlantean-wares-426921-j3
```

1. 构建并推送 Docker 镜像到 Artifact Registry
构建 Docker 镜像：

```bash
docker build -t us-central1-docker.pkg.dev/$GCP_PROJECT_ID/my-repo/flask-app:latest .
```

将镜像推送到 Artifact Registry：

```bash
docker push us-central1-docker.pkg.dev/$GCP_PROJECT_ID/my-repo/flask-app:latest
```

1. 部署 Flask 应用到 GKE
创建一个 Kubernetes 部署文件 daemonset.yaml，将镜像地址更新为 Artifact Registry 地址：

daemonset.yaml:

```yaml
---
apiVersion: v1
kind: Service
metadata:
  name: web-service
spec:
  selector:
    app: flask-demo
  ports:
  - name: flask-demo
    protocol: TCP
    port: 80
    targetPort: 5000
  type: LoadBalancer
---
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: web-gke
spec:
  selector:
    matchLabels:
      app: flask-demo
  template:
    metadata:
      labels:
        app: flask-demo
    spec:
      containers:
      - name: flask-app
        image: us-central1-docker.pkg.dev/atlantean-wares-426921-j3/my-repo/flask-app:latest
        ports:
        - containerPort: 5000
        resources:
          requests:
            memory: "0.5Gi"
            cpu: "100m"
            ephemeral-storage: "0.5Gi"
          limits:
            memory: "1Gi"
            cpu: "500m"
            ephemeral-storage: "1Gi"
```

应用这个部署文件：

```bash
kubectl apply -f daemonset.yaml
```

1. 访问你的 Flask 应用
执行以下命令查看外部 IP 地址：

```bash
kubectl get services
```

你会看到类似下面的输出：

```bash
NAME            TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
flask-service   LoadBalancer   10.0.0.1       35.123.45.67    80:31337/TCP   5m
```

在浏览器中访问 http://35.123.45.67 (替换为实际的外部 IP 地址)，你应该能看到你的 Flask 应用返回的内容。


```bash
  gcloud container node-pools update default-pool \
    --cluster=multi-zonal \
    --node-locations=us-central1-a \
    --region=us-central1-a
```


https://cloud.google.com/kubernetes-engine/docs/tutorials/simulate-zone-failure