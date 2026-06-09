# External Secrets Operator

External Secrets Operator synchronizes selected Google Secret Manager values into Kubernetes Secrets.

## Gate 3 Scope

- Install External Secrets Operator through Argo CD and the upstream Helm chart.
- Enable CRD installation.
- Use Workload Identity instead of static Google Cloud credentials.
- Keep the `ClusterSecretStore` in a separate Sebastian-owned issue so the IAM and secret-store convention remains visible.

## Workload Identity

The Kubernetes service account in namespace `external-secrets` is annotated with:

```text
iam.gke.io/gcp-service-account=external-secrets@dulcet-velocity-495612-j0.iam.gserviceaccount.com
```
