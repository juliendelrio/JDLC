# ADR-0005 — Mécanisme d'installation dans les projets

- **Statut** : Proposé — à affiner (orientation retenue comme hypothèse de travail, à confirmer avec les premiers composants concrets)
- **Date** : 2026-11-11

## Contexte

JDLC doit pouvoir être « installé » dans mes projets. Un SDLC agentique se compose typiquement de deux familles d'éléments :

- **des éléments outillage**, utilisés par l'agent : instructions, skills, sous-agents, commandes, hooks ;
- **des éléments projet**, qui vivent dans le dépôt cible : fichier d'instructions du projet (`CLAUDE.md` / `AGENTS.md`), arborescence `docs/` (specs, ADR…), gabarits.

Les besoins :

- installer en une commande ;
- mettre à jour sans écraser les adaptations locales ;
- savoir quelle version de JDLC est installée dans un projet ;
- à terme, ne pas être prisonnier d'un seul outil agentique.

## Options envisagées

1. **Plugin Claude Code + marketplace** — le dépôt JDLC sert de marketplace ; l'installation et la mise à jour passent par le gestionnaire de plugins de Claude Code.
   - ➕ Installation et mises à jour natives, versionnement, rien à copier dans le projet pour la partie outillage.
   - ➖ Spécifique à Claude Code ; ne couvre pas les éléments qui doivent vivre dans le dépôt cible.
2. **Script d'initialisation / synchronisation** (`jdlc init`, `jdlc update`) qui copie les fichiers dans le projet cible et enregistre la version installée.
   - ➕ Indépendant de l'outil agentique ; les fichiers sont versionnés dans le projet et adaptables.
   - ➖ Les mises à jour demandent une stratégie de fusion (fichiers modifiés localement).
3. **Sous-module ou subtree git**.
   - ➕ Versionnement explicite par commit.
   - ➖ Friction connue des sous-modules ; les fichiers ne sont pas à l'emplacement attendu par les outils.
4. **Dépôt modèle (*template repository*)**.
   - ➕ Trivial pour démarrer un projet.
   - ➖ Inutilisable pour un projet existant ; aucune mise à jour.

## Orientation (non définitive)

Une approche **hybride** paraît la plus prometteuse :

- un **cœur méthodologique en Markdown**, indépendant de l'outil (processus, gabarits, checklists) ;
- une **distribution sous forme de plugin Claude Code** pour la partie outillage (option 1) ;
- une **commande d'initialisation** pour poser les éléments projet dans le dépôt cible (option 2), éventuellement exposée par le plugin lui-même.

### Mise à jour du 2026-11-11

- **Périmètre réduit.** L'[ADR-0006](0006-claude-code-uniquement.md) limite JDLC à Claude Code : le besoin « ne pas être prisonnier d'un seul outil » et le « cœur indépendant de l'outil » ne sont plus des objectifs.
- **Précédent concret.** Le framework [AIDD](../inspirations/aidd.md) se distribue exactement selon l'option 1 :
  - le dépôt est une marketplace (`.claude-plugin/marketplace.json`), avec les plugins dans `plugins/` ;
  - l'installation passe par `/plugin marketplace add` puis `/plugin install` ;
  - le mémoire projet et l'onboarding sont générés par un skill dans le projet cible (option 2, portée par le plugin lui-même).

L'orientation devient donc : **plugin(s) Claude Code distribués via la marketplace du dépôt JDLC, avec un skill d'initialisation pour les éléments projet.**

## Décision

Reportée. On tranchera quand les premiers composants du SDLC existeront et qu'on pourra tester l'installation sur un vrai projet. La structure et les capacités exactes du système de plugins seront vérifiées dans la documentation officielle à jour au moment de décider.
