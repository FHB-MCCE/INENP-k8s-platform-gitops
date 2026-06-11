# Crossplane

Crossplane stellt die Grundlage für Tenant-Claims, XRDs und Compositions bereit.
Gate 3 installiert den Operator und die Kernprovider. Gate 5 ergänzt die
`TenantApplication` API und die erste Basis-Composition.

## Argo CD Applications

- `crossplane` installiert den Crossplane-Controller aus dem offiziellen Helm-Repository.
- `crossplane-providers` verwaltet die Provider-Pakete aus `infrastructure/crossplane`.
- `tenant-application-api` verwaltet ProviderConfigs, XRD und Basis-Composition aus `platform/crossplane`.

Die Provider-App und die Tenant-API nutzen spätere Sync-Waves, damit die
Crossplane-CRDs zuerst verfügbar sind.

## Provider

- `provider-kubernetes` wird für Kubernetes-Ressourcen verwendet, die später aus Crossplane-Compositions erzeugt werden.
- `provider-helm` wird für spätere Helm-Releases in Tenant-Compositions vorbereitet.

## TenantApplication

Das Claim-Schema ist in [tenant-application-api.md](tenant-application-api.md)
dokumentiert. Die erste Composition erzeugt den Tenant-Namespace. Secrets,
Datenbank, Backend, Frontend, Ingress, TLS und Policies werden in den
nachfolgenden Gate-5-Issues ergänzt.

## Workload Identity

Der Crossplane-ServiceAccount ist mit der Google Service Account `crossplane@dulcet-velocity-495612-j0.iam.gserviceaccount.com` annotiert. Die IAM-Bindung wird im IaC-Repo verwaltet.

## Verifikation

```powershell
kubectl -n argocd get application crossplane crossplane-providers tenant-application-api
kubectl -n crossplane-system get pods
kubectl get providers.pkg.crossplane.io
kubectl get xrd xtenantapplications.platform.inenp.at
```
