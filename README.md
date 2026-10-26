# vProfile: Docker & Kubernetes Deployment

Containerising and deploying a multi-tier Java web app (the open-source [vProfile](https://github.com/hkhcoder/vprofile-project) app) with **Docker, Docker Compose and Kubernetes**.

## Architecture

```
            Ingress (NGINX)
                  │
           vproapp (Tomcat + Java WAR)
          ┌───────┼────────────┐
     MySQL (PVC)  Memcached   RabbitMQ
```

| Service | Image / build | Role |
|---|---|---|
| `vproweb` | NGINX (`Docker-files/web`) | Reverse proxy |
| `vproapp` | Tomcat 10 + JDK 21, multi-stage Maven build (`Docker-files/app`) | Java web app |
| `vprodb` | MySQL with seed backup (`Docker-files/db`) | Database |
| `vprocache01` | Memcached | DB query cache |
| `vpromq01` | RabbitMQ | Message broker |

## What's in this repo

- **`Docker-files/`**: Dockerfiles for the app, DB and web tiers, including a **multi-stage build** that compiles with Maven and ships only the WAR on Tomcat.
- **`docker-compose.yml`**: runs the whole 5-service stack locally with named volumes.
- **`kubedefs/`**: Kubernetes manifests: Deployments, Services, PersistentVolumeClaim for MySQL, Secret for credentials, and an NGINX Ingress.

## Run locally

```bash
docker compose up -d --build     # app on http://localhost
```

## Deploy to Kubernetes

```bash
kubectl apply -f kubedefs/
kubectl get pods,svc,ingress
```

> The Secret contains demo credentials only. Use a secret manager for real deployments.
