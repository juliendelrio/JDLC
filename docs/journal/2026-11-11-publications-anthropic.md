# 2026-11-11 — Les publications d'Anthropic

## Ce qu'on a fait

Étude de sept articles du blog d'Anthropic ([note d'analyse](../inspirations/anthropic.md)) :

- deux articles fondateurs, désormais datés : les motifs d'agents (2024) et l'ingénierie de contexte (2025) ;
- cinq articles récents : harness pour applications longues, mode auto de Claude Code, Managed Agents, post-mortem qualité d'avril 2026, composants de la mémoire d'agent.

Les pages sont « © Anthropic PBC » sans licence de réutilisation : on reste au niveau « idée ».

## Pourquoi cette source compte particulièrement

JDLC cible Claude Code ([ADR-0006](../adr/0006-claude-code-uniquement.md)). Anthropic décrit donc à la fois des principes généraux *et* le fonctionnement interne de l'outil sur lequel JDLC repose : mémoire, compaction, permissions, mode auto.

## Ce qu'on a appris

- **Forte convergence avec SFEIR et AIDD.**
  - Séparer production et évaluation, car l'auto-évaluation est complaisante.
  - Définir « terminé » avant de coder.
  - Partir minimal et enrichir à partir des échecs.
  - Traiter le contexte comme du code.
- **Trois idées vraiment nouvelles :**
  1. **Un composant de JDLC est une hypothèse** sur les limites du modèle. Ces hypothèses se périment : JDLC devra être réexaminé à chaque nouveau modèle, et savoir *maigrir*.
  2. **La fatigue d'approbation.** Les utilisateurs acceptent 93 % des demandes de permission. Trop de validations humaines finissent par ne plus rien valider : c'est un argument fort pour peu de portes, mais des portes significatives.
  3. **Changer JDLC, c'est changer le comportement des agents.** Le post-mortem montre qu'une simple ligne d'instruction peut dégrader la qualité sans qu'aucun test ne le voie. Les évolutions de JDLC devront être mesurées et utilisées par moi-même dans leur version publiée.
- **La mémoire, moins mais mieux.** Une mémoire abondante et périmée nuit. Il faut un contexte injecté court et une consolidation périodique.

## Prochaines étapes de l'analyse

Restent à étudier : Spec Kit, BMAD, Superpowers, AI-DLC (AWS), ADLC, l'article de Google, DORA 2025, Kiro, le *compound engineering* et la documentation Claude Code.
