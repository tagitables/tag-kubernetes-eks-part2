# Kubernetes Part 2 — AWS EKS Hands-On Activity

## Overview

This project demonstrates the deployment and exposure of a containerised HTTP application on a shared Amazon EKS cluster using Kubernetes.

## Technologies

- Amazon Elastic Kubernetes Service (EKS)
- Kubernetes and kubectl
- AWS CLI
- HashiCorp HTTP Echo
- Git and GitHub

## Namespace

`tag-eks-activity`

## Assignment Implementation

1. Connect to the shared Amazon EKS cluster.
2. Verify and configure the Kubernetes namespace.
3. Create a ConfigMap and Secret using imperative commands.
4. Inspect and verify the ConfigMap and Secret.
5. Deploy an HTTP echo application using a Kubernetes Deployment.
6. Expose the application through LoadBalancer, ClusterIP and NodePort Services.

## Kubernetes Resources

| Resource | Name | Configuration |
|---|---|---|
| Deployment | tag-sample-httpd-app | 1 replica |
| ConfigMap | sample-config | Welcome message |
| Secret | sample-secret | Demonstration password |
| LoadBalancer | tag-loadbalancer-service | Port 80 |
| ClusterIP | tag-clusterip-service | Port 8080 |
| NodePort | tag-nodeport-service | Service port 30001 |

The application listens on container port 5678.

## Deployment

```bash
kubectl apply -f httpd-deployment.yaml
kubectl apply -f httpd-loadbalancer-service.yaml
kubectl apply -f httpd-clusterip-service.yaml
kubectl apply -f httpd-nodeport-service.yaml
```

The ConfigMap and Secret must be created separately before deploying the application.

## Verification

```bash
kubectl get pods -n tag-eks-activity
kubectl get deployments -n tag-eks-activity
kubectl get svc -n tag-eks-activity
```

Expected HTTP response:

```text
Welcome to the EKS Activity
```

## Security Notes

Kubernetes Secret values are not committed to this repository. The activity uses a demo password.
