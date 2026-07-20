# Politique de gestion des secrets

## Objectif

Ce document définit les règles appliquées à la gestion des secrets et des variables de configuration de GetEvent.

## Principes appliqués

- Les fichiers `.env` et `.env.local` contenant les valeurs réelles sont exclus du versionnement.
- Seuls les fichiers `.env.example`, composés de valeurs fictives, accompagnent le code source.
- Les secrets de développement et de production sont séparés.
- Les variables `NEXT_PUBLIC_*` sont considérées comme publiques et ne contiennent aucune clé privée.
- Les secrets de production sont configurés dans Render, Netlify ou GitHub Secrets selon leur usage.
- Toute valeur accidentellement exposée est révoquée puis remplacée dans les environnements concernés.

## Configuration sensible du backend

Le fichier `GetEvent_back/.env.example` documente l'ensemble des variables attendues. Les principales valeurs sensibles sont :

```env
DATABASE_URL=remplacer_par_l_url_postgresql
SECRET_KEY=remplacer_par_le_secret_du_token_acces
REFRESH_TOKEN_SECRET=remplacer_par_le_secret_du_token_de_rafraichissement
JWT_SECRET=remplacer_par_le_secret_jwt
QR_SECRET=remplacer_par_le_secret_de_signature_des_qr_codes
STRIPE_SECRET_KEY=remplacer_par_la_cle_secrete_stripe
Feda_Pay_Private_Key=remplacer_par_la_cle_privee_fedapay
FEDAPAY_WEBHOOK_SECRET=remplacer_par_le_secret_webhook_fedapay
CLOUDINARY_API_SECRET=remplacer_par_le_secret_api_cloudinary
GOOGLE_CLIENT_SECRET=remplacer_par_le_secret_client_google
BREVO_API_KEY=remplacer_par_la_cle_api_brevo
SENTRY_DSN=remplacer_par_le_dsn_sentry
EMAIL_ADRESS=adresse_smtp_facultative
EMAIL_PASSWORD=mot_de_passe_smtp_facultatif
```

`EVENT_REMINDER_CRON` et `EVENT_REMINDER_TIMEZONE` pilotent la planification des rappels. Ce sont des variables de configuration, et non des secrets.

## Configuration du frontend

```env
NEXT_PUBLIC_API_URL=http://localhost:3001
NEXT_PUBLIC_API_ENDPOINT=http://localhost:3001
NEXT_PUBLIC_SOCKET_URL=http://localhost:3001
NEXT_PUBLIC_GOOGLE_MAPS_API_KEY=remplacer_par_la_cle_publique_google_maps
NEXT_PUBLIC_GOOGLE_MAP_ID=remplacer_par_le_map_id_google
API_INTERNAL_URL=http://localhost:3001
```

En développement local, `3000` est le port du frontend et `3001` celui du backend. La clé Google Maps et le Map ID utilisés par le navigateur sont publics par conception. La clé est destinée à être restreinte dans Google Cloud aux domaines et aux API nécessaires. `API_INTERNAL_URL` est une URL utilisée côté serveur par Next.js ; ce n'est pas une clé secrète.

## Répartition par plateforme

| Emplacement | Données configurées |
| --- | --- |
| Développement local | Valeurs locales dans `.env` ou `.env.local`, non versionnées |
| Render | Variables et secrets nécessaires au backend de production |
| Netlify | Variables nécessaires au build et à l'exécution du frontend |
| GitHub Secrets | Hooks de déploiement Render et Netlify |
| GitHub Variables | Valeurs publiques utilisées pour valider le build frontend |

## Procédure en cas d'exposition

1. Identifier la valeur et les environnements concernés.
2. Révoquer la valeur auprès du fournisseur associé.
3. Générer une nouvelle valeur avec une source aléatoire sûre.
4. Mettre à jour Render, Netlify, GitHub ou l'environnement local concerné.
5. Vérifier les parcours qui dépendent de cette configuration.
6. Consigner l'événement et les actions correctives dans le suivi de maintenance.
