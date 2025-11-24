# DataPress – POC conteneurs & Kubernetes

Ce dépôt propose un proof of concept complet pour moderniser la plateforme interne de DataPress. Il inclut une API FastAPI minimaliste, un front statique servi par NGINX, l'orchestration locale via Docker Compose, des manifestes Kubernetes pour l'environnement de recette, ainsi qu'un début de chaîne CI/CD.

## Organisation du dépôt

- `app/api/` : code et Dockerfile de l'API FastAPI.
- `app/front/` : front statique, template HTML et image NGINX personnalisée.
- `k8s/` : manifests Kubernetes (namespace, ConfigMap, Secret, Deployments, Services).
- `.github/workflows/` : pipeline CI de build.
- `docs/` : documentation technique et présentation client.
- `docker-compose.yml` : exécution locale.

## Prérequis

- Docker Desktop ou équivalent (compose v2 inclus).
- Accès à un cluster Kubernetes (kind, k3d, minikube ou cluster distant) et `kubectl`.
- Optionnel : GitHub Actions (ou autre) pour exécuter le workflow CI.

## Lancer le mode développement (Docker Compose)

```bash
docker compose up --build
```

Services exposés :

- API : http://localhost:8000
- Front : http://localhost:8080

Le front interroge l'API via le réseau Compose et affiche la réponse JSON en temps réel.

## Déploiement Kubernetes (recette)

1. Créez les images et poussez-les dans un registre accessible au cluster (ou utilisez `kubectl apply -k .` après avoir paramétré `imagePullPolicy: Never` sur un cluster local qui partage les images Docker Desktop).
2. Appliquez les manifests :

```bash
kubectl apply -f k8s/namespace.yaml
kubectl apply -n datapress-recette -f k8s/configmap.yaml -f k8s/secret.yaml
kubectl apply -n datapress-recette -f k8s/api-deployment.yaml -f k8s/api-service.yaml
kubectl apply -n datapress-recette -f k8s/front-deployment.yaml -f k8s/front-service.yaml
```

3. Vérifiez l'état :

```bash
kubectl get all -n datapress-recette
kubectl get events -n datapress-recette --sort-by=.lastTimestamp
```

4. Accédez au front via le NodePort (`http://<node-ip>:30080`). L'interface consomme l'API via le Service interne `datapress-api`.

## CI/CD minimal

Le workflow `build-api.yml` (GitHub Actions) :

- s'exécute sur chaque `push` sur `main`,
- installe Python,
- installe les dépendances de l'API,
- construit l'image Docker pour valider le Dockerfile.

Ajoutez des secrets de registre si vous souhaitez pousser automatiquement l'image.

## Documentation

Les documents détaillés se trouvent dans `docs/` :

- `documentation_technique.md` : contexte, architecture, décisions et guide d'exploitation.
- `presentation_client.md` : support synthétique orienté DSI.

## Tests et vérifications

- `docker compose logs -f api front` pour suivre les services locaux.
- `uvicorn` embarqué expose `/health` utilisé par les probes Kubernetes.
- Sur Kubernetes : `kubectl logs -n datapress-recette deploy/datapress-api` et `kubectl port-forward service/datapress-api 8081:80` pour diagnostiquer.

## Étapes suivantes possibles

- Ajouter un Ingress avec TLS.
- Intégrer une base de données managée et un stockage persistant.
- Étendre la CI avec des tests unitaires, scans de sécurité et publication d'images signées.

