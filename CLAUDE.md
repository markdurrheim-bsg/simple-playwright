# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**simple-playwright** is a minimal static website served through nginx in a containerized environment (Docker/Kubernetes). The site displays a developer's name via the `DEV_NAME` environment variable. It's designed as a testing ground for container orchestration platforms.

**Key characteristics:**
- Completely static HTML (no build process, no Node dependencies)
- Uses nginx-unprivileged for security in constrained environments (e.g., OpenShift)
- Includes Kubernetes manifests for deployment
- Automatically deployed to GitHub Pages when static files change
- Two pages: index.html (home) and another-page.html

## Development

### Local Preview

Use the VS Code Live Server extension (configured in `.vscode/settings.json` to serve from `/static` folder). Simply open the root in Live Server to preview at http://localhost:5500.

### File Structure

- **`/static/`** – HTML, CSS, images. Direct source for both Docker and GitHub Pages.
  - `index.html`, `another-page.html` – Main pages
  - `styles/` – CSS files (includes Pico CSS framework)
  - `images/` – Static images
- **`nginx.conf.template`** – Nginx config with `DEV_NAME` environment variable substitution via sub_filter
- **`Dockerfile`** – Uses `nginxinc/nginx-unprivileged` base, copies static files, sets ENV
- **`deploy.yaml`** – Kubernetes Deployment and Service; references `durrheimmark/simple-static-site:1.1` Docker image
- **`.github/workflows/static.yml`** – Auto-deploys static folder to GitHub Pages on main branch

### Docker

Build and run locally with DEV_NAME substitution:

```bash
docker build -t simple-playwright .
docker run --name=simple-playwright -e DEV_NAME="YOUR NAME" -p 9082:8080 simple-playwright
# Open http://localhost:9082
```

### Kubernetes

Apply manifests (works with kubectl or OpenShift oc):

```bash
kubectl apply -f deploy.yaml
# For OpenShift:
oc apply -f deploy.yaml -f openshift-route.yaml

# Port-forward to access locally:
kubectl port-forward service/simple-playwright-service 9082:8080
# OR on Minikube:
minikube service simple-playwright-service
```

### Playwright CLI

The repository includes `.claude/skills/playwright-cli/` with Playwright test generation and browser automation. Install globally before use:

```bash
npm install -g @playwright/cli
```

## Deployment & CI/CD

- **GitHub Pages:** Automatically triggered on push to main when files in `/static/` change
- **Docker Hub:** Pre-built image available at `durrheimmark/simple-static-site:1.1`
- **Kubernetes:** Update `deploy.yaml` image tag and reapply when pushing new images

## Notes for Future Work

- **DEV_NAME substitution** happens at runtime via nginx sub_filter (not build time), allowing dynamic content without rebuilds
- **Health checks** in Kubernetes probe `/health` endpoint (responds with "healthy")
- **.gitignore** excludes `.playwright-cli/` output (may contain credentials)
- The deployment uses resource requests/limits (50Mi memory, 10m CPU) appropriate for testing environments
