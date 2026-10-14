# Kubernetes on AWS EKS | Deployment & Service Networking

Deploying and exposing a containerised HTTP application on Amazon Elastic Kubernetes Service (EKS), demonstrating Kubernetes workload management, configuration injection, service discovery, and AWS networking integration.

## Architecture

```text
                    HTTP Client
                         |
                         | HTTP :80
                         v
              AWS Load Balancer
                         |
                         v
             Kubernetes Services
              (tag-eks-activity)
                         |
           +-------------+-------------+
           |             |             |
      LoadBalancer    ClusterIP      NodePort
         :80            :8080         :30001
           |             |             |
           +-------------+-------------+
                         |
                  Target :5678
                         |
                 Kubernetes Pod
                         |
              HashiCorp HTTP Echo
                         |
                 ConfigMap + Secret
```

## Tech Stack

`AWS EKS` · `Kubernetes` · `AWS CLI` · `Linux` · `kubectl` · `YAML` · `Git` · `GitHub`

## Implementation

- **Container orchestration:** Deployed a containerised HTTP application using a Kubernetes Deployment with declarative YAML configuration.
- **Workload management:** Configured replica management, Pod labels, selectors, and container networking.
- **Configuration management:** Injected application configuration through a Kubernetes ConfigMap and Secret using environment variable references.
- **Service networking:** Implemented LoadBalancer, ClusterIP, and NodePort Services to demonstrate external exposure, internal service discovery, and node-level access.
- **AWS integration:** Provisioned an AWS load balancer through a Kubernetes `LoadBalancer` Service.
- **Infrastructure validation:** Used `kubectl` to inspect Deployments, Pods, Services, networking endpoints, and application logs.
- **Version control:** Maintained Kubernetes manifests and technical documentation using Git and GitHub.

## Kubernetes Resources

| Resource | Configuration |
|---|---|
| Namespace | `tag-eks-activity` |
| Deployment | `tag-sample-httpd-app` |
| Container image | `hashicorp/http-echo:latest` |
| Replicas | `1` |
| Container port | `5678` |
| ConfigMap | `sample-config` |
| Secret | `sample-secret` |
| LoadBalancer Service | `tag-loadbalancer-service` |
| ClusterIP Service | `tag-clusterip-service` |
| NodePort Service | `tag-nodeport-service` |

## Service Networking

Three Kubernetes Service types route traffic to the same application workload.

| Service | Cluster IP | Service Port | Allocated NodePort | Target Port |
|---|---|---|---|---|
| LoadBalancer | `10.100.139.187` | `80` | `32260` | `5678` |
| ClusterIP | `10.100.140.97` | `8080` | — | `5678` |
| NodePort | `10.100.199.165` | `30001` | `31927` | `5678` |

**LoadBalancer endpoint:**

`http://ab4abfdb34c1541bbbf5c1a8368c7686-666970539.us-east-1.elb.amazonaws.com`

The Services were confirmed as created in EKS. The external HTTP response remains to be verified. Cluster-assigned addresses and ports may change if Services are recreated.

## Repository Structure

```text
.
├── httpd-deployment.yaml
├── httpd-loadbalancer-service.yaml
├── httpd-clusterip-service.yaml
├── httpd-nodeport-service.yaml
├── README.md
└── .gitignore
```

## Deployment

**Prerequisites:** Access to an existing EKS cluster, configured AWS credentials, and `kubectl`. The namespace, ConfigMap, and Secret must exist before applying the Deployment.

```bash
# Configure EKS access
aws eks update-kubeconfig \
  --name shared-eks-cluster \
  --region us-east-1

# Apply Kubernetes manifests
kubectl apply -f httpd-deployment.yaml
kubectl apply -f httpd-loadbalancer-service.yaml
kubectl apply -f httpd-clusterip-service.yaml
kubectl apply -f httpd-nodeport-service.yaml

# Verify resources
kubectl get deployments -n tag-eks-activity
kubectl get pods -n tag-eks-activity
kubectl get svc -n tag-eks-activity
```

## Application Testing

The HTTP echo application is configured to return:

```text
Welcome to the EKS Activity
```

Test the internal ClusterIP Service using port forwarding:

```bash
kubectl port-forward \
  -n tag-eks-activity \
  service/tag-clusterip-service 8080:8080
```

From another terminal:

```bash
curl http://localhost:8080
```

Inspect the running workload:

```bash
kubectl logs deployment/tag-sample-httpd-app \
  -n tag-eks-activity

kubectl describe deployment tag-sample-httpd-app \
  -n tag-eks-activity
```

## Engineering Concepts Demonstrated

**Kubernetes workload lifecycle:** Deployment → ReplicaSet → Pod → Container

**Service abstraction:** Service selectors decouple application access from individual Pod IP addresses.

**Network exposure:** ClusterIP enables internal access, NodePort exposes a port on eligible nodes, and LoadBalancer integrates with AWS-managed networking.

**Configuration separation:** ConfigMaps and Secrets provide runtime configuration without embedding values directly in the container image.

**Declarative infrastructure:** Version-controlled YAML manifests define the desired Kubernetes resource configuration.
