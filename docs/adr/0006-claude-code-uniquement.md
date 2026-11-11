# ADR-0006 — Compatibilité : Claude Code uniquement (pour l'instant)

- **Statut** : Accepté
- **Date** : 2026-11-11
- **Inspirations** : [AIDD](../inspirations/aidd.md) (distribution via la marketplace Claude Code)

## Contexte

La vision initiale prévoyait un cœur méthodologique indépendant de l'outil, pour pouvoir l'adapter plus tard à d'autres agents. Deux sources étudiées vont dans ce sens :

- SFEIR : *« standardiser le contexte et la preuve, pas l'outil »* ;
- le manifeste AIDD, principe 12 : rendre la pratique transmissible entre outils.

Mais l'étude d'AIDD montre aussi ce que coûte le multi-outils : une CLI de traduction, des formats de distribution par outil, des garde-fous de portabilité. Or JDLC est un projet solo, à ses débuts, et son auteur travaille avec Claude Code.

## Options envisagées

1. **Multi-outils dès le départ** — portabilité maximale, mais coût de conception et de maintenance élevé avant même d'avoir un premier composant utile.
2. **Cœur agnostique + adaptateur Claude Code** — portabilité préservée, mais chaque choix doit être pensé pour être neutre, ce qui ralentit et prive des fonctions natives (hooks, sous-agents, marketplace).
3. **Claude Code uniquement** — on exploite pleinement l'outil réellement utilisé ; la portabilité est reportée.

## Décision

**JDLC cible uniquement Claude Code pour l'instant.** On peut utiliser librement ses fonctions natives : plugins et marketplace, skills, sous-agents, commandes, hooks, mémoire, règles.

## Conséquences

- L'option « plugin Claude Code + marketplace » de l'[ADR-0005](0005-mecanisme-installation.md) devient la piste naturelle pour l'installation. L'ADR-0005 reste « à affiner ».
- On n'investit pas dans la portabilité. Le contenu restant majoritairement du Markdown, une ouverture à d'autres outils reste possible plus tard, via un nouvel ADR qui remplacera celui-ci.
- Les inspirations orientées multi-outils (CLI de traduction d'AIDD, principe 12 du manifeste) sont notées mais non reprises.
- Les éventuelles dépendances à des fonctions propres à Claude Code sont assumées et documentées.
