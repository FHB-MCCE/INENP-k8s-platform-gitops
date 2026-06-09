# ExternalDNS

ExternalDNS automates DNS records for Kubernetes Services and Ingresses in Cloud DNS.

## Gate 3 Scope

- Deploy ExternalDNS through Argo CD and the upstream Helm chart.
- Use Google Cloud DNS as provider.
- Restrict management to `inenp.naehrer.me`.
- Start with `upsert-only` so records are created and updated but not deleted.
- Use Workload Identity instead of static service account keys.

## Settings

| Setting | Value |
|---|---|
| Provider | Google Cloud DNS |
| Domain filter | `inenp.naehrer.me` |
| Policy | `upsert-only` |
| Registry | TXT |
| TXT owner ID | `inenp-dev-gke` |
| Google project | `dulcet-velocity-495612-j0` |
