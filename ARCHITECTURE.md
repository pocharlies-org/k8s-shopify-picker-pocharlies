# ARCHITECTURE — k8s-shopify-picker-pocharlies

Despliegue del «picker» (integración Picqer y recomendación de compras). Código en `pocharlies/skirmshop-picqer` (fuera de la tanda, tiene `CONTRACTS.yaml`).

## Clientes y versiones
- Un servicio web `skirmshop-picker` (puerto 3480, `skirmshop.e-dani.com/picker`, ns `skirmshop`) y CronJobs. Tronco: `main` (Application `shopify-picker`, path `k8s`).

## Dependencias (ambos sentidos)
- Base `pocharlies/k8s-shopify-framework-pocharlies//base?ref=deploy/prod` (+ `routing.yaml` propio: middlewares y dos rutas, admin con SSO); imagen `harbor.e-dani.com/homelab/skirmshop-picker`; Picqer; Postgres compartido; RabbitMQ; GA4 (`externalsecret-ga4.yaml`); feedback del brain (`externalsecret-brain-feedback.yaml`).

## Stack
Kustomize con base remota; sin Helm.

## Componentes compartidos
Base del framework (caso «bespoke admin tool»: routing y SSO propios, como `examples/picker-admin`); `scripts/verify-scopes.sh` + `k8s/expected-scopes.txt`.

## Cómo se construye
`k8s/kustomization.yaml`, `routing.yaml`, `cronjobs.yaml`: `picklist_close` horario, `purchase_recommend` lunes 06:00, refresco de señales 04:00, snapshot GA4 03:30, poda de snapshots 05:00.

## Tests y validaciones
`reusable-ci.yml`; `scripts/verify-scopes.sh`.

## CI/CD y despliegue
`ci.yml`, `release.yml` (`reusable-manifest-release`), `pr-review.yml`. ArgoCD lee `main`.

## Decisiones y trampas
- El `CLAUDE.md` de `skirmshop-picqer` dice que el tronco es `deploy/prod`; el medido es `main`.
- El picker es una herramienta interna (SSO), no una app de comercio embebida estándar.
