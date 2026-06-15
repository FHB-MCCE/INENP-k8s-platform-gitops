# Ingress NGINX

The tenant backend and frontend Helm releases create Kubernetes Ingress
resources with `className: nginx`. The platform therefore installs the
`ingress-nginx` controller as an Argo CD managed platform application.

The controller exposes a Google Cloud LoadBalancer Service, creates the `nginx`
IngressClass, and publishes the LoadBalancer address back to tenant Ingress
status. ExternalDNS can then create DNS records for `demo.inenp.naehrer.me`,
`staging.inenp.naehrer.me`, and their API hosts.

The chart is pinned to `4.15.1` and runs two controller replicas. Tenant
Ingress resources remain owned by the TenantApplication composition and tenant
application charts.
