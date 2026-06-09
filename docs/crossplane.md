# Crossplane

Crossplane stellt die Grundlage für spätere Tenant-Claims, XRDs und Compositions bereit. Gate 3 installiert nur den Operator und die Kernprovider.

## Argo CD Applications

- `crossplane` installiert den Crossplane-Controller aus dem offiziellen Helm-Repository.
- `crossplane-providers` verwaltet die Provider-Pakete aus `infrastructure/crossplane`.

Die Provider-App nutzt eine spätere Sync-Wave, damit die Crossplane-CRDs zuerst verfügbar sind.

## Provider

- `provider-kubernetes` wird für Kubernetes-Ressourcen verwendet, die später aus Crossplane-Compositions erzeugt werden.
- `provider-helm` wird für spätere Helm-Releases in Tenant-Compositions vorbereitet.

## Workload Identity

Der Crossplane-ServiceAccount ist mit der Google Service Account `crossplane@dulcet-velocity-495612-j0.iam.gserviceaccount.com` annotiert. Die IAM-Bindung wird im IaC-Repo verwaltet.

## Verifikation

```powershell
kubectl -n argocd get application crossplane crossplane-providers
kubectl -n crossplane-system get pods
kubectl get providers.pkg.crossplane.io
```
