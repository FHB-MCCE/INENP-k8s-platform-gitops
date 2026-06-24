# INENP Kubernetes Platform - GitOps

Argo CD App-of-Apps-Konfiguration und Kubernetes-Manifeste für die INENP Kubernetes Platform.

## Überblick

Dieses Repository enthält die GitOps-Definitionen für:

- **Argo CD** - App-of-Apps Bootstrap, ApplicationSets
- **cert-manager** - TLS-Zertifikate via ACME DNS-01
- **ExternalDNS** - Automatische DNS-Verwaltung
- **External Secrets Operator** - Secrets aus Google Secret Manager
- **CloudNativePG** - PostgreSQL-Operator für Datenbanken
- **Crossplane** - Multi-Tenancy via XRDs und Compositions
- **Tenant-Konfigurationen** - Namespace, Secrets, Ingress pro Tenant

## Voraussetzungen

- Kubernetes Cluster (GKE) provisioniert via [IaC-Repo](https://github.com/FHB-MCCE/INENP-k8s-platform-iac)
- `kubectl` konfiguriert
- Argo CD CLI (optional)

## Projektstruktur

```text
.
|-- infrastructure/   # Cluster-weite Infrastruktur (Argo CD, cert-manager, ExternalDNS, ESO)
|-- platform/         # Platform-Services (Crossplane, CloudNativePG Operator)
|-- apps/             # Argo CD App-of-Apps Definitionen
|-- tenants/          # Tenant-spezifische Konfigurationen (Namespaces, Claims)
|-- docs/             # Bootstrap- und Betriebsdokumentation
|-- .github/          # CI/CD Workflows
`-- README.md
```

## Argo CD Bootstrap

Der Gate-3-Bootstrap ist in [docs/argocd-bootstrap.md](docs/argocd-bootstrap.md) dokumentiert. Argo CD wird zunächst nur intern betrieben; öffentliche Erreichbarkeit folgt später über DNS und cert-manager.

## Plattform-Operatoren

- cert-manager: [docs/cert-manager.md](docs/cert-manager.md)
- External Secrets Operator: [docs/external-secrets.md](docs/external-secrets.md)
- CloudNativePG: [docs/cloudnative-pg.md](docs/cloudnative-pg.md)
- ExternalDNS: [docs/external-dns.md](docs/external-dns.md)
- ClusterSecretStore: [docs/cluster-secret-store.md](docs/cluster-secret-store.md)
- Crossplane: [docs/crossplane.md](docs/crossplane.md)
- Backend/DB-Verifikation: [docs/backend-database-verification.md](docs/backend-database-verification.md)
- Frontend/TLS-Verifikation: [docs/frontend-tls-update-verification.md](docs/frontend-tls-update-verification.md)
- TenantApplication API: [docs/tenant-application-api.md](docs/tenant-application-api.md)
- Tenant backend and database: [docs/tenant-backend-database.md](docs/tenant-backend-database.md)
- Tenant frontend release: [docs/tenant-frontend-release.md](docs/tenant-frontend-release.md)
- Tenant isolation policies: [docs/tenant-isolation-policies.md](docs/tenant-isolation-policies.md)
- Tenant claims: [docs/tenant-claims.md](docs/tenant-claims.md)

## CI/CD

Pull Requests werden automatisch validiert:

- YAML Linting
- Kubernetes Manifest Validierung (`kubeconform`)
- Helm Template Rendering

## Lizenz

Internes Hochschulprojekt - FH Burgenland INENP 2026.
