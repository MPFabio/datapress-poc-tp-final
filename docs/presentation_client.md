# Présentation client – POC DataPress

## Slide 1 – Contexte
- Plateforme actuelle monolithique sur un serveur unique.
- Incidents récents = indisponibilité totale.
- Objectif : préparer une migration progressive via un POC conteneurisé.

## Slide 2 – Objectifs du POC
- Séparer front et API et fiabiliser la recette.
- Introduire Docker, Kubernetes et un début de CI/CD.
- Documenter et présenter une architecture claire au DSI.

## Slide 3 – Architecture globale
- Mode dev : Docker Compose (services `api`, `front`, réseau dédié).
- Mode recette : Kubernetes (`datapress-recette`, Deployments, Services, ConfigMap, Secret).
- Communication interne via DNS Kubernetes, exposition front en NodePort.

## Slide 4 – Mode développement
- `docker compose up --build`.
- Front statique (NGINX) qui consomme l'API.
- Expérience développeur reproductible sans dépendances locales.

## Slide 5 – Mode recette Kubernetes
- API en 2 réplicas, probes `/health`, ressources limitées.
- Front en 1 réplique, Service NodePort 30080.
- ConfigMap pour les paramètres, Secret pour le token.

## Slide 6 – Sécurité et fiabilité
- Images multi-stage, utilisateur non-root.
- Probes readiness/liveness pour réduire les temps morts.
- Variables sensibles isolées via Secret, ressources bornées.

## Slide 7 – CI/CD
- Workflow GitHub Actions : checkout, installation Python, build de l'image API.
- Base pour ajouter tests, scans et push registre.

## Slide 8 – Limites et prochaines étapes
- Ajouter Ingress + TLS, base de données, observabilité.
- Étendre la CI/CD (tests, scans, promotion automatisée).
- Mettre en place des NetworkPolicies et un HPA.

## Slide 9 – Conclusion
- POC rejouable, documenté, démontrant les bénéfices attendus.
- Base solide pour décider de la généralisation vers la production.

