# Configuration du déploiement

Le workflow `.github/workflows/ci.yml` construit l'image depuis le `Dockerfile`, la publie dans GHCR sous `ghcr.io/<owner>/<repository>:<sha>`, puis redémarre le conteneur `citest-app` sur le serveur.

Dans **Settings → Secrets and variables → Actions**, ajoutez ces secrets au dépôt (ou à l'environnement `production`) :

| Secret | Valeur |
| --- | --- |
| `PRODUCTION_HOST` | Nom DNS ou adresse IP du serveur |
| `PRODUCTION_USER` | Utilisateur SSH autorisé à exécuter Docker |
| `PRODUCTION_SSH_KEY` | Clé privée SSH de cet utilisateur |

Le serveur doit accepter la connexion SSH et avoir Docker installé. L'utilisateur doit pouvoir lancer Docker et accéder à l'image GHCR. Si le paquet GHCR est privé, accordez au `GITHUB_TOKEN` du dépôt l'accès en lecture au paquet dans les paramètres du paquet. Le port 80 du serveur doit être disponible.

Le workflow utilise également `GITHUB_TOKEN` pour publier l'image. Aucune clé de registre supplémentaire n'est nécessaire.
