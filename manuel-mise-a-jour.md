# 10.3 Manuel de mise à jour

## Objectif

Ce manuel décrit la procédure à suivre pour faire évoluer GetEvent sans casser les fonctionnalités existantes.

## Cycle recommandé

1. Créer une branche Git.
2. Développer la correction ou la fonctionnalité.
3. Exécuter les tests.
4. Vérifier le build.
5. Fusionner vers `develop`.
6. Valider la CI.
7. Fusionner vers `main`.
8. Laisser la CD déployer la version stable.

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
- les secrets GitHub Actions si nécessaire ;
- la configuration du VPS ;
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

## Rollback

En cas de problème :

1. Identifier le commit fautif.
2. Revenir à l'image Docker précédente ou au commit stable.
3. Restaurer les variables d'environnement si elles ont changé.
4. Vérifier `/health`.
5. Ajouter l'incident au plan de correction des bogues.
