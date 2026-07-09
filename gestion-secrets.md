# Politique de gestion des secrets

## Objectif

Ce document definit les regles de gestion des secrets de GetEvent.

## Regles principales

- Ne jamais livrer `.env` ou `.env.local`.
- Fournir uniquement des fichiers `.env.example`.
- Ne jamais mettre de cle privee dans une variable `NEXT_PUBLIC`.
- Revoquer toute cle exposee.
- Separer les secrets de developpement, test et production.
- Stocker les secrets de production dans GitHub Secrets ou sur le serveur.

## Variables backend

```env
DATABASE_URL=remplacer_par_l_url_postgresql
SECRET_KEY=remplacer_par_le_secret_du_token_acces
REFRESH_TOKEN_SECRET=remplacer_par_le_secret_du_token_rafraichissement
JWT_SECRET=remplacer_par_le_secret_jwt
STRIPE_SECRET_KEY=remplacer_par_la_cle_secrete_stripe
FEDAPAY_WEBHOOK_SECRET=remplacer_par_le_secret_webhook_fedapay
CLOUDINARY_API_SECRET=remplacer_par_le_secret_api_cloudinary
GOOGLE_CLIENT_SECRET=remplacer_par_le_secret_client_google
BREVO_API_KEY=remplacer_par_la_cle_api_brevo
SENTRY_DSN=remplacer_par_le_dsn_sentry
```

## Variables frontend

```env
NEXT_PUBLIC_API_URL=http://localhost:8080
NEXT_PUBLIC_SOCKET_URL=http://localhost:8080
NEXT_PUBLIC_GOOGLE_MAPS_API_KEY=remplacer_par_la_cle_publique_google_maps
```

## Procedure en cas d'exposition

1. Supprimer le secret du fichier livre.
2. Revoquer la cle chez le fournisseur.
3. Generer une nouvelle cle.
4. Mettre a jour les environnements.
5. Verifier que l'application fonctionne.
6. Documenter l'incident.

