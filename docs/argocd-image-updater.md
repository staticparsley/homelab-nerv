# Argo CD Image Updater

## Purpose

Automatically deploy new versions of the Quote API without manually restarting Kubernetes Deployments.

## Architecture

quote-api
↓
GitHub Actions
↓
GitHub Container Registry (GHCR)
↓
Argo CD Image Updater
↓
Commit updated image tag to homelab-nerv
↓
Argo CD detects Git change
↓
Kubernetes Deployment updates automatically

## Design Decisions

### Separate repositories

- `quote-api` owns application code.
- `homelab-nerv` owns infrastructure and Kubernetes manifests.

This keeps application development independent from infrastructure.

### Semantic versioning

Images are published as:

- v0.1.0
- v0.1.1
- v0.2.0

rather than relying on `latest`.

### Git write-back

Image Updater commits changes back into Git rather than only modifying the live Application.

Git remains the single source of truth.

## Files

- apps/quote-api/kustomization.yaml
- platform/argocd/image-updaters/quote-api.yaml

## Verification

Commands used to verify that GitOps is functioning correctly.
