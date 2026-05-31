# Argo CD App of Apps

This repository contains an Argo CD "App of Apps" deployment for a simple Kubernetes environment with Apache and Nginx applications.

## Overview

This repo is structured as an Argo CD umbrella application. The top-level Argo CD app points to child applications defined in the `applications/` folder, while the actual Kubernetes manifests for each service are stored in their own subdirectories.

## Repository Structure

- `bootstrap/`
  - `root-app.yaml` — the top-level Argo CD app definition that bootstraps the app-of-apps pattern.
- `applications/`
  - `apache-app.yaml` — Argo CD application manifest for Apache.
  - `nginx-app.yaml` — Argo CD application manifest for Nginx.
- `apache/`
  - `apache.yaml` — actual Kubernetes manifest(s) for the Apache service.
- `nginx/`
  - `nginx.yaml` — actual Kubernetes manifest(s) for the Nginx service.

## How It Works

1. `bootstrap/root-app.yaml` defines the root Argo CD app.
2. That root app references the `applications/` folder.
3. Each app manifest under `applications/` points to its corresponding service folder (`apache/` or `nginx/`).
4. Argo CD then syncs the child apps and deploys the Kubernetes resources.

## Deploying

1. Install Argo CD in your Kubernetes cluster.
2. Add this repo to Argo CD as a Git repository.
3. Create/sync the root application using `bootstrap/root-app.yaml`.
4. Argo CD will then deploy the child apps and underlying resources.

## Nginx App

The `nginx/nginx.yaml` manifest currently includes:

- `Deployment` for `nginx` with 3 replicas
- `Service` of type `NodePort` exposing port `80` on node port `30091`

## Notes

- Adjust `image`, `replicas`, and `nodePort` settings as needed.
- For production, consider using a `LoadBalancer` or Ingress resource instead of `NodePort`.
- Keep the child application paths aligned with the folder names under this repository.


repo: app_of_apps/
  ├─ bootstrap/root-app.yaml
  │    └─ Argo CD Application "root-app"
  │         ├─ source: repo/path=applications
  │         └─ dest: cluster=https://192.168.1.140:6443, namespace=argocd
  └─ applications/
       ├─ apache-app.yaml
       │    └─ Argo CD Application "apache-app"
       │         ├─ source: repo/path=apache
       │         └─ dest: cluster=https://192.168.1.140:6443, namespace=apache
       └─ nginx-app.yaml
            └─ Argo CD Application "nginx-app"
                 ├─ source: repo/path=nginx
                 └─ dest: cluster=https://192.168.1.140:6443, namespace=nginx

repo paths:
  ├─ apache/      -> Apache Kubernetes manifests
  └─ nginx/       -> Nginx Kubernetes manifests
                     ├─ Deployment nginx (3 replicas)
                     └─ Service nginx-svc (NodePort 30091)