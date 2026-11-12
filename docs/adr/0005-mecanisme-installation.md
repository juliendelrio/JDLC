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
  - la mémoire projet et l'onboarding sont générés par un skill dans le projet cible (option 2, portée par le plugin lui-même).

L'orientation devient donc : **plugin(s) Claude Code distribués via la marketplace du dépôt JDLC, avec un skill d'initialisation pour les éléments projet.**

### Contraintes relevées dans la documentation de Claude Code (2026-11-11)

Source : [note d'analyse](../inspirations/claude-code-docs.md).

- **Un plugin ne peut livrer ni `CLAUDE.md`, ni `.claude/rules/`, ni règles de permissions.** Il peut livrer skills, agents, hooks, serveurs MCP et styles de sortie. Tout ce qui doit vivre dans le projet cible doit donc être écrit par un skill d'initialisation : l'approche hybride n'est pas un confort, c'est la seule voie.
- **La version installée est enregistrée par machine**, pas dans le dépôt cible. Pour savoir quelle version de JDLC équipe un projet, l'initialisation devra écrire un marqueur.
- **Versionnement** : une version fixée dans `plugin.json` doit être incrémentée à chaque publication, sinon personne ne reçoit la mise à jour. Pas de canaux natifs, pas d'état « déprécié », et un plugin publié ne doit pas être renommé.
- **Coût en contexte** : la description de chaque skill invocable automatiquement est chargée à chaque tour, dans chaque projet où le plugin est actif. C'est un argument pour un JDLC petit.
- **Préserver les adaptations locales** : Spec Kit (manifeste de hachages qui détecte les fichiers modifiés localement) et BMad (fichiers de surcharge séparés des fichiers installés) montrent deux solutions éprouvées pour la commande d'initialisation et de mise à jour.

### Précédents dans les dépôts d'Anthropic (2026-11-11)

Source : [note d'analyse des plugins](../inspirations/anthropic-plugins.md).

- **Le moteur dans le plugin, les règles dans le projet.** Le plugin livre un hook générique ; sa configuration vit dans un fichier du projet (`.claude/<plugin>.local.md` ou un fichier commité). Sans ce fichier, le hook ne fait rien. Cela répond en partie à la question « hook livré par le plugin ou posé par l'init ».
- **Les hooks n'agissent que dans un projet concerné.** La revue de la marketplace officielle refuse un hook qui tourne partout sans vérifier le projet. Pour JDLC : un hook teste le marqueur d'initialisation et sort tout de suite s'il manque.
- **Adaptations locales sur trois niveaux** : utilisateur, projet commité, projet local non commité. Les fichiers installés ne sont jamais modifiés à la main. Les préférences personnelles peuvent passer par `userConfig` dans `plugin.json`, les données persistantes par `${CLAUDE_PLUGIN_DATA}`.
- **Une marketplace peut pointer vers un autre dépôt** (source `git-subdir`). Le dépôt JDLC peut donc rester la marketplace même si un plugin déménage.
- **La configuration qui juge vient de la branche cible** dans la GitHub Action officielle ([note outillage](../inspirations/anthropic-tooling.md)). À garder en tête si JDLC fournit un jour une revue en CI.

### Skills par outil de tickets (2026-11-11)

L'[ADR-0008](0008-ticket-unite-de-travail.md) prévoit un ensemble de skills par outil de tickets, dont un projet ne charge que le sien. Deux voies sont possibles.

| | Un plugin par outil | Copie des skills par l'initialisation |
|---|---|---|
| Installation | `/plugin install`, depuis la marketplace JDLC | Le skill d'initialisation copie les fichiers dans `.claude/skills/` du projet |
| Désinstallation | Une commande ; rien ne reste dans le dépôt | Suppression manuelle des fichiers, ou un skill de désinstallation à écrire |
| Mise à jour | Par la version du plugin | À refaire par l'initialisation ; risque d'écraser une adaptation locale |
| Partage en équipe | Le plugin est déclaré dans le `.claude/settings.json` commité du projet | Les fichiers sont commités, donc partagés d'office |
| Adaptation locale | Par des fichiers de réglages séparés | Directe, mais elle diverge de la source |

**Piste retenue par l'auteur : un plugin par outil.** C'est la voie la plus simple à désinstaller et à mettre à jour, et elle n'écrit rien dans le dépôt cible. À confirmer en construisant le premier.

Précisions de l'auteur :

- **Chaque plugin a un chemin d'accès par défaut et des chemins de secours.** Pour GitHub : la CLI (`gh`) d'abord ; si elle n'est pas disponible, le serveur MCP ; sinon l'API.
- **Un vocabulaire générique, traduit par chaque plugin.** JDLC parle de « ticket » ; le plugin GitHub le traduit en « issue ». La liste des termes génériques et leur traduction par outil restent à écrire.
- **L'initialisation demande quels outils sont disponibles** et n'installe que les plugins correspondants.

### Version de JDLC (2026-11-11)

La version 0.1.0 sera la première implémentation ([ADR-0011](0011-versionnement.md)). Le numéro sera porté par `plugin.json`.

## Décision

Reportée. On tranchera quand les premiers composants du SDLC existeront et qu'on pourra tester l'installation sur un vrai projet. La structure et les capacités exactes du système de plugins seront vérifiées dans la documentation officielle à jour au moment de décider.
