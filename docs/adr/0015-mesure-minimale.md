# ADR-0015 — Mesurer JDLC : le minimum utile

- **Statut** : Accepté (hypothèse à vérifier par l'usage, voir [ADR-0011](0011-versionnement.md))
- **Date** : 2026-11-11
- **Inspirations** : [single agent loop kit](../inspirations/single-agent-loop-kit.md) (test avec et sans) ; [skills Anthropic](../inspirations/anthropic-skills.md) (évaluation des skills) ; [DORA 2025](../inspirations/dora-2025.md) (indicateurs) ; [documentation Claude Code](../inspirations/claude-code-docs.md) (`claude plugin eval`) ; [synthèse](../synthese-des-sources.md), arbitrage A7 et contradiction 2.12

## Contexte

Le principe 1 demande de savoir si un composant aide. Les méthodes d'évaluation complètes des sources sont lourdes. Il faut choisir un niveau d'effort.

## Options envisagées

1. **Minimal** : un test avec et sans par skill, et 2 ou 3 indicateurs tirés de git.
2. **Méthode complète** pour chaque composant.
3. **Pas de mesure formelle** au début.

## Décision

**Option 1.**

- Chaque skill a un **test avec et sans** : si le résultat est le même sans lui, il est supprimé. Ce test est rejoué à chaque nouveau modèle.
- **2 ou 3 indicateurs tirés de git** suivent l'effet de JDLC sur un projet. Leur choix reste à faire.
- **Les tests de skills sont assumés.** Leur coût n'est pas un obstacle : ce sont des contrôles faits une fois par version. Un ton plus impératif ou une description plus insistante ne s'ajoutent qu'après un test qui montre l'échec.

## Conséquences

- L'outil de départ est `claude plugin eval`, à vérifier dans la documentation au moment de construire.
- La mesure nourrit le passage en 1.0 ([ADR-0011](0011-versionnement.md)).

## Précisions du 2026-11-12

- **Les trois indicateurs sont choisis** : la part des pull requests qu'un humain fait corriger, le temps d'attente d'une pull request avant sa relecture par un humain, les défauts trouvés après fusion. Voir l'[ADR-0017](0017-cycle-complet.md).
- Ils se lisent dans la forge (pull requests, tickets) plus que dans git seul. La façon de les relever reste à concevoir.
