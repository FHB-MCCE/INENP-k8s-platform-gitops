# ClusterSecretStore

The `gcp-secret-manager` ClusterSecretStore connects External Secrets Operator to Google Secret Manager.

## Gate 3 Scope

- Use the existing External Secrets Operator installation.
- Authenticate through GKE Workload Identity.
- Avoid static Google service account keys.
- Provide one cluster-wide store for platform and tenant ExternalSecrets.

## Store

| Field | Value |
|---|---|
| Name | `gcp-secret-manager` |
| Provider | Google Secret Manager |
| Project | `dulcet-velocity-495612-j0` |
| Cluster | `inenp-dev-gke` |
| Location | `europe-west3-a` |
| Kubernetes ServiceAccount | `external-secrets/external-secrets` |
