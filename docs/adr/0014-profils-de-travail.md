# ADR-0014 — Dimensionner le processus par des profils de travail

- **Statut** : Accepté (hypothèse à vérifier par l'usage, voir [ADR-0011](0011-versionnement.md))
- **Date** : 2026-11-11
- **Inspirations** : [AWS AI-DLC](../inspirations/aws-ai-dlc.md) (profils nommés) ; [ADLC](../inspirations/adlc.md) (risque et rayon d'impact) ; [conférences d'Erik S.](../inspirations/anthropic-talks-erik.md) (feuilles et tronc) ; [synthèse](../synthese-des-sources.md), arbitrage A5

## Contexte

Toutes les sources proportionnent le processus au travail, mais avec des critères différents : la taille, le risque, le type de travail, la zone du code touchée.

## Options envisagées

1. **Des profils nommés**, choisis selon le risque et la zone touchée.
2. **La taille seule.**

## Décision

**Option 1.** JDLC définit des **profils de travail nommés**. Le profil d'un ticket est choisi selon le **risque** et la **zone** touchée : une *feuille*, dont rien ne dépend, ou le *tronc*, sur lequel le reste s'appuie. L'agent propose le profil, l'humain confirme.

## Conséquences

- Le profil est porté par le ticket ([ADR-0008](0008-ticket-unite-de-travail.md)).
- Le profil règle la profondeur du processus : niveau de spec, vérifications, revue.
- Restent à concevoir : la liste des profils, leurs critères, et ce que chacun règle. Un profil « sans processus », pour l'évident, en fait partie.

## Précisions du 2026-11-12

- **Le profil est fixé au découpage du lot**, et validé par un humain avec le reste du découpage.
- **Entrée courte** : un défaut, une urgence ou une petite demande devient un ticket sans repasser par le cadre ni par les priorités. Un agent propose son profil, un humain le confirme à la création du ticket.
- **Seuls les tickets du tronc ont une porte humaine sur le plan.** Voir l'[ADR-0017](0017-cycle-complet.md).
