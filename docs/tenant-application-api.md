# TenantApplication API

The `TenantApplication` claim is the GitOps entry point for tenant-specific
weather application instances. Gate 5 starts with the tenant API and a base
composition that creates the tenant namespace. Backend, frontend, database,
secrets, ingress, TLS and policy resources are added by the follow-up Gate 5
issues owned by the respective team members.

## API Group

```text
platform.inenp.at/v1alpha1
```

## Required Claim Parameters

| Field | Purpose |
|---|---|
| `tenantName` | DNS-safe tenant identifier and base for the namespace name. |
| `hostname` | Public frontend hostname, for example `demo.inenp.naehrer.me`. |
| `frontendImageTag` | Frontend image tag published to private GHCR. |
| `backendImageTag` | Backend image tag published to public GHCR. |

Optional parameters:

| Field | Default | Purpose |
|---|---|---|
| `environment` | `dev` | Environment label and later value selection. |
| `databaseSize` | `small` | T-shirt size for the later CloudNativePG tenant database. |

## Example Claim

```yaml
apiVersion: platform.inenp.at/v1alpha1
kind: TenantApplication
metadata:
  name: demo
  namespace: tenants
spec:
  parameters:
    tenantName: demo
    environment: demo
    hostname: demo.inenp.naehrer.me
    frontendImageTag: latest
    backendImageTag: latest
    databaseSize: small
```

The current base composition creates the namespace `tenant-<tenantName>` and
labels it with the tenant and environment. Later Gate 5 PRs extend the same
composition with tenant secrets, CloudNativePG resources, Helm releases,
Ingress/TLS and isolation policies.
