# Memos on K3s

Kubernetes manifests for running Memos with PostgreSQL 17 on a two-node K3s cluster. The setup uses a dedicated namespace, Traefik ingress, Secrets and ConfigMaps, and persistent database storage.

## Architecture

```text
Client -> Traefik Ingress -> Memos Service -> Memos Deployment
                                           |
                                           v
                              PostgreSQL Service -> StatefulSet -> PVC
```

Memos is exposed through an ingress route. PostgreSQL stays behind a ClusterIP Service and stores data on a persistent volume.

## Deploy

Copy the example Secret to the Git-ignored `secret.yaml`, replace both `CHANGE_ME` values with the same strong password, and review the configuration. Do not commit real passwords or tokens. Then apply the manifests in order:

```bash
cp secret.example.yaml secret.yaml
# Edit secret.yaml to replace both CHANGE_ME values before applying it.
kubectl apply -f namespace.yaml
kubectl apply -f configmap.yaml
kubectl apply -f secret.yaml
kubectl apply -f postgres.yaml
kubectl apply -f memos.yaml
kubectl apply -f ingress.yaml
```

## Verify

```bash
kubectl get deployments,statefulsets,pods -n memos
kubectl get svc,ingress,pvc -n memos
kubectl describe pod -n memos <pod-name>
```

Check that the Memos Pod is ready, PostgreSQL has a bound PVC, and the ingress host resolves to the cluster ingress address. This example was designed for a small lab cluster, not a high-availability production deployment.
