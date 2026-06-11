# Tenant Backend And Database

Gate 5 extends the `TenantApplication` composition with backend and database
resources. This work belongs to GitOps issue #3.

## Resources

For every tenant claim, the composition now creates:

- an `ExternalSecret` named `backend-app-secrets`
- a CloudNativePG `Cluster` named `<tenant>-db`
- an Argo CD `Application` named `<tenant>-backend`

The backend release is installed from the backend repository Helm chart at
`charts/weather-app-backend`. It receives the tenant-specific image tag from the
claim and uses the `backend-app-secrets` Kubernetes Secret for database and AVWX
configuration.

## Secret Contract

Secret values stay in Google Secret Manager. The tenant namespace receives only
the materialized Kubernetes Secret from External Secrets Operator.

Required Google Secret Manager entries:

| Secret | Purpose |
|---|---|
| `database-app-password` | Password for the tenant application database owner. |
| `avwx-api-key` | AVWX API token used by the backend application. |

The generated Kubernetes Secret contains:

| Key | Consumer |
|---|---|
| `username` | CloudNativePG bootstrap owner user. |
| `password` | CloudNativePG bootstrap owner password. |
| `spring-datasource-url` | Backend datasource URL. |
| `spring-datasource-username` | Backend datasource user. |
| `spring-datasource-password` | Backend datasource password. |
| `avwx-api-key` | Backend AVWX API key. |

## Backend Endpoint

The backend chart enables Ingress for:

```text
api.<tenant-hostname>
```

The Ingress uses the `letsencrypt-production` ClusterIssuer. ExternalDNS and
cert-manager reconcile the DNS record and TLS certificate after the Argo CD
Application syncs.
