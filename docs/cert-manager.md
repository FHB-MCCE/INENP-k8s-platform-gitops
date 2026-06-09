# cert-manager

cert-manager is deployed through Argo CD from the upstream Jetstack Helm chart.

## Gate 3 Scope

- Install cert-manager with CRDs.
- Use Workload Identity for Cloud DNS DNS-01 challenges.
- Create both staging and production Let's Encrypt ClusterIssuers.
- Keep public ingress configuration for later frontend/TLS integration.

## Issuers

| Issuer | ACME Server | Purpose |
|---|---|---|
| `letsencrypt-staging` | Let's Encrypt staging | Safe validation and DNS-01 testing |
| `letsencrypt-production` | Let's Encrypt production | Final public certificates |

The ACME contact email is `scheifinger01@gmail.com`.
