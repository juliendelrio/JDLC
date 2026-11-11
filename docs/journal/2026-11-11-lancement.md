# 2026-11-11 — Lancement du projet

## Point de départ

Un dépôt public vide et une intention : construire mon propre SDLC agentique, à partir de mon expérience et des SDLC existants, en documentant la démarche pour qu'elle serve à d'autres.

## Ce qu'on a posé

Avant d'écrire la moindre ligne de processus, on a posé le **cadre de conception** :

- une **vision** et des principes directeurs ([docs/vision.md](../vision.md)) ;
- une pratique de **traçabilité des décisions** par ADR ([ADR-0001](../adr/0001-tracer-les-decisions.md)) ;
- une convention de **langues** ([ADR-0002](../adr/0002-langues.md)) ;
- une **politique d'inspiration et de licences** ([ADR-0003](../adr/0003-politique-inspirations-licences.md), [docs/licences.md](../licences.md)) et un registre des crédits ([CREDITS.md](../../CREDITS.md)) ;
- deux décisions laissées ouvertes : la **licence du projet** ([ADR-0004](../adr/0004-licence-du-projet.md)) et le **mécanisme d'installation** ([ADR-0005](../adr/0005-mecanisme-installation.md)).

## Pourquoi commencer par là

- **La licence avant les imports.** Sans licence choisie, on ne peut pas savoir ce qu'on a le droit d'importer. D'où la règle : aucun import tant que l'ADR-0004 n'est pas accepté.
- **Distinguer idée et contenu.** S'inspirer d'une méthode n'a pas les mêmes implications que copier un prompt. Les trois niveaux (idée / adaptation / import) rendent cette distinction opérationnelle.
- **Ne pas figer l'installation trop tôt.** Le mécanisme dépend de la forme des composants, qu'on ne connaît pas encore. On a listé les options et une orientation, sans trancher.

## Décisions de fin de session

- **Licence : MIT** ([ADR-0004](../adr/0004-licence-du-projet.md) accepté). Les imports de contenus sous licence compatible deviennent possibles.
- **Installation : à affiner.** L'orientation hybride de l'[ADR-0005](../adr/0005-mecanisme-installation.md) reste une hypothèse de travail.

## Prochaines étapes

1. Étudier les premières sources d'inspiration et rédiger leurs notes d'analyse.
2. Identifier, à partir de mes projets en cours, la première phase du cycle à outiller.
