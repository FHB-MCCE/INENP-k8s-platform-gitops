# TenantApplication API

The `TenantApplication` claim is the GitOps entry point for tenant-specific
weather application instances. The Gate 5 composition creates the tenant
namespace, database, backend release, frontend release, secrets, ingress, TLS,
resource controls, and isolation policies.

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

The composition creates the namespace `tenant-<tenantName>` and labels it with
the tenant and environment. The `tenants/` directory contains concrete staging
and demo claims for GitOps-driven provisioning.
