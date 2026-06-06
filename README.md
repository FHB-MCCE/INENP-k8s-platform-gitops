# INENP Kubernetes Platform – GitOps

Argo CD App-of-Apps-Konfiguration und Kubernetes-Manifeste für die INENP Kubernetes Platform.

## Überblick

Dieses Repository enthält die GitOps-Definitionen für:

- **Argo CD** – App-of-Apps Bootstrap, ApplicationSets
- **cert-manager** – TLS-Zertifikate via ACME DNS-01
- **ExternalDNS** – Automatische DNS-Verwaltung
- **External Secrets Operator** – Secrets aus Google Secret Manager
- **CloudNativePG** – PostgreSQL-Operator für Datenbanken
- **Crossplane** – Multi-Tenancy via XRDs und Compositions
- **Tenant-Konfigurationen** – Namespace, Secrets, Ingress pro Tenant

## Voraussetzungen

- Kubernetes Cluster (GKE) provisioniert via [IaC-Repo](https://github.com/FHB-MCCE/INENP-k8s-platform-iac)
- `kubectl` konfiguriert
- Argo CD CLI (optional)

## Projektstruktur

```
.
├── apps/             # Argo CD App-of-Apps Definitionen
├── infrastructure/   # Platform-Operator Helm Releases
├── tenants/          # Tenant-spezifische Konfigurationen
├── .github/          # CI/CD Workflows
└── README.md
```

## CI/CD

Pull Requests werden automatisch validiert:
- YAML Linting
- Kubernetes Manifest Validierung (`kubeconform`)
- Helm Template Rendering

## Lizenz

Internes Hochschulprojekt – FH Burgenland INENP 2026.
