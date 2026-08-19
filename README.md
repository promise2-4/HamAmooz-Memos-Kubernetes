# Memos Kubernetes Deployment

This repository contains the Kubernetes manifests for deploying **Memos** with **PostgreSQL 17** on a two-node **K3s cluster**.

The purpose of this project was to deploy a complete application stack on Kubernetes while following basic Kubernetes best practices, including resource isolation, configuration management, secret handling, persistent storage, and ingress-based access.


## Cluster

The application is deployed on the following K3s cluster:

| Node | Role |
|------|------|
| hemmasian-ha1 | Control Plane |
| hemmasian-ha2 | Worker |


## Architecture

```
                         Internet
                            |
                            |
                    Traefik Ingress
                            |
                            |
                    Memos Service
                            |
                            |
                  Memos Deployment
                            |
                            |
                PostgreSQL Service
                            |
                            |
              PostgreSQL StatefulSet
                            |
                            |
                  Persistent Storage
```

All application components are deployed inside a dedicated Kubernetes namespace:

```
memos
```

This keeps the resources related to this application isolated from other workloads in the cluster.


## Components

### Memos

- Deployed using a Kubernetes Deployment.
- Exposed internally through a ClusterIP Service.
- Accessible externally through Traefik Ingress.

### PostgreSQL 17

- Deployed using a StatefulSet.
- Uses persistent storage through a PersistentVolumeClaim.
- Database access is limited to the internal Kubernetes network.

### Configuration Management

Application configuration is separated from the application manifests:

- **ConfigMap**
  - Stores non-sensitive configuration values.

- **Secret**
  - Stores sensitive information such as database credentials and connection strings.


## Access

The application is exposed using Kubernetes Ingress:

```
http://hemmasian.osdl.ir
```


## Deployment

Apply the manifests in the following order:

```bash
kubectl apply -f namespace.yaml
kubectl apply -f configmap.yaml
kubectl apply -f secret.yaml
kubectl apply -f postgres.yaml
kubectl apply -f memos.yaml
kubectl apply -f ingress.yaml
```


## Notes

- PostgreSQL is not exposed outside the cluster and is only reachable by internal services.
- Persistent storage is used to keep application and database data after pod recreation.
- Secrets are not committed with the repository and should be created separately before deployment.
