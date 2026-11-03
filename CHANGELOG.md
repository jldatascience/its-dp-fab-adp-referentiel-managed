# Changelog

Toutes les évolutions notables du dépôt `its-dp-fab-adp-referentiel-managed` (configuration de déploiement, pipelines,
documentation) sont consignées ici, de la plus récente à la plus ancienne.

Format : [Keep a Changelog](https://keepachangelog.com/fr/1.1.0/). Ce dépôt n'est pas versionné
par tag : chaque entrée est datée (`## AAAA-MM-JJ`). Les évolutions du socle commun sont décrites
dans le `CHANGELOG.md` de `its-dp-fab-cicd-common`, versionné par tag `vX.Y.Z`.

## 2026-11-03

### Added

- Initialisation du dépôt sur le modèle des autres dépôts consommateurs ADP (socle
  `its-dp-fab-cicd-common`, tag `v1.5.0`, catégorie `adp` ; modèle : dépôt ADP Alexandrie, commit
  `08fad3b`), à partir du commit initial `ab86778` (qui ne contenait que `README.md`) :
  - `.gitattributes` et `.gitignore` identiques aux autres dépôts.
  - `CICD/deployment/config.yml` : workspaces `FR - DP - <ENV> - FAB - ADP - Referentiel - MANAGED`
    pour DEV, ITG, PPR et PRD (confirmés le 03/11/2026, tiret simple) ;
    `repository_directory: "../../fabric"` ; `item_types_in_scope` vide ; clé `parameter` absente.
  - `CICD/deployment/dependencies.yml` : `role: consumer`, `category: adp`, `inventory_status: pending`,
    `framework.required: true` (workspaces Framework, `ado_gate`), listes à relever ;
    `guid_policy.forbidden_prefixes` : préfixes des workspaces ADP Referentiel ITG, PPR et PRD de
    l'ancien `deploy.py`, absents du registre du socle.
  - `CICD/deployment/parameter.yml` : aucune règle (en-tête : les deux familles admises pour un ADP).
  - `CICD/pipelines/CI.yml` : template KPMG conservé ; déclencheurs `develop`, `release/*`, `main` ;
    SonarQube `Discovery_DP-FAB-ADP-Referentiel-Managed`, « [Discovery] DP FAB ADP Referentiel Managed »,
    porte « [Discovery] Starter », repris de l'ancienne CI ; filtre d'artefact `"!/*.json"`.
  - `CICD/pipelines/CD.yml` : `extends` de `CICD/pipelines/templates/cd-pipeline.yml@cicd_common`
    (`refs/tags/v1.5.0`), `role: consumer`, `devMode: skip`, coffre `frfrkvdevdp0001`,
    3 service connections ARM ITG, PPR, PRD à renseigner (décision du 31/10/2026),
    `frameworkGate: ado` ; source CI `its-dp-fab-adp-referentiel-managed-CI`, reprise de l'ancienne CD.
  - `CICD/pipelines/PR-Validation.yml` : `extends` de
    `CICD/pipelines/templates/validate-pipeline.yml@cicd_common` (`refs/tags/v1.5.0`).
  - `CICD/ref_dynamique/01_inventaire_github.py` à `04_recoupement.py` et `templates/` : outillage
    des consommateurs ADP, adapté au dépôt (sorties hors du dépôt, workspace Framework du même
    environnement, `framework_items_adp.yml`, `FRAMEWORK_NON_DECLARE`).
  - `fabric/.gitkeep` : dossier des items suivi par Git avant la première synchronisation.
  - README au format des autres consommateurs ; journal des changements (ce fichier).

### Changed

- `README.md` du commit initial (titre seul) remplacé par la documentation du dépôt.

### Vérifié

- `yamllint` (règles du socle) sur `CICD/deployment`, `CICD/pipelines` et `CICD/ref_dynamique/templates` : sans erreur.
- `python -m compileall CICD/ref_dynamique` et `--help` des 4 scripts : sans erreur.
- `deploy.py validate` (socle `v1.5.0`) : code 4 attendu (« Aucun item Fabric »).
- Sur une copie hors dépôt munie d'un item fictif : 0 erreur, `repository_directory` résolu vers
  `fabric/`, matrice `adp` acceptée.
- Scripts 01, 03 et 04 exécutés avec `--out` hors du dépôt : rien n'est écrit dans le dépôt.
  `02_fab_cli.py` n'a pas été exécuté (pas d'accès au tenant).

### À confirmer

- Organisation et service connection GitHub ; noms des 3 service connections ARM ITG, PPR et PRD (création prévue).
- Casse exacte du pipeline CI dans Azure DevOps ; nom exact de la CD Framework.
- Types d'items, règles, `framework.items`, connexions Fabric, Variable Libraries, préfixe du
  workspace DEV dans `guid_policy` et `data_dependencies`, après synchronisation Git Integration ;
  rôle des lakehouses `lkh_storage_<ENV>` de l'ancien `deploy.py`.
- Point V-01 : le filtre `contents: "!/*.json"` de l'artefact CI ne doit pas exclure les fichiers d'items.
