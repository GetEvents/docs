# 13.1 Manuel de déploiement

## Objectif

Ce manuel décrit le déploiement automatisé actuel de GetEvent : le backend est publié sous forme d'image Docker puis déployé sur Render, tandis que le frontend est déployé sur Netlify.

## Environnements

| Environnement | Usage |
| --- | --- |
| Développement | Travail local du développeur |
| Intégration | Vérification automatique sur la branche `develop` |
| Production | Déploiement depuis la branche `main` |

## Prérequis

- Dépôts GitHub configurés avec GitHub Actions.
- Service backend Render relié à l'image Docker GetEvent.
- Site frontend Netlify configuré pour construire l'application Next.js.
- Base PostgreSQL de production accessible depuis Render.
- Variables et secrets configurés dans GitHub, Render et Netlify.

## Déploiement backend sur Render

Le workflow `GetEvent_back/.github/workflows/deploy.yml` est lancé lors d'un push sur `main` ou manuellement avec `workflow_dispatch`.

Il effectue les opérations suivantes :

1. Construction de l'image à partir du `Dockerfile` backend.
2. Publication dans GitHub Container Registry sous `ghcr.io/getevents/getevent-back`.
3. Publication des tags `latest` et `sha`.
4. Appel du hook Render afin de déclencher le déploiement.

Secret GitHub requis :

```text
RENDER_DEPLOY_HOOK_URL
```

Les variables applicatives du backend, notamment `DATABASE_URL`, les secrets JWT, les clés de paiement et `SENTRY_DSN`, doivent être définies dans l'environnement Render. Le fichier `.env` local ne doit jamais être déployé.

## Déploiement frontend sur Netlify

Le workflow `GetEvent_front/.github/workflows/deploy.yml` est lancé lors d'un push sur `main` ou manuellement.

Avant le déploiement, il :

1. installe les dépendances avec `npm ci` ;
2. exécute `npm run lint` ;
3. vérifie le build avec `npm run build` ;
4. appelle le hook de production Netlify.

Secret GitHub requis :

```text
NETLIFY_BUILD_HOOK_URL
```

Variables GitHub Actions utilisées pour la validation du build :

- `NEXT_PUBLIC_API_URL`
- `NEXT_PUBLIC_API_ENDPOINT`
- `NEXT_PUBLIC_SOCKET_URL`
- `NEXT_PUBLIC_GOOGLE_MAPS_API_KEY`
- `NEXT_PUBLIC_GOOGLE_MAP_ID`
- `API_INTERNAL_URL`

Ces variables doivent également être cohérentes avec celles configurées dans Netlify. Les variables `NEXT_PUBLIC_*` sont intégrées au code envoyé au navigateur et ne doivent contenir aucun secret privé.

## Base de données

La variable `DATABASE_URL` doit pointer vers PostgreSQL en production. Lorsqu'une migration Prisma est ajoutée, elle doit être appliquée dans l'environnement de production avec :

```bash
npx prisma migrate deploy
```

Cette étape doit être exécutée depuis un environnement autorisé à joindre la base, avant ou pendant la mise en production de la version qui dépend du nouveau schéma.

## Vérification après déploiement

| Élément | Contrôle | Résultat attendu |
| --- | --- | --- |
| Workflow backend | GitHub Actions puis événements Render | Image publiée et déploiement terminé |
| API | `GET /health` sur l'URL Render | Réponse HTTP 200 avec un statut sain |
| Workflow frontend | GitHub Actions puis déploiements Netlify | Build et déploiement terminés |
| Interface | Ouvrir l'URL Netlify | Page d'accueil chargée sans erreur |
| Authentification | Inscription puis connexion | Session créée correctement |
| Événements | Consultation et création autorisée | Données enregistrées et affichées |
| Paiement | Transaction de test en environnement sandbox | Callback ou webhook traité |
| Temps réel | Messagerie et notifications | Connexion Socket.IO fonctionnelle |
| Supervision | Tableau de bord Sentry | Événements reçus avec le bon environnement |

## Retour à une version stable

### Backend

Redéployer sur Render une image ou une révision antérieure connue comme stable. Si une migration a modifié le schéma, préparer une migration corrective : ne pas utiliser `migrate dev` en production.

### Frontend

Utiliser la fonction de restauration d'un déploiement antérieur dans Netlify ou rétablir la dernière version validée, puis relancer le workflow.

Après la restauration, vérifier `/health`, l'authentification, les événements et les parcours de paiement, puis consigner l'analyse et les actions réalisées dans le suivi de maintenance.

## Sécurité des secrets

- Ne jamais versionner ni livrer `.env` ou `.env.local`.
- Utiliser les GitHub Secrets uniquement pour les hooks de déploiement et les autres valeurs réellement secrètes.
- Configurer les secrets applicatifs dans Render et Netlify selon leur lieu d'utilisation.
- Ne jamais placer une clé privée dans une variable `NEXT_PUBLIC_*`.
- Révoquer immédiatement toute valeur exposée et la remplacer dans tous les environnements.
