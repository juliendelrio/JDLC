# ADR-0016 — Comprendre le tronc, accepter les feuilles sur preuve

- **Statut** : Accepté (hypothèse à vérifier par l'usage, voir [ADR-0011](0011-versionnement.md))
- **Date** : 2026-11-11
- **Inspirations** : [conférences d'Erik S.](../inspirations/anthropic-talks-erik.md) (feuilles et tronc) ; [manifeste AIDD](../inspirations/aidd.md) (principe 9) ; [synthèse](../synthese-des-sources.md), arbitrage A3

## Contexte

Le principe 9 de la vision dit : l'humain n'accepte pas un résultat qu'il ne comprend pas. Pris à la lettre, il oblige à lire et comprendre chaque ligne produite par un agent. Une conférence d'Anthropic propose une nuance selon la zone du code touchée.

- Le **tronc** est le code dont le reste dépend : architecture, modèle de données, interfaces partagées.
- Une **feuille** est un morceau dont rien ne dépend : un écran, un export, un script isolé.

## Options envisagées

1. **Principe 9 strict** : l'humain comprend tout ce qui est fusionné.
2. **Zonage** : comprendre le tronc, accepter les feuilles sur preuve de comportement.
3. **Décider ticket par ticket.**

## Décision

**Option 2.**

- **Sur le tronc**, l'humain comprend le code avant de l'accepter.
- **Sur une feuille**, l'humain peut accepter sans lire tout le code, s'il a la **preuve du comportement** : il a lu les tests, il a vu leur exécution, il a essayé le résultat.

## Conséquences

- Le principe 9 de la vision est précisé dans ce sens.
- La zone est déjà un critère du profil de travail ([ADR-0014](0014-profils-de-travail.md)) : c'est le profil du ticket qui dit quel niveau de compréhension est attendu.
- La preuve reste obligatoire dans tous les cas (principe 8). Le zonage allège la lecture du code, pas la vérification.
- Il faut un moyen fiable de classer un changement en feuille ou en tronc. L'agent propose, l'humain confirme. En cas de doute, c'est le tronc.
- Une feuille peut devenir du tronc quand d'autres morceaux se mettent à en dépendre. À surveiller.
- En équipe, la compréhension du tronc peut être portée par un collègue relecteur.
