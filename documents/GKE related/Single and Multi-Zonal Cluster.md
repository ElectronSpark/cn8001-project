# Zonal GKE Cluster

Zonal Cluster has only a single control plane in a single zone. 

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
