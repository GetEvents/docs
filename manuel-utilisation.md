# 10.2 Manuel d'utilisation

## Présentation

GetEvent est une application web permettant de créer, publier, rechercher et gérer des événements. Elle gère aussi les inscriptions, les paiements, les billets QR code, la messagerie et les notifications.

## Rôles

| Rôle | Capacités principales |
| --- | --- |
| Participant | Consulter les événements, s'inscrire, payer, consulter ses billets |
| Organisateur | Créer des événements, suivre les participants, scanner les billets, demander un retrait |
| Administrateur technique | Superviser le déploiement, les variables d'environnement et les journaux serveur |

## Créer un compte

1. Ouvrir la page d'inscription.
2. Renseigner les informations personnelles.
3. Choisir un rôle.
4. Valider le formulaire.
5. Confirmer le compte si une vérification par e-mail est effectuée.

## Se connecter

1. Ouvrir la page de connexion.
2. Saisir l'e-mail et le mot de passe.
3. Valider.
4. L'utilisateur est redirigé vers l'application.

Une connexion Google OAuth est également disponible.

## Créer un événement

1. Se connecter avec un compte organisateur.
2. Accéder à la page de création d'événement.
3. Renseigner le titre, la catégorie, la description, les dates, l'adresse, la capacité et le prix.
4. Ajouter une image.
5. Valider la création.

## Participer à un événement

1. Ouvrir la liste des événements.
2. Consulter le détail d'un événement.
3. Cliquer sur le bouton de participation.
4. Si l'événement est payant, effectuer le paiement.
5. Une fois la participation validée, le billet apparaît dans l'espace participation.

## Consulter un billet

1. Accéder à la page des participations.
2. Sélectionner le billet.
3. Afficher le QR code.
4. Présenter le QR code à l'organisateur lors de l'événement.

## Scanner un billet

1. L'organisateur ouvre le lecteur de codes QR.
2. Il autorise l'accès à la caméra.
3. Il scanne le QR code du participant.
4. Le système indique si le billet est valide, invalide ou déjà utilisé.

## Messagerie

La messagerie permet d'échanger autour d'un événement. Les messages sont transmis en temps réel avec Socket.IO.

## Notifications

Les notifications informent l'utilisateur des actions importantes : inscription, rappel, paiement, remboursement, message ou changement d'état.

## Problèmes fréquents

| Problème | Solution | Priorité |
| --- | --- | --- |
| Mot de passe oublié | Utiliser la page de récupération | Haute |
| Paiement interrompu | Revenir sur l'événement et recommencer | Haute |
| QR code non reconnu | Vérifier que le billet correspond au bon événement | Moyenne |
| Caméra inaccessible | Autoriser la caméra dans le navigateur | Moyenne |
