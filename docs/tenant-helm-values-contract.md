# Tenant Helm Values Contract

This document captures the Helm values that Crossplane will need to provide when a
TenantApplication claim is implemented.

## Frontend Chart

Chart repository:

```text
ghcr.io/fhb-mcce/inenp-k8s-platform-frontend
```

Required or tenant-specific values:

```yaml
image:
  tag: <frontend-image-tag>

imagePullSecrets:
  - name: frontend-ghcr-pull

runtimeConfig:
  backendApiUrl: https://api.<tenant>.inenp.naehrer.me
  metarApiUrl: https://avwx.rest/api/metar/{icao}
  avwxApiKey: ""

ingress:
  enabled: true
  className: nginx
  hosts:
    - host: <tenant>.inenp.naehrer.me
      paths:
        - path: /
          pathType: Prefix
  tls:
    - secretName: <tenant>-frontend-tls
      hosts:
        - <tenant>.inenp.naehrer.me
```

## Backend Chart

Chart repository:

```text
ghcr.io/fhb-mcce/inenp-k8s-platform-backend
```

Required or tenant-specific values:

```yaml
image:
  tag: <backend-image-tag>

secrets:
  existingSecretName: backend-app-secrets
  datasourceUrlKey: spring-datasource-url
  datasourceUsernameKey: spring-datasource-username
  datasourcePasswordKey: spring-datasource-password
  avwxApiKeyKey: avwx-api-key
```

The referenced backend secret is expected to be created through External Secrets
Operator from Google Secret Manager. Database credentials and API keys must not be
committed to Git.

## Crossplane Parameters

The later TenantApplication schema should expose at least:

```yaml
tenantName: <tenant>
environment: dev
frontendImageTag: <tag>
backendImageTag: <tag>
databaseSize: small
hostname: <tenant>.inenp.naehrer.me
```

Crossplane should create the namespace, the tenant database resources, the
ExternalSecret resources, and Helm releases or Argo CD applications that pass the
values above to the charts.

