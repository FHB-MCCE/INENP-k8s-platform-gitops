# Tenant Frontend Release

TenantApplication claims deploy the weather frontend through a Crossplane-managed
Argo CD Application. The application points to the frontend repository and renders
the `charts/weather-app-frontend` chart into the tenant namespace.

## Hostname Convention

The `hostname` claim parameter is the public frontend hostname:

```text
<tenant>.inenp.naehrer.me
```

The backend runtime URL is derived from the same value:

```text
https://api.<tenant>.inenp.naehrer.me/api
```

This keeps the browser-facing frontend endpoint and API endpoint tenant-specific
while allowing the frontend chart to receive only runtime configuration.

## Helm Values

Crossplane patches tenant parameters into the frontend chart:

```yaml
image:
  tag: <frontendImageTag>

runtimeConfig:
  backendApiUrl: https://api.<hostname>/api

ingress:
  enabled: true
  className: nginx
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt-production
  hosts:
    - host: <hostname>
      paths:
        - path: /
          pathType: Prefix
  tls:
    - secretName: <tenant>-frontend-tls
      hosts:
        - <hostname>
```

The API base URL includes the backend `/api` prefix. Frontend service methods
append resource-specific paths such as `/user/`, `/<userId>/favorite/`, and
`/<userId>/<locationId>/metar/`.

The frontend chart keeps `frontend-ghcr-pull` as the default image pull secret.
That secret is materialized in tenant namespaces through External Secrets Operator
and Google Secret Manager, as described in
[frontend-image-pull-secret.md](frontend-image-pull-secret.md).

## Verification Scope

This composition change wires the desired resources. Runtime checks for DNS, TLS,
HTTPS reachability, frontend-to-backend communication, and update or rollback
behavior are tracked separately in issue #8.
