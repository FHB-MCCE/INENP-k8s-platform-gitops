# CloudNativePG

CloudNativePG provides the PostgreSQL operator used for tenant databases.

## Gate 3 Scope

- Install the CloudNativePG operator through Argo CD and the upstream Helm chart.
- Keep database `Cluster` resources out of Gate 3; tenant databases are introduced later through Crossplane.
- Keep Prometheus `PodMonitor` disabled until the monitoring scope is explicitly added.

## Upgrade Strategy

Operator upgrades are handled by GitOps:

1. Update the chart `targetRevision` in the Argo CD Application.
2. Let Argo CD reconcile the operator.
3. Test tenant database behavior in the staging tenant before promoting application changes.

Database instance upgrades will be handled through CloudNativePG-managed cluster specifications in the tenant composition.
