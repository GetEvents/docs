# 13.3 Manuel de mise à jour

## Objectif

Ce manuel décrit la procédure utilisée pour faire évoluer GetEvent tout en préservant la stabilité des fonctionnalités existantes.

## Cycle de mise à jour

1. Créer une branche Git.
2. Développer la correction ou la fonctionnalité.
3. Exécuter les tests.
4. Vérifier le build.
5. Fusionner vers `develop` et valider la CI.
6. Fusionner vers `main`.
7. Vérifier la publication Docker et le déploiement Render du backend.
8. Vérifier le build et le déploiement Netlify du frontend.

## Mise à jour backend

```bash
cd GetEvent_back
npm ci
npx prisma generate
npm test
docker build -t getevent-back:check .
```

Si le schéma Prisma change :

```bash
npx prisma migrate dev
```

En production :

```bash
npx prisma migrate deploy
```

## Mise à jour frontend

```bash
cd GetEvent_front
npm ci
npm run lint
npm test
npm run build
npm run build-storybook
```

## Mise à jour des dépendances

Avant de mettre à jour une dépendance :

1. Lire les changements majeurs.
2. Mettre à jour `package.json` et `package-lock.json`.
3. Exécuter les tests.
4. Vérifier les parcours critiques.

## Mise à jour des variables d'environnement

Toute nouvelle variable doit être ajoutée dans :

- le `.env` local du développeur ;
- les secrets ou variables GitHub Actions si nécessaire ;
- la configuration Render pour le backend ;
- la configuration Netlify pour le frontend ;
- le fichier `.env.example` sans valeur secrète.

## Vérification post-mise à jour

| Vérification | Attendu |
| --- | --- |
| Backend | Démarrage sans erreur |
| Frontend | Build Next.js valide |
| Tests backend | OK |
| Authentification | Connexion possible |
| Événements | Création et consultation OK |
| Paiement | Paiement sandbox OK |
| QR code | Génération et scan OK |

## Procédure de retour à une version stable

En cas d'anomalie après une mise à jour :

1. Identifier la dernière version validée et la modification à isoler.
2. Restaurer une image/révision stable sur Render et un déploiement stable sur Netlify.
3. Restaurer les variables d'environnement si elles ont changé.
4. Vérifier `/health`.
5. Consigner l'analyse et les actions correctives dans le suivi de maintenance.
