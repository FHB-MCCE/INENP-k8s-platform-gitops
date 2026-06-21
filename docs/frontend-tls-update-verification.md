# Frontend DNS, TLS und Update-Verifikation

Dieses Dokument bündelt die GitOps-Prüfung für den Frontend-Pfad.

## Argo CD Applications

```sh
kubectl -n argocd get applications demo-frontend staging-frontend ingress-nginx
```

Erwartung:

- `demo-frontend` ist `Synced / Healthy`
- `staging-frontend` ist `Synced / Healthy`
- `ingress-nginx` ist `Synced / Healthy`

## Tenant Status

```sh
kubectl -n tenants get tenantapplications
kubectl -n tenant-demo get pods
kubectl -n tenant-staging get pods
```

Erwartung:

- `demo` und `staging` sind `SYNCED=True` und `READY=True`
- Frontend Pods laufen jeweils `1/1`

## DNS und TLS

```sh
kubectl get ingress -A
kubectl -n tenant-demo get certificate demo-frontend-tls
kubectl -n tenant-staging get certificate staging-frontend-tls
```

Erwartung:

- Frontend-Ingresses haben eine Adresse
- Zertifikate sind `READY=True`
- DNS zeigt auf die ingress-nginx LoadBalancer-IP

## HTTPS Smoke Tests

```sh
curl -sS -o /dev/null -w 'demo_frontend %{http_code}\n' https://demo.inenp.naehrer.me/
curl -sS -o /dev/null -w 'staging_frontend %{http_code}\n' https://staging.inenp.naehrer.me/
curl -sS https://demo.inenp.naehrer.me/config.js
curl -sS https://staging.inenp.naehrer.me/config.js
```

Erwartung:

- Frontend URLs antworten mit HTTP `200`
- Demo `config.js` zeigt auf `https://api.demo.inenp.naehrer.me/api`
- Staging `config.js` zeigt auf `https://api.staging.inenp.naehrer.me/api`
- ausgelieferte Dateien enthalten keinen AVWX-Key und keine `avwx.rest`-URL

## Update- und Rollback-Pfad

Staging ist der Prüf-Tenant. Ein neues Frontend Image wird zuerst nur dort
angepinnt. Nach erfolgreichem Test wird derselbe unveränderliche SHA nach Demo
promoviert. Rollback erfolgt durch Zurücksetzen auf den vorherigen SHA in Git.

Demo bleibt während Staging-Tests unverändert.

