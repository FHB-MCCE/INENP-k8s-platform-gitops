# Argo CD Repository Credentials

Argo CD needs repository credentials to render Helm charts from private GitHub
repositories. The frontend repository is private, so the frontend tenant
Applications require an Argo CD repository Secret in the `argocd` namespace.

The `argocd-repository-credentials` Application syncs an ExternalSecret that
reads the `argocd-frontend-repo-credentials` value from Google Secret Manager and
materializes the `argocd-frontend-repo` Secret with the Argo CD repository label.

The Secret Manager value must be provided manually and must not be committed to
GitHub. Store it as JSON:

```json
{
  "username": "jmoritz24",
  "password": "<fine-grained-token-with-frontend-repository-read-access>"
}
```

This credential is separate from `frontend-ghcr-pull-token`. The GHCR pull token
is used by Kubernetes to pull container images, while the Argo CD repository
credential is used by Argo CD to read the private frontend Git repository.
