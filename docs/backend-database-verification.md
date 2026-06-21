# Backend- und Datenbank-Verifikation

Dieses Dokument fasst die Gate-7-Prüfung für Backend, CloudNativePG und den
Tenant-Datenfluss zusammen.

## Ziel

Nach Gate 6 laufen Demo und Staging als getrennte Tenant-Instanzen. Diese
Verifikation zeigt, dass Backend, Datenbank und Secrets korrekt verbunden sind,
ohne Secret-Werte offenzulegen.

## Relevante Ressourcen

| Tenant | Namespace | Backend Application | Datenbank |
|---|---|---|---|
| Demo | `tenant-demo` | `demo-backend` | CloudNativePG Cluster im Tenant-Namespace |
| Staging | `tenant-staging` | `staging-backend` | CloudNativePG Cluster im Tenant-Namespace |

Aktiver Backend Image-SHA:

```text
f5feb13942519da31cfac8f87ebdd58fd0cc0784
```

## Kubernetes Checks

```sh
kubectl -n argocd get applications demo-backend staging-backend
kubectl -n tenants get tenantapplications
kubectl -n tenant-demo get pods
kubectl -n tenant-staging get pods
kubectl -n tenant-demo get clusters.postgresql.cnpg.io
kubectl -n tenant-staging get clusters.postgresql.cnpg.io
```

Erwartung:

- `demo-backend` und `staging-backend` sind `Synced / Healthy`.
- `demo` und `staging` sind `SYNCED=True` und `READY=True`.
- Backend- und Datenbank-Pods laufen jeweils `1/1`.
- CloudNativePG meldet pro Tenant einen bereiten Cluster.

## Secret Checks

Nur Secret-Namen und ExternalSecret-Status prüfen:

```sh
kubectl -n tenant-demo get externalsecrets
kubectl -n tenant-staging get externalsecrets
kubectl -n tenant-demo get secrets
kubectl -n tenant-staging get secrets
```

Nicht erlaubt:

- Secret-Payloads mit `kubectl get secret -o yaml` ausgeben
- Secret-Werte base64-dekodieren
- Secret-Werte in Screenshots, Issues oder Logs posten

## HTTPS Smoke Tests

```sh
curl -sS -o /dev/null -w 'demo_api %{http_code}\n' https://api.demo.inenp.naehrer.me/actuator/health
curl -sS -o /dev/null -w 'staging_api %{http_code}\n' https://api.staging.inenp.naehrer.me/actuator/health
curl -sS -o /dev/null -w 'demo_users %{http_code}\n' https://api.demo.inenp.naehrer.me/api/user/
curl -sS -o /dev/null -w 'staging_users %{http_code}\n' https://api.staging.inenp.naehrer.me/api/user/
```

Erwartung: Alle Aufrufe antworten mit HTTP `200`.

## Datenfluss

1. Frontend ruft die tenant-spezifische Backend-URL mit `/api` auf.
2. Backend liest Datenbank- und AVWX-Konfiguration aus Secrets.
3. User, Favorite Locations und Forecast werden über die Tenant-Datenbank
   beziehungsweise Backend-Services verarbeitet.
4. METAR wird serverseitig über den Backend-Endpunkt geladen.

Der AVWX-Schlüssel wird nicht an den Browser ausgeliefert.

## Bekannte Besonderheit

`tenant-application-api` kann wegen von Crossplane ergänzten Feldern als
`OutOfSync / Healthy` erscheinen. Das ist kein Gate-7-Blocker, solange Root-App,
TenantApplications und konkrete Frontend-/Backend-Applications gesund sind.

## Gate-7 Nachweis

Für Gate 7 gilt die Backend-/Datenbank-Verifikation als abgeschlossen, wenn:

- beide TenantApplications `READY=True` sind
- beide Backend Applications gesund sind
- beide Datenbankcluster bereit sind
- Health-, User-, Forecast- und METAR-Flows über HTTPS funktionieren
- keine Secret-Payloads in Dokumentation oder Issues auftauchen

