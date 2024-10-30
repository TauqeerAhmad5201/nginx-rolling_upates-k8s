# Rolling Update Strategy in Kubernetes

Rolling updates in Kubernetes allow you to update your applications incrementally with zero downtime. This strategy is typically used to update the application without taking it offline. Instead of bringing down all instances of an application at once, the rolling update gradually replaces pods one by one with the new version.

This README covers the basic setup and commands needed to perform rolling updates in Kubernetes.

## Prerequisites

1. Kubernetes cluster (Minikube or any Kubernetes setup).
2. `kubectl` installed and configured to interact with your Kubernetes cluster.
3. A deployment already set up for the application you want to update.

## Key Concepts

- **Rolling Update**: Gradually replaces old versions of the app with new versions without downtime.
- **Deployment**: A Kubernetes object used to manage a ReplicaSet, which provides rolling update support.
- **ReplicaSet**: Ensures the specified number of pod replicas are running at all times.

## Steps to Perform a Rolling Update

### 1. Create a Deployment

First, create a deployment for an example NGINX application (or any other app).

```bash
kubectl create deployment nginx-deployment --image=nginx:1.14
```

**Check the deployment status**

```bash
kubectl get deployments
```

**Check the pods running under this deployment:**

```bash
kubectl get pods -l app=nginx-deployment
```

### 2. Update the Deployment

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.16
```

Here:

- nginx-deployment is the name of the deployment.
- nginx=nginx:1.16 updates the image version of nginx to 1.16.

*Always good to give a annotation which helps us in revion that what was the change*

  ```bash
  kubectl annot deployment nginx-deployment kubernetes.io/change-cause="update image to nginx-1.16"
  ```

### 3. Monitor the Rolling Update

**Check the status of the rolling update:**

```bash
kubectl rollout status deployment/nginx-deployment
```

### 4. Verify the Rolling Update

**You can verify that the update was successful by checking the image version of the pods:**

```bash
kubectl get pods -l app=nginx-deployment -o wide
```

To check the history of deployments (useful for tracking changes):

```bash
kubectl rollout history deployment/nginx-deployment
```

### 5. Roll Back if Necessary

**If there’s an issue with the new version, you can roll back to the previous version using:**

```bash
kubectl rollout undo deployment/nginx-deployment --to-revision=<revision no>
```

### 6. Terms

- **maxUnavailable:** Specifies the maximum number of pods that can be unavailable during the update process.
- **maxSurge:** Specifies the maximum number of pods that can be created above the desired replicas count during the update.

### 7. Accessing the Application (Optional)

**If you're using Minikube, you can expose the service using:**

```bash
minikube service nginx-deployment --url
```

*Or, use `kubectl port-forward` to forward a port and access the application locally:*

```bash
kubectl port-forward svc/nginx-service 8080:80
```

# Kubernetes Command Summary

This repository provides a summary of essential Kubernetes commands for creating, updating, monitoring, and rolling back deployments in a Minikube environment.

## Commands Overview

| Command | Description |
| ------- | ----------- |
| `kubectl create deployment nginx-deployment --image=nginx:1.14` | Create a deployment |
| `kubectl set image deployment/nginx-deployment nginx=nginx:1.16 --record` | Update the deployment image |
| `kubectl rollout status deployment/nginx-deployment` | Monitor update status |
| `kubectl rollout history deployment/nginx-deployment` | View deployment history |
| `kubectl rollout undo deployment/nginx-deployment` | Roll back to previous version |
| `kubectl get pods -l app=nginx-deployment -o wide` | Verify updated pods |
| `minikube service nginx-deployment --url` | Access the service in Minikube |

