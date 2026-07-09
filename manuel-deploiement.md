# 10.1 Manuel de déploiement

## Objectif

Ce manuel explique comment déployer GetEvent en environnement de production.

## Environnements

| Environnement | Usage |
| --- | --- |
| Développement | Travail local du développeur |
| Test | Vérification avant mise en production |
| Production | Version stable accessible aux utilisateurs |

## Prérequis serveur

- VPS Linux.
- Docker et Docker Compose installés.
- Accès SSH.
- Accès à GitHub Container Registry.
- Variables d'environnement configurées.
- Base PostgreSQL accessible.

## Déploiement backend

Le backend est déployé via GitHub Actions :

1. Le code est fusionné sur la branche `main`.
2. GitHub Actions construit l'image Docker.
3. L'image est publiée sur GitHub Container Registry.
4. Le workflow se connecte au VPS.
5. Le fichier `docker-compose.yml` est copié.
6. Le VPS télécharge la nouvelle image.
7. Le conteneur est redémarré.

Commande équivalente sur le serveur :

```bash
cd /opt/getevent-back
docker compose pull
docker compose up -d
```

## Déploiement frontend

En production sur VPS, le fichier `docker-compose.yml` attend une image :

```txt
ghcr.io/getevents/getevent-front:latest
```

La publication automatique de cette image est gérée par le workflow :

```txt
GetEvent_front/.github/workflows/deploy.yml
```

Le workflow construit l'image, pousse les tags `latest` et `sha`, copie `docker-compose.yml` sur le VPS, puis redémarre le conteneur frontend.

Variables GitHub Actions à configurer pour le build frontend :

- `NEXT_PUBLIC_API_URL`
- `NEXT_PUBLIC_API_ENDPOINT`
- `NEXT_PUBLIC_SOCKET_URL`
- `NEXT_PUBLIC_GOOGLE_MAPS_API_KEY`
- `API_INTERNAL_URL`

Les variables `NEXT_PUBLIC_*` sont publiques côté navigateur. Elles ne doivent pas contenir de secrets privés. La variable `API_INTERNAL_URL` est utilisée par Next.js côté serveur, notamment dans le proxy d'authentification, pour joindre l'API depuis le conteneur.

## Base de données

En production, appliquer les migrations avec :

```bash
npx prisma migrate deploy
```

La variable `DATABASE_URL` doit pointer vers la base PostgreSQL de production.

## Vérification après déploiement

| Élément à vérifier | Méthode de contrôle | Résultat attendu | Statut de validation |
| --- | --- | --- | --- |
| Disponibilité de l'API | Requête HTTP `GET /health` sur l'URL du VPS. | Réponse HTTP 200 OK avec payload de statut. | Conforme |
| Intégrité des conteneurs | Lecture des journaux via `docker compose logs -f`. | Démarrage complet des services, absence d'erreur critique ou de crash loop. | Conforme |
| Authentification | Tentative de connexion via le proxy d'authentification. | Vérification d'identité réussie, génération et stockage des tokens JWT et Refresh Token. | Conforme |
| Persistance des données | Chargement de la page de recherche/liste des événements. | Requête Prisma exécutée avec succès, données hydratées et affichées côté client. | Conforme |
| Passerelle de paiement | Simulation d'une inscription payante via l'environnement Sandbox. | Webhook ou callback Stripe/FedaPay reçu et validé par l'API, passage du billet à l'état valide. | Conforme |
| Temps réel et supervision | Connexion au serveur de messagerie Socket.IO. | Handshake Socket.IO valide, transmission instantanée des messages et des notifications. | Conforme |
| Qualité et observabilité | Inspection du dashboard centralisé Sentry de production. | Flux de monitoring actif et absence d'exceptions ou d'anomalies non capturées suite au déploiement. | Conforme |

## Rollback

En cas d'incident :

1. Identifier l'image Docker précédente.
2. Modifier le tag dans `docker-compose.yml`.
3. Exécuter :

```bash
docker compose pull
docker compose up -d
```

4. Vérifier `/health`.
5. Documenter l'incident dans le plan de correction des bogues.

## Sécurité des secrets

Les fichiers `.env` ne doivent pas être versionnés ni livrés. Les secrets doivent être stockés :

- dans les GitHub Secrets ;
- dans les variables serveur ;
- dans un gestionnaire de secrets si disponible.
