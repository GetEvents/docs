# 13.2 Manuel d'utilisation

## Présentation

GetEvent est une application web permettant de créer, publier, rechercher et gérer des événements. Elle gère aussi les inscriptions, les paiements, les billets QR code, la messagerie et les notifications.

## Rôles

| Rôle | Capacités principales |
| --- | --- |
| Participant | Consulter les événements, s'inscrire, payer, consulter ses billets |
| Organisateur | Créer des événements, suivre les participants, scanner les billets, demander un retrait |
| Administrateur | Administrer les utilisateurs, les événements, certains messages, remboursements et retraits |

## Créer un compte

1. Ouvrir la page d'inscription.
2. Renseigner les informations personnelles.
3. Choisir le rôle Participant ou Organisateur. Pour un compte organisateur, le pays, le numéro de téléphone et la majorité sont requis.
4. Valider le formulaire.
5. Ouvrir l'e-mail de vérification et suivre le lien de confirmation.
6. Après validation de l'adresse, un e-mail de bienvenue confirme la fin du parcours de vérification.

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

1. Se connecter à l'application.
2. Ouvrir la liste des événements.
3. Consulter le détail d'un événement. La fiche détaillée nécessite une session authentifiée.
4. Cliquer sur le bouton de participation.
5. Si l'événement est payant, effectuer le paiement.
6. Une fois la participation validée, le billet apparaît dans l'espace participation.

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

## Administration

Le rôle `ADMIN` accède au tableau de bord avec des droits étendus. Il peut notamment consulter les utilisateurs, gérer les événements, traiter les remboursements et retraits et supprimer les messages d'un événement lorsque cela est nécessaire.

## Assistance utilisateur

| Situation | Action à effectuer |
| --- | --- |
| Mot de passe oublié | Utiliser la page de récupération du mot de passe |
| Paiement interrompu | Revenir sur la fiche de l'événement et relancer le parcours de paiement |
| QR code non reconnu | Vérifier que le billet présenté correspond à l'événement sélectionné |
| Caméra inaccessible | Autoriser l'accès à la caméra dans les paramètres du navigateur |
