# YAML CI/CD Cheat Sheet
## GitHub Actions & GitLab CI/CD

**Quick reference · DevOps · 2026**

---

## 1. YAML fundamentals

| Syntax | Meaning | Example |
|---|---|---|
| `key: value` | Key-value pair | `name: CI` |
| `- item` | List element | `branches: [main]` |
| `# comment` | Comment | `# Run tests` |
| `true` / `false` | Boolean | `enabled: true` |
| `[]` | Empty list | `needs: []` |
| `{}` | Empty mapping | `options: {}` |
| `\|` | Multiline string, preserves newlines | `script: \|` |
| `>` | Folded multiline string | `description: >` |
| `${VARIABLE}` | Shell variable expansion | `echo "$HOME"` |
| `${{ ... }}` | GitHub Actions expression | `${{ github.ref }}` |
| `$VARIABLE` | GitLab variable reference | `$CI_COMMIT_BRANCH` |

**Rules:** use consistent indentation (usually two spaces), never use tabs for indentation, quote ambiguous strings, and keep list indentation consistent.

```yaml
# Mapping
database:
  host: localhost
  port: 5432

# List
services:
  - api
  - database

# Multiline command
script: |
  echo "Starting"
  npm test
```

## 2. GitHub Actions — `.github/workflows/ci.yml`

### Basic workflow

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '22'
          cache: npm

      - name: Install
        run: npm ci

      - name: Test
        run: npm test
```

### Most-used keywords

| Keyword | Purpose |
|---|---|
| `name` | Workflow or job name |
| `on` | Events that trigger the workflow |
| `push` | Run on a push |
| `pull_request` | Run on pull requests |
| `workflow_dispatch` | Manual trigger |
| `schedule` | Scheduled execution |
| `jobs` | Define jobs |
| `runs-on` | Select runner |
| `steps` | Define ordered steps |
| `uses` | Execute an action |
| `run` | Execute shell commands |
| `with` | Pass action inputs |
| `env` | Set environment variables |
| `if` | Conditional execution |
| `needs` | Job dependencies |
| `strategy.matrix` | Run multiple configurations |
| `permissions` | Set `GITHUB_TOKEN` permissions |
| `concurrency` | Control simultaneous runs |
| `timeout-minutes` | Limit job execution time |

### Conditions and expressions

```yaml
steps:
  - name: Run only on main
    if: github.ref == 'refs/heads/main'
    run: echo "Main branch"

  - name: Run after failure
    if: failure()
    run: echo "A previous step failed"

  - name: Run always
    if: always()
    run: echo "Cleanup"
```

Common contexts and functions:

- `github.ref` — branch or tag reference.
- `github.sha` — commit SHA.
- `github.event_name` — triggering event.
- `env.MY_VAR` — environment variable.
- `vars.MY_VAR` — repository or organization variable.
- `secrets.MY_SECRET` — secret value.
- `needs.build.result` — dependency result.
- `success()`, `failure()`, `cancelled()`, `always()` — status functions.

### Dependencies and parallel jobs

```yaml
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - run: npm run lint

  test:
    runs-on: ubuntu-latest
    steps:
      - run: npm test

  deploy:
    needs: [lint, test]
    runs-on: ubuntu-latest
    steps:
      - run: ./deploy.sh
```

`lint` and `test` run in parallel; `deploy` waits for both.

### Matrix builds

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node: ['20', '22', '24']

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node }}
      - run: npm ci
      - run: npm test
```

### Artifacts and caching

```yaml
- uses: actions/upload-artifact@v4
  with:
    name: build-output
    path: dist/

- uses: actions/download-artifact@v4
  with:
    name: build-output
    path: dist/
```

Caching with `actions/setup-node`:

```yaml
- uses: actions/setup-node@v4
  with:
    node-version: '22'
    cache: npm
```

### Secrets and deployments

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: production

    steps:
      - name: Deploy
        env:
          DEPLOY_TOKEN: ${{ secrets.DEPLOY_TOKEN }}
        run: ./deploy.sh
```

Configure `production` and its protection rules in repository settings. Never print secrets or hardcode credentials.

---

## 3. GitLab CI/CD — `.gitlab-ci.yml`

### Basic pipeline

```yaml
stages:
  - lint
  - test
  - build
  - deploy

variables:
  NODE_ENV: test

default:
  image: node:22

lint:
  stage: lint
  script:
    - npm ci
    - npm run lint

test:
  stage: test
  script:
    - npm ci
    - npm test

build:
  stage: build
  script:
    - npm ci
    - npm run build
  artifacts:
    paths:
      - dist/
    expire_in: 1 week

deploy:
  stage: deploy
  script:
    - ./deploy.sh
  rules:
    - if: '$CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH'
      when: manual
```

### Most-used keywords

| Keyword | Purpose |
|---|---|
| `stages` | Define execution order |
| `stage` | Assign a job to a stage |
| `image` | Select a Docker image |
| `services` | Start supporting service containers |
| `script` | Commands executed by the job |
| `before_script` | Commands executed before the script |
| `after_script` | Commands executed afterward |
| `variables` | Define CI/CD variables |
| `rules` | Control whether a job runs |
| `workflow` | Control pipeline creation |
| `needs` | Define job dependencies |
| `artifacts` | Preserve job outputs |
| `cache` | Reuse files between jobs |
| `include` | Import configuration |
| `extends` | Inherit job configuration |
| `environment` | Define a deployment environment |
| `when: manual` | Require a manual job action |
| `allow_failure` | Allow a job to fail without failing the pipeline |

### Conditions with `rules`

```yaml
deploy_production:
  stage: deploy
  script:
    - ./deploy.sh
  rules:
    - if: '$CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH'
      when: manual
    - when: never
```

Useful predefined variables:

- `$CI_COMMIT_BRANCH` — branch name for branch pipelines.
- `$CI_COMMIT_TAG` — tag name for tag pipelines.
- `$CI_COMMIT_SHA` — commit SHA.
- `$CI_DEFAULT_BRANCH` — default branch.
- `$CI_PIPELINE_SOURCE` — pipeline trigger source.
- `$CI_PROJECT_DIR` — checkout directory.
- `$CI_REGISTRY_IMAGE` — project container image path.

### Dependencies and artifacts

```yaml
build:
  stage: build
  script:
    - npm ci
    - npm run build
  artifacts:
    paths:
      - dist/

test:
  stage: test
  needs:
    - job: build
      artifacts: true
  script:
    - npm test
```

`needs` allows a job to start as soon as its specified dependencies are complete, without waiting for every job in earlier stages.

### Caching

```yaml
cache:
  key:
    files:
      - package-lock.json
  paths:
    - .npm/

install:
  stage: test
  script:
    - npm ci --cache .npm --prefer-offline
```

### Docker services

```yaml
integration_test:
  image: docker:27-cli
  services:
    - name: docker:27-dind
      alias: docker
  variables:
    DOCKER_HOST: tcp://docker:2375
    DOCKER_TLS_CERTDIR: ""
  script:
    - docker info
```

This example requires a runner configured to support Docker-in-Docker. For production environments, configure appropriate TLS and runner security rather than disabling TLS indiscriminately.

### Reusable jobs

```yaml
.node_template:
  image: node:22
  before_script:
    - npm ci

lint:
  extends: .node_template
  script:
    - npm run lint

test:
  extends: .node_template
  script:
    - npm test
```

A job whose name starts with `.` is hidden and can serve as a reusable template.

---

## 4. GitHub Actions vs. GitLab CI/CD

| Concept | GitHub Actions | GitLab CI/CD |
|---|---|---|
| Configuration | `.github/workflows/*.yml` | `.gitlab-ci.yml` |
| Execution unit | Job | Job |
| Command step | `steps[].run` | `script` |
| Reusable component | Action / reusable workflow | Include / template / component |
| Job order | `needs` | `stages` and `needs` |
| Runner | `runs-on` | Runner tags and configuration |
| Conditional execution | `if` | `rules:if` |
| Environment variables | `env` | `variables` |
| Secrets | `secrets` context | Protected/masked CI/CD variables |
| Build outputs | Upload/download artifact actions | `artifacts` keyword |
| Cache | Cache actions | `cache` keyword |
| Matrix execution | `strategy.matrix` | `parallel:matrix` |
| Manual execution | `workflow_dispatch` / approval | `when: manual` / pipeline triggers |
| Reuse full pipelines | Reusable workflows | `include` and downstream pipelines |

## 5. YAML mistakes to avoid

1. **Wrong indentation:** use spaces, not tabs.
2. **Incorrect quoting:** quote strings that could be parsed as booleans, dates, or special values.
3. **Confusing execution models:** GitHub `needs` and GitLab `needs` both express dependencies, but stage behavior differs.
4. **Mixing cache and artifacts:** caches speed up future jobs; artifacts transfer outputs.
5. **Exposing credentials:** use the platform's secret-management features.
6. **Overusing privileged runners:** grant only the permissions and system access required.
7. **Ignoring validation:** test configurations before merging.

## 6. Official references

- GitHub: https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax
- GitHub expressions: https://docs.github.com/en/actions/reference/evaluate-expressions-in-workflows-and-actions
- GitLab YAML reference: https://docs.gitlab.com/ci/yaml/
- GitLab CI/CD variables: https://docs.gitlab.com/ci/variables/
- YAML specification: https://yaml.org/spec/1.2.2/

**Best practice:** keep your pipeline configuration thin, extract repeated logic into reusable components, pin third-party dependencies to reviewed versions, and validate every change in CI.
