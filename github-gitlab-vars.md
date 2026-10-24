# CI/CD Variables Reference
## GitHub Actions & GitLab CI/CD

**Predefined variables · Contexts · Quick reference · 2026**

---

## 1. How to use variables

| Platform | Syntax in YAML | Syntax in shell |
|---|---|---|
| GitHub Actions | `${{ github.ref }}` | `$GITHUB_SHA` (env runner) ou `"$VAR"` après mappage via `env:` |
| GitLab CI/CD | `$CI_COMMIT_SHA` | `$CI_COMMIT_SHA` |

**Règles :**

- GitHub : les contextes (`github`, `env`, `vars`, `secrets`…) ne sont résolus qu'à l'évaluation YAML — pour les utiliser dans un script shell, mappez-les dans `env:`.
- GitLab : les variables pré-définies existent déjà comme variables d'environnement dans les shells du job.
- Ne jamais `echo` un secret ; utilisez le masquage (GitLab : *masked*) ou les secrets chiffrés (GitHub).

---

## 2. GitHub Actions — Variables et contextes

### 2.1 Contexte `github` (identité du workflow)

| Variable | Description |
|---|---|
| `github.actor` | Nom d'utilisateur de l'acteur ayant déclenché le workflow |
| `github.actor_id` | ID numérique de l'acteur |
| `github.api_url` | URL de l'API REST (`https://api.github.com`) |
| `github.base_ref` | Branche cible du pull request (PR uniquement) |
| `github.event_name` | Nom de l'évenement déclencheur (`push`, `pull_request`…) |
| `github.event_path` | Chemin vers le fichier JSON du payload de l'événement |
| `github.graphql_url` | URL de l'API GraphQL |
| `github.head_ref` | Branche source du PR (PR uniquement) |
| `github.job` | ID du job actuel |
| `github.ref` | Référence complète (`refs/heads/main`, `refs/tags/v1.0.0`) |
| `github.ref_name` | Nom de la branche ou du tag (`main`, `v1.0.0`) |
| `github.ref_protected` | `true` si la référence est protégée |
| `github.ref_type` | `branch` ou `tag` |
| `github.repository` | Dépôt au format `owner/repo` |
| `github.repository_id` | ID numérique du dépôt |
| `github.repository_owner` | Propriétaire du dépôt |
| `github.repository_owner_id` | ID du propriétaire |
| `github.retention_days` | Jours de rétention des logs/artefacts |
| `github.run_id` | ID unique du workflow run |
| `github.run_number` | Numéro séquentiel du run dans le workflow |
| `github.run_attempt` | Numéro de la tentative actuelle du run |
| `github.server_url` | URL du serveur (`https://github.com`) |
| `github.sha` | SHA du commit |
| `github.token` | Token `GITHUB_TOKEN` automatique (équivalent de `secrets.GITHUB_TOKEN`) |
| `github.triggering_actor` | Acteur qui a déclenché la première tentative |
| `github.workflow` | Nom du workflow |
| `github.workflow_ref` | Référence complète du workflow |
| `github.workflow_sha` | SHA du commit du workflow |
| `github.workspace` | Chemin absolu du répertoire de travail |

### 2.2 Payload `github.event` (selon l'événement)

| Événement | Chemins utiles |
|---|---|
| `push` | `github.event.before`, `github.event.after`, `github.event.ref`, `github.event.head_commit.id`, `github.event.head_commit.message`, `github.event.commits` |
| `pull_request` | `github.event.pull_request.number`, `.title`, `.body`, `.base.ref`, `.head.ref`, `.user.login`, `.merged`, `.labels`, `.assignees` |
| `workflow_dispatch` | `github.event.inputs.<INPUT_NAME>` |
| `schedule` | `github.event.schedule` (cron déclenché) |
| `release` | `github.event.release.tag_name`, `.name`, `.prerelease`, `.draft` |
| `issue` / `issue_comment` | `github.event.issue.number`, `.title`, `.body`, `.user.login` |
| `discussion` | `github.event.discussion.title`, `.body` |

### 2.3 Contextes de configuration

| Contexte | Valeurs | Description |
|---|---|---|
| `env` | `env.MY_VAR` | Variables d'environnement du workflow/job/step |
| `vars` | `vars.MY_VAR` | Variables définies dans Settings → Secrets and variables → Actions |
| `secrets` | `secrets.MY_SECRET`, `secrets.GITHUB_TOKEN` | Secrets du dépôt/environnement/organisation |
| `inputs` | `inputs.<NAME>` | Entrées d'un workflow dispatch ou reusable workflow |
| `matrix` | `matrix.<KEY>` | Valeur courante de la matrice de stratégie |

### 2.4 Contextes d'exécution

| Contexte | Valeurs | Description |
|---|---|---|
| `runner` | `runner.name`, `runner.os`, `runner.arch`, `runner.environment`, `runner.temp`, `runner.tool_cache`, `runner.debug` | Propriétés du runner |
| `job` | `job.status`, `job.container`, `job.services` | État et config du job |
| `steps` | `steps.<id>.outputs.<name>`, `steps.<id>.outcome`, `steps.<id>.conclusion` | Sorties et résultat d'une étape |
| `needs` | `needs.<job_id>.result`, `needs.<job_id>.outputs.<name>` | Résultat d'un job prerequisite |
| `strategy` | `strategy.job-index`, `strategy.job-total`, `strategy.max-parallel` | Position dans la matrice |

### 2.5 Variables d'environnement automatiques du runner

Disponibles directement comme `$VAR` dans les `run:` :

| Variable | Description |
|---|---|
| `CI` | `true` |
| `GITHUB_ACTIONS` | `true` |
| `GITHUB_ACTOR` | Acteur ayant déclenché le run |
| `GITHUB_API_URL` | URL de l'API |
| `GITHUB_BASE_REF` | Branche cible du PR |
| `GITHUB_ENV` | Chemin du fichier d'environnement (append pour propager des vars) |
| `GITHUB_EVENT_NAME` | Événement déclencheur |
| `GITHUB_EVENT_PATH` | Chemin du payload JSON |
| `GITHUB_GRAPHQL_URL` | URL GraphQL |
| `GITHUB_HEAD_REF` | Branche source du PR |
| `GITHUB_JOB` | ID du job |
| `GITHUB_PATH` | Chemin du fichier PATH (append pour ajouter des binaires) |
| `GITHUB_REF` / `GITHUB_REF_NAME` / `GITHUB_REF_TYPE` / `GITHUB_REF_PROTECTED` | Référence git |
| `GITHUB_REPOSITORY` / `GITHUB_REPOSITORY_OWNER` | Dépôt et propriétaire |
| `GITHUB_RETENTION_DAYS` | Rétention des artefacts/logs |
| `GITHUB_RUN_ATTEMPT` / `GITHUB_RUN_ID` / `GITHUB_RUN_NUMBER` | Identifiants du run |
| `GITHUB_SERVER_URL` | URL du serveur |
| `GITHUB_SHA` | SHA du commit |
| `GITHUB_WORKFLOW` / `GITHUB_WORKFLOW_REF` / `GITHUB_WORKFLOW_SHA` | Workflow courant |
| `GITHUB_WORKSPACE` | Répertoire de travail |
| `RUNNER_ARCH` / `RUNNER_DEBUG` / `RUNNER_ENVIRONMENT` / `RUNNER_NAME` / `RUNNER_OS` / `RUNNER_TEMP` / `RUNNER_TOOL_CACHE` | Propriétés du runner |

> `GITHUB_TOKEN` n'est **pas** exposé parmi les variables d'environnement par défaut : mappez-le explicitement (`env: { GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }} }`).

### 2.6 Variables personnalisées (GitHub)

```yaml
env:
  APP_ENV: production          # workflow / job / step
# Définies dans Settings → Secrets and variables → Actions :
#   - Secrets      → ${{ secrets.MY_SECRET }}
#   - Variables    → ${{ vars.MY_VAR }}
#   - Environnement→ ${{ vars.MY_VAR }} (liées à un environment)
```

---

## 3. GitLab CI/CD — Variables prédéfinies

### 3.1 Pipeline et projet

| Variable | Description |
|---|---|
| `CI` | Toujours `true` dans un job |
| `CI_PIPELINE_ID` | ID global du pipeline |
| `CI_PIPELINE_IID` | ID interne du pipeline (par projet) |
| `CI_PIPELINE_SOURCE` | Source : `push`, `merge_request_event`, `schedule`, `api`, `trigger`, `web`, `chat`, `external`, `pipeline`… |
| `CI_PIPELINE_NAME` | Nom du pipeline |
| `CI_PIPELINE_TAG` | Nom du tag (pipeline de tag), sinon vide |
| `CI_PIPELINE_URL` | URL du pipeline dans l'interface |
| `CI_PIPELINE_EDITOR` | Éditeur ayant créé le pipeline (`webide`, `vscode_extension`…) |
| `CI_FREEZE_PERIOD` | `true` si dans une période de gel |
| `CI_FREEZE_WINDOW` | Fenêtre de gel active |
| `CI_PROJECT_ID` | ID du projet |
| `CI_PROJECT_NAME` | Nom court du projet |
| `CI_PROJECT_NAMESPACE` | Namespace (groupe ou utilisateur) |
| `CI_PROJECT_PATH` | Chemin complet `namespace/project` |
| `CI_PROJECT_PATH_SLUG` | Chemin slugifié |
| `CI_PROJECT_ROOT_NAMESPACE` | Racine du namespace |
| `CI_PROJECT_TITLE` | Titre du projet |
| `CI_PROJECT_URL` | URL du projet |
| `CI_PROJECT_DIR` | Chemin absolu du checkout sur le runner |
| `CI_PROJECT_VISIBILITY` | `private`, `internal` ou `public` |
| `CI_PROJECT_REPOSITORY_LANGUAGES` | Langages détectés (JSON) |
| `CI_SHARED_NAMESPACE` | `true` si namespace partagé |
| `CI_DEFAULT_BRANCH` | Branche par défaut du projet |
| `CI_CONFIG_PATH` | Chemin du `.gitlab-ci.yml` |
| `CI_ENVIRONMENTS_URL` | URL de la page des environnements |

### 3.2 Commit et référence

| Variable | Description |
|---|---|
| `CI_COMMIT_SHA` | SHA complet du commit |
| `CI_COMMIT_SHORT_SHA` | SHA tronqué (16 caractères) |
| `CI_COMMIT_BRANCH` | Nom de la branche (pipelines de branche) |
| `CI_COMMIT_REF_NAME` | Branche ou tag déclencheur |
| `CI_COMMIT_REF_PATH` | `refs/heads/...` ou `refs/tags/...` |
| `CI_COMMIT_REF_SLUG` | Référence slugifiée |
| `CI_COMMIT_REF_PROTECTED` | `true` si la référence est protégée |
| `CI_COMMIT_TAG` | Nom du tag (pipelines de tag) |
| `CI_COMMIT_TITLE` | Premier ligne du message de commit |
| `CI_COMMIT_MESSAGE` | Message complet du commit |
| `CI_COMMIT_DESCRIPTION` | Description du commit (après la 1re ligne) |
| `CI_COMMIT_AUTHOR` | Auteur au format `Name <email>` |
| `CI_COMMIT_TIMESTAMP` | Horodatage ISO du commit |
| `CI_COMMIT_BEFORE_SHA` | SHA précédent (`0000…` si premier push ou nouvelle branche) |
| `CI_MERGE_REQUEST_*` | Voir section 3.5 |

### 3.3 Job et exécution

| Variable | Description |
|---|---|
| `CI_JOB_ID` | ID global du job |
| `CI_JOB_NAME` | Nom du job |
| `CI_JOB_NAME_SLUG` | Nom slugifié |
| `CI_JOB_STAGE` | Stage du job |
| `CI_JOB_URL` | URL du job |
| `CI_JOB_TOKEN` | Token JWT éphémère du job (API GitLab, registre…) |
| `CI_JOB_JWT` / `CI_JOB_JWT_V2` | JWT signé (⚠️ `CI_JOB_JWT` déprécié) |
| `CI_JOB_RETRIED` | `true` si le job est une reprise |
| `CI_JOB_STATUS` | Statut dans `after_script` : `created`, `running`, `failed`, `success`, `canceled` |
| `CI_JOB_CLASS` | Classe : `build`, `test`, `deploy`, `other` |
| `CI_NODE_INDEX` / `CI_NODE_TOTAL` | Position dans `parallel:matrix` |
| `CI_UNSTABLE_SOURCE` | Job précédemment instable (`retry`, `schedule`…) |
| `CI_DEBUG_TRACE` | `true` si le débogage complet est activé |
| `CI_DEBUG_JOB` | `true` si le mode debug du job est activé |
| `CI_HAS_JUNIT_REPORTS` | `true` si le job a des rapports JUnit |

### 3.4 Runner

| Variable | Description |
|---|---|
| `CI_RUNNER_ID` | ID du runner |
| `CI_RUNNER_DESCRIPTION` | Description du runner |
| `CI_RUNNER_TAGS` | Tags du runner (liste) |
| `CI_RUNNER_EXECUTABLE_ARCH` | Architecture de l'exécutable du runner |
| `RUNNER_ARCH` | `amd64`, `arm64`, `386`… |
| `RUNNER_TAGS` | Tags du runner (déprécié au profit de `CI_RUNNER_TAGS`) |
| `RUNNER_USER` | Utilisateur exécutant le job |
| `RUNNER_WORK_DIR` | Répertoire de travail du runner |
| `RUNNER_FEATURES` | Fonctionnalités supportées par le runner |

### 3.5 Merge requests (`CI_MERGE_REQUEST_*`)

| Variable | Description |
|---|---|
| `CI_MERGE_REQUEST_EVENT_TYPE` | `merged_result`, `detached`, `merge_train` |
| `CI_MERGE_REQUEST_ID` | ID global de la MR |
| `CI_MERGE_REQUEST_IID` | Numéro de la MR (#42) |
| `CI_MERGE_REQUEST_TITLE` | Titre de la MR |
| `CI_MERGE_REQUEST_DESCRIPTION` | Description de la MR |
| `CI_MERGE_REQUEST_SOURCE_BRANCH` | Branche source |
| `CI_MERGE_REQUEST_SOURCE_BRANCH_NAME` | Nom de la branche source |
| `CI_MERGE_REQUEST_TARGET_BRANCH` | Branche cible |
| `CI_MERGE_REQUEST_TARGET_BRANCH_NAME` | Nom de la branche cible |
| `CI_MERGE_REQUEST_SOURCE_PROJECT_PATH` | Projet source |
| `CI_MERGE_REQUEST_SOURCE_PROJECT_ID` | ID du projet source |
| `CI_MERGE_REQUEST_PROJECT_PATH` | Projet cible |
| `CI_MERGE_REQUEST_PROJECT_ID` | ID du projet cible |
| `CI_MERGE_REQUEST_REF_NAME` | Référence de la MR |
| `CI_MERGE_REQUEST_REF_PATH` | Chemin de référence de la MR |
| `CI_MERGE_REQUEST_ASSIGNEE` / `CI_MERGE_REQUEST_ASSIGNEES` | Assignés |
| `CI_MERGE_REQUEST_AUTHOR` | Auteur |
| `CI_MERGE_REQUEST_LABELS` | Labels (liste) |
| `CI_MERGE_REQUEST_MILESTONE` | Jalon |
| `CI_MERGE_REQUEST_APPROVED` | `true` si approuvée (Premium) |
| `CI_OPEN_MERGE_REQUESTS` | MR ouvertes vers la branche courante |
| `CI_EXTERNAL_PULL_REQUEST_IID` / `_SOURCE_BRANCH` / `_TARGET_BRANCH` / `_SOURCE_PROJET_PATH` / `_STATE` / `_TITLE` / `_URL` | PR GitHub liées (Enterprise) |

### 3.6 Environnement de déploiement

| Variable | Description |
|---|---|
| `CI_ENVIRONMENT_NAME` | Nom de l'environnement |
| `CI_ENVIRONMENT_SLUG` | Nom slugifié |
| `CI_ENVIRONMENT_ACTION` | `start`, `prepare` ou `stop` |
| `CI_ENVIRONMENT_TIER` | `production`, `staging`, `development`, `other` |

### 3.7 Registre et déploiements

| Variable | Description |
|---|---|
| `CI_REGISTRY` | Hôte du registre GitLab |
| `CI_REGISTRY_IMAGE` | Image du projet (`registry.gitlab.com/group/project`) |
| `CI_REGISTRY_USER` | Utilisateur pour le registre |
| `CI_REGISTRY_PASSWORD` | Mot de passe pour le registre |
| `CI_REGISTRY_PASSWORD_FILE` | Chemin du fichier contenant le mot de passe |
| `CI_REGISTRY` (login) | Se connecter : `docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY` |
| `CI_DEPENDENCY_REGISTRY` | Hôte du registre de dépendances |

### 3.8 Serveur et API

| Variable | Description |
|---|---|
| `CI_SERVER_HOST` | Hôte du serveur GitLab |
| `CI_SERVER_PORT` | Port du serveur |
| `CI_SERVER_PROTOCOL` | `http` ou `https` |
| `CI_SERVER_NAME` | Nom du serveur |
| `CI_SERVER_URL` | URL de base de l'instance |
| `CI_API_V4_URL` | URL de base de l'API v4 (`https://gitlab.com/api/v4`) |
| `CI_API_V3_URL` | API v3 (dépréciée) |
| `CI_SERVER_FQDN` / `CI_SERVER_ADDRESS` | Dépréciés → utiliser `CI_SERVER_HOST` / `CI_SERVER_URL` |

### 3.9 Utilisateur déclencheur

| Variable | Description |
|---|---|
| `GITLAB_USER_ID` | ID de l'utilisateur ayant déclenché le pipeline |
| `GITLAB_USER_LOGIN` | Login de l'utilisateur |
| `GITLAB_USER_EMAIL` | E-mail de l'utilisateur |
| `GITLAB_USER_NAME` | Nom complet de l'utilisateur |

### 3.10 Pages, tentative, artefacts

| Variable | Description |
|---|---|
| `CI_PAGES_URL` | URL du site GitLab Pages |
| `CI_PAGES_DOMAIN` | Domaine Pages |
| `GET_SOURCES_ATTEMPTS` | Tentatives max de `get_sources` (défaut : 3) |
| `INSTALL_DEPENDENCIES_ATTEMPTS` | Tentatives max d'installation de dépendances |
| `RESTORE_CACHE_ATTEMPTS` | Tentatives max de restauration du cache |
| `SAVE_CACHE_ATTEMPTS` | Tentatives max de sauvegarde du cache |
| `ARTIFACTS_PATHS` | Chemins des artefacts (défini par `artifacts:paths`) |
| `ARTIFACTS_GRADE` | Grade des artefacts (`debug`, `archive`, `preliminary`) |
| `ARTIFACTS_EXCLUDE` | Chemins exclus des artefacts |
| `ARTIFACTS` | `true` si des artefacts sont produits |
| `DEPLOY_*` / `KUBERNETES_*` | Variables injectées par les executors/deployments K8s |

### 3.11 Variables personnalisées (GitLab)

```yaml
variables:
  APP_ENV: production            # dans le YAML
  DB_PASSWORD: $PROD_DB_PASSWORD # héritée d'une variable de niveau supérieure
```

- **Settings → CI/CD → Variables** : chiffrées, option *masked*, *protected*, *expanded*.
- **Scope** : instance, groupe, projet, environment.
- `protected: true` → uniquement sur refs protégées ; `masked: true` → masquées dans les logs.
- Injection fichier : définir `MY_VAR_FILE` avec une valeur → `$MY_VAR_FILE` pointe vers un fichier temporaire.

---

## 4. Mapping GitHub ↔ GitLab

| Concept | GitHub Actions | GitLab CI/CD |
|---|---|---|
| Commit SHA | `github.sha` / `$GITHUB_SHA` | `$CI_COMMIT_SHA` |
| SHA court | — (formatter) | `$CI_COMMIT_SHORT_SHA` |
| Branche | `github.ref_name` | `$CI_COMMIT_BRANCH` |
| Tag | `github.ref_type == 'tag'` | `$CI_COMMIT_TAG` |
| Branche par défaut | — (via `vars`) | `$CI_DEFAULT_BRANCH` |
| Référence complète | `github.ref` | `$CI_COMMIT_REF_PATH` |
| Message de commit | `github.event.head_commit.message` | `$CI_COMMIT_MESSAGE` |
| Dépôt | `github.repository` | `$CI_PROJECT_PATH` |
| ID du projet/run | `github.run_id` | `$CI_PIPELINE_ID` |
| ID du job | `github.job` | `$CI_JOB_ID` |
| Nom du job | `github.job` | `$CI_JOB_NAME` |
| Événement déclencheur | `github.event_name` | `$CI_PIPELINE_SOURCE` |
| Utilisateur acteur | `github.actor` | `$GITLAB_USER_LOGIN` |
| Token du job | `secrets.GITHUB_TOKEN` / `github.token` | `$CI_JOB_TOKEN` |
| Répertoire de travail | `github.workspace` | `$CI_PROJECT_DIR` |
| URL du pipeline/run | `github.server_url}/{owner}/{repo}/actions/runs/{run_id}` | `$CI_PIPELINE_URL` |
| URL de l'API | `github.api_url` | `$CI_API_V4_URL` |
| Secrets | `secrets.*` / `vars.*` | Variables CI/CD (masked/protected) |
| Env de déploiement | `github.event.environment` | `$CI_ENVIRONMENT_NAME` |

---

## 5. Bonnes pratiques

1. **Mapper avant d'utiliser** (GitHub) : `${{ secrets.X }}` dans `env:`, puis `$X` dans `run:`.
2. **Protéger les secrets** : GitHub *Encrypted secrets* / GitLab *masked + protected* — jamais en clair dans les logs.
3. **Restrindre le token** : GitHub — déclarer le minimum sous `permissions:` ; GitLab — le `CI_JOB_TOKEN` suit le principe du moindre privilège (allowlist projets).
4. **Refs protégées** : utiliser `github.ref_protected` / `$CI_COMMIT_REF_PROTECTED` pour conditionner les déploiements.
5. **Pipelines de tag** : `$CI_COMMIT_TAG` / `github.ref_type` pour la release uniquement sur tag.
6. **Ne pas coder en dur** : toute valeur variable (versions, endpoints) passe par `vars` / variables CI/CD.
7. **Éviter `CI_DEBUG_TRACE`** en production : il expose tout, y compris les secrets masqués non protégés.

## 6. Références officielles

- GitHub — Contexts : https://docs.github.com/en/actions/reference/contexts
- GitHub — Default environment variables : https://docs.github.com/en/actions/reference/workflows-and-actions/variables
- GitLab — Predefined variables : https://docs.gitlab.com/ci/variables/predefined_variables/
- GitLab — CI/CD variables : https://docs.gitlab.com/ci/variables/
