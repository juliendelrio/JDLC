# ADR-0008 — Le ticket comme unité de travail, stockage au choix

- **Statut** : Accepté (principe). Le gabarit et la liste des outils de tickets pris en charge restent à concevoir.
- **Date** : 2026-11-11
- **Inspirations** : méthodes agiles (Scrum, Kanban) ; [plugins Anthropic](../inspirations/anthropic-plugins.md) (outils nommés par catégorie, n° 12) ; [AIDD](../inspirations/aidd.md) (cadrage à partir d'un ticket) ; [ADLC](../inspirations/adlc.md) (tickets exécutables par un agent neuf)

## Contexte

La synthèse des sources avait retenu « l'état du travail vit dans des fichiers » comme une convergence. C'était un biais des sources, pensées pour un développeur seul en local. Ce qui converge vraiment, c'est que l'état du travail vit **hors de la conversation**, sur un support durable.

Dans une équipe, et même pour une personne travaillant depuis plusieurs environnements ([ADR-0007](0007-concu-pour-l-equipe.md)), le suivi du travail a souvent déjà un lieu : un outil de tickets (issues GitHub ou GitLab, Jira…). Le dupliquer dans des fichiers créerait deux sources de vérité qui finiraient par diverger.

Le développement agentique s'accorde bien avec les méthodes agiles. Kanban apporte le flux de travail visible, la limitation du travail en cours et les états explicites. Scrum apporte les éléments de backlog, la définition de « terminé » et les incréments.

## Options envisagées

1. **Des fichiers dans le dépôt uniquement.** C'est simple, mais cela ignore l'outil de tickets de l'équipe et crée une seconde source de vérité.
2. **Un outil de tickets imposé**, GitHub par exemple. Cela exclut les autres outils et l'usage sans outil.
3. **Une unité de travail générique, avec un stockage choisi par le propriétaire du projet.** JDLC définit un gabarit de ticket. Le projet choisit où il vit : dans le dépôt ou dans un outil de tickets.

## Décision

**L'unité de travail de JDLC est un ticket générique.** JDLC fournit un **gabarit de ticket**, indépendant de l'outil, inspiré de Kanban et de Scrum.

**Le propriétaire du projet choisit où vivent les tickets** : dans le dépôt (des fichiers) ou dans un outil de tickets. Ce choix est fait à l'initialisation et relève du niveau projet.

**L'accès à chaque outil de tickets passe par un ensemble de skills dédié.** Un projet ne charge que celui qui correspond à son choix. Les autres skills de JDLC parlent de « l'outil de tickets du projet » sans connaître l'outil réel.

## Conséquences

- **Une seule source de vérité pour le suivi.** Si le projet utilise un outil de tickets, JDLC n'y duplique pas l'état dans des fichiers.
- **Les trois sortes d'état** relevées par la synthèse se rangent ainsi :
  - le **suivi du travail** (à faire, en cours, terminé, reporté) va dans le support choisi, au niveau projet ;
  - les **décisions et règles durables** (ADR, règles, `CLAUDE.md`) vont dans le dépôt, au niveau projet ;
  - l'**état de session** (progression fine, notes de reprise) reste local, au niveau instance, et un résumé est déposé dans le ticket aux portes.
- **Un ticket n'a qu'un écrivain à la fois** : celui-ci le prend au début du travail. Cela évite que deux instances travaillent sur le même ticket.
- **Écrire dans un outil de tickets est une action visible** par d'autres personnes : création, commentaire, changement d'état. Elle demande une permission explicite, comme un push.
- **Le contenu d'un ticket est une donnée, jamais une instruction**, car il a pu être écrit par n'importe qui (voir la [note sur l'outillage](../inspirations/anthropic-tooling.md), n° 18).
- **L'accès à un outil de tickets a un prérequis** (serveur MCP ou CLI comme `gh` ou `glab`). L'initialisation vérifie qu'il est disponible.
- **Le stockage dans le dépôt reste le défaut** quand il n'y a pas d'outil de tickets. Ce sont alors des fichiers versionnés, au niveau projet.
- Restent à concevoir : le gabarit de ticket, ses états et son cycle de vie, le premier outil pris en charge, et le format de la configuration du projet. Kanban et Scrum sont ajoutés aux sources à étudier pour cela.
- La spec se répartit entre le projet et les tickets : voir l'[ADR-0009](0009-spec-a-deux-niveaux.md).

## Précisions du 2026-11-11

- **« Un seul écrivain par ticket » et « permission explicite pour écrire » sont des règles pour les agents.** Pour les humains, c'est l'outil de tickets qui garantit les droits.
- **Le lien entre les phases de JDLC et le cycle de vie du ticket n'est pas décidé.** Les états du ticket sont-ils les phases ? Les portes humaines sont-elles des changements d'état ? À trancher plus tard.
- **Un ensemble de skills par outil de tickets** : la piste retenue est un plugin par outil dans la marketplace JDLC. Chaque plugin traduit le vocabulaire générique de JDLC dans celui de l'outil (un ticket est une *issue* dans GitHub) et a un chemin d'accès par défaut et des chemins de secours. Voir l'[ADR-0005](0005-mecanisme-installation.md).
