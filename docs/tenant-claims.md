# Tenant Claims

The `tenant-claims` Argo CD Application syncs the `tenants/` directory after the
TenantApplication API has been installed. Claims live in the `tenants` namespace
and Crossplane reconciles each claim into one isolated tenant stack.

## Included Claims

| Tenant | Environment | Hostname | Database size |
|---|---|---|---|
| `staging` | `staging` | `staging.inenp.naehrer.me` | `small` |
| `demo` | `demo` | `demo.inenp.naehrer.me` | `small` |

Staging is the Gate 6 canary and pins the backend and frontend images that move
METAR access behind the backend API. Demo remains on the previous image tags
until staging users, locations, forecast, METAR, and browser secret handling are
verified. Image tags are promoted independently through TenantApplication
claims rather than mutable `latest` rollouts.

## Provisioned Resources

Each claim asks Crossplane to provision:

- a tenant namespace named `tenant-<tenant>`
- backend database and backend Argo CD Application
- frontend Argo CD Application, ingress, and TLS values
- ExternalSecret resources for backend values and the frontend image pull secret
- resource limits and tenant ingress isolation policies

Runtime checks for Argo CD sync, Crossplane reconciliation, application health,
DNS, TLS, secrets, and NetworkPolicies are tracked by the Gate 5 verification
issues #4, #8, and #18.
