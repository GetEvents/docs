# Plan de correction des bogues

## Objectif

Ce document decrit la qualification, la correction et la verification des anomalies detectees pendant la recette GetEvent.

## Cycle de traitement

1. Detection du bug.
2. Reproduction.
3. Qualification de la gravite.
4. Analyse technique.
5. Correction dans le code ou la configuration.
6. Test de non-regression.
7. Cloture.

## Niveaux de gravite

| Gravite | Definition |
|---|---|
| Bloquant | L'application ou un parcours critique est inutilisable |
| Majeur | Fonctionnalite importante degradee |
| Mineur | Probleme contournable |
| Cosmetique | Probleme visuel ou wording |

## Tableau de suivi des anomalies corrigees

| ID | Fonctionnalite | Description | Reproduction | Gravite | Statut | Correction technique appliquee | Test non-regression |
|---|---|---|---|---|---|---|---|
| BUG-001 | Docker frontend / API backend | Le conteneur frontend ne parvenait pas a joindre l'API backend en utilisant `localhost` depuis Docker | Lancer `docker compose up -d --build`, puis ouvrir le front et appeler une route API | Bloquant | Corrige | Separation des URL publiques navigateur et internes serveur : ajout de `API_INTERNAL_URL`, valeurs Docker Compose, et utilisation dans `src/proxy.ts` | Verification des variables dans les logs Docker et acces API depuis le front conteneurise |
| BUG-002 | CI frontend / ESLint | Le workflow GitHub Actions echouait sur `react-hooks/set-state-in-effect` dans le formulaire de creation/edition evenement | Lancer `npm run lint` dans le front | Majeur | Corrige | Ajout d'une exception ciblee et documentee sur l'hydratation du formulaire d'edition dans `src/components/form/CreatEvent/index.jsx` | `npm run lint` : 0 erreur bloquante |
| BUG-003 | Tests frontend / Vitest | Les tests browser echouaient avec des erreurs de mocks, `IntersectionObserver` absent et configuration coverage mal placee | Lancer `npm test` puis `npm run build` | Majeur | Corrige | Ajout de `src/tests/setup.ts`, mock `IntersectionObserver`, helper `render`, configuration `optimizeDeps`, correction du typage `IS_REACT_ACT_ENVIRONMENT`, deplacement de `coverage` au niveau global Vitest | `npm test` : 7 fichiers, 20 tests passes ; `npm run build` OK |
| BUG-005 | Storybook peuple avec stories | Le build Storybook etait auparavant peu utile car il ne documentait aucun composant reutilisable | Rechercher les stories avec `rg "\\.stories\\." GetEvent_front/src` puis lancer `npm run build-storybook` | Majeur | Corrige | Ajout de stories pour Button, Input, NotificationModal et TicketQRCode ; import du CSS global dans `.storybook/preview.js` ; nettoyage du pattern Storybook | `npm run build-storybook` OK ; stories visibles dans `GetEvent_front/src/**/*.stories.*` |
| BUG-006 | Secrets de configuration | Des fichiers `.env` / `.env.local` contenaient des secrets locaux dans le workspace | Inspection du dossier livre | Bloquant securite | Corrige dans le rendu | Ajout de `.env.example`, documentation `docs/gestion-secrets.md`, consigne de retrait des `.env` du livrable et rotation des cles exposees | Presence des exemples `.env.example` et verification `.gitignore` |
| BUG-007 | Notifications UI | La modale devait rester accessible et permettre la fermeture et le marquage comme lu | Ouvrir la modale de notifications | Mineur | Corrige | Tests Vitest sur `NotificationModal` : role `dialog`, etat vide, fermeture, marquage global comme lu | Tests `NotificationModal.test.jsx` valides |

## Anomalies surveillees sans blocage de livraison

| ID | Sujet | Impact | Decision |
|---|---|---|---|
| OBS-001 | Warnings ESLint `console` | Qualite de code, aucun blocage fonctionnel | Warnings conserves et suivis, 0 erreur bloquante |
| OBS-002 | Warnings Next `<img>` | Optimisation performance/LCP | Images principales utilisables ; optimisation progressive via `next/image` sur les composants restants |

## Priorisation

Les anomalies bloquantes et majeures liees a l'authentification, au paiement, aux billets, aux donnees utilisateur, a la CI/CD et a la securite sont traitees en priorite. Les corrections listees ci-dessus sont integrees dans la version stabilisee du MVP.
