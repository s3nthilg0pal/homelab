# Copilot instructions for this repo

## Big picture
- This repo is a collection of per-app Kubernetes manifests managed with Kustomize; each top-level app folder is a self-contained bundle (e.g., [adgaurd/kustomization.yaml](../adgaurd/kustomization.yaml), [umami/kustomization.yaml](../umami/kustomization.yaml)).
- Most apps follow the pattern: `namespace` + `pvc` (if needed) + `deployment` + `service`, with optional `ingress`/`certificate`.
- Some apps use a base/overlay split (e.g., [home-assistant/kustomization.yaml](../home-assistant/kustomization.yaml) points to [home-assistant/base/kustomization.yaml](../home-assistant/base/kustomization.yaml)).

## Kustomize patterns to follow
- Keep resource lists explicit in each Kustomization; add new files to the `resources` list or they won’t be applied (see [adgaurd/kustomization.yaml](../adgaurd/kustomization.yaml)).
- Common labels are added at the Kustomize level for grouping (`app.kubernetes.io/*` in [umami/kustomization.yaml](../umami/kustomization.yaml) and [blocky/kustomization.yaml](../blocky/kustomization.yaml)).
- Image overrides are done via Kustomize `images` (example in [blocky/kustomization.yaml](../blocky/kustomization.yaml)).
- ConfigMaps are generated via `configMapGenerator` with file inputs (see [blocky/kustomization.yaml](../blocky/kustomization.yaml) and [blocky/config/blocky-config.yaml](../blocky/config/blocky-config.yaml)).
- Node pinning is applied with a strategic merge patch (see [blocky/node-selector.yaml](../blocky/node-selector.yaml)).

## Secrets and certificates
- Secrets are stored as SealedSecrets; update the encrypted payload in place and keep the `template.metadata` aligned (see [umami/sealedsecret.yaml](../umami/sealedsecret.yaml)).
- TLS uses cert-manager `Certificate` referencing a cluster issuer `home-ca-issuer` (see [sample/certificate.yaml](../sample/certificate.yaml) and [cert-manager/base/cluster-issuer.yaml](../cert-manager/base/cluster-issuer.yaml)).
- Ingress TLS references the matching `secretName` and host (see [sample/ingress.yaml](../sample/ingress.yaml)).

## Conventions by example
- Namespaces are usually declared per app in `namespace.yaml` and set in Kustomize (e.g., [adgaurd/namespace.yaml](../adgaurd/namespace.yaml) and [adgaurd/kustomization.yaml](../adgaurd/kustomization.yaml)).
- Deployments typically label pods with `app: <name>` and services select that label (see [feedpushr/deployment.yaml](../feedpushr/deployment.yaml) and [feedpushr/service.yaml](../feedpushr/service.yaml)).

## Tooling
- Dependency update automation is configured via Renovate at the repo root (see [renovate.json](../renovate.json)).
