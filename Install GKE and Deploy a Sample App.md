# Install GKE and Deploy a Sample App

Google Kubernetes Engine (GKE) is a managed [Kubernetes](https://kubernetes.io/) service that you can use to deploy and operate containerized applications at scale using Google's infrastructure. 

In this section, we are going to install GKE in Google Cloud Platform and test it.

## Before it begins

1. In the Google Cloud console, on the project selector page, select or create a Google Cloud Project.
2. Enable Artifact Registry API and Kubernetes Engine API

## ![Screenshot1](.\images\Install_GKE\Screenshot1.png)Launch Cloud Shell

Go to the Google Cloud console and activate the Cloud Shell, and set the default project to the one we are working on.

![Lauchshell](.\images\Install_GKE\Lauchshell.png)

## Create a GKE cluster

In this section, we are going to Create an Autopilot cluster and name it `cluster-sample`

```sh
gcloud container clusters create-auto cluster-sample \
    --location=us-central1
```

After several minutes, we can see it successfully installed and running.

![installsuccess](.\images\Install_GKE\installsuccess.png)

