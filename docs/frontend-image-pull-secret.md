# Frontend ImagePullSecret Wiring

The frontend image is private in GHCR. Tenant namespaces therefore need a Kubernetes
`dockerconfigjson` pull secret that is synchronized from Google Secret Manager by
External Secrets Operator.

## Secret Source

Create one Google Secret Manager secret per environment:

```text
inenp-ghcr-frontend-pull
```

The secret value must be the complete `.dockerconfigjson` payload:

```json
{
  "auths": {
    "ghcr.io": {
      "username": "<github-user-or-bot>",
      "password": "<read-packages-token>",
      "auth": "<base64(username:token)>"
    }
  }
}
```

The token only needs `read:packages` for the private frontend image.

## Tenant Namespace Wiring

Each tenant namespace gets an `ExternalSecret` that materializes the pull secret as
`frontend-ghcr-pull`:

```yaml
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: frontend-ghcr-pull
  namespace: tenant-example
spec:
  refreshInterval: 1h
  secretStoreRef:
    kind: ClusterSecretStore
    name: gcp-secret-manager
  target:
    name: frontend-ghcr-pull
    creationPolicy: Owner
    template:
      type: kubernetes.io/dockerconfigjson
  data:
    - secretKey: .dockerconfigjson
      remoteRef:
        key: inenp-ghcr-frontend-pull
```

The frontend Helm chart already expects this secret by default:

```yaml
imagePullSecrets:
  - name: frontend-ghcr-pull
```

## Security Notes

- No GHCR token or rendered `.dockerconfigjson` is stored in Git.
- The Google Secret Manager secret is managed outside GitOps and only referenced by
  name.
- Tenant namespaces receive a pull-only token, not a package write token.
- If a token is rotated, External Secrets Operator refreshes the Kubernetes secret
  without a Git change.

