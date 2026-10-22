# Simple static site

A simple two-page static site served with nginx.

Display your name by setting the `DEV_NAME` environment variable.

Preview of the site with no dev set: https://markdurrheim.github.io/simple-static-site/

Pre-built Docker image: https://hub.docker.com/r/durrheimmark/simple-static-site

## Playwright CLI

Ensure that you have `playwright-cli` installed

```shell
npm install -g @playwright/cli
```

## Local Dev

You can do quick and easy local previews with the [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) VS Code Extension.

The workspace setting to target the `/static` folder is already set up.

## Docker
Build the image
```shell
docker build -t simple-playwright .
```

Run in Docker
```shell
docker run --name=simple-playwright -e DEV_NAME="YOUR NAME" -p 9082:8080 simple-playwright
```

Open in browser (PowerShell)
```shell
start http://localhost:9082
```

## Kubernetes

Apply configuration to create a deployment and service to expose it
```shell
kubectl apply -f .\deploy.yaml

# OR for OpenShift

oc apply -f .\deploy.yaml -f .\openshift-route.yaml
```

Enable port-forwarding for the service at http://localhost:9082
```shell
kubectl port-forward service/simple-playwright-service 9082:8080
```

Minikube can port-forward and open with the command
````shell
minikube service simple-playwright-service
````
