# ADR-0012 — Mode par défaut : supervisé, puis autonomie gagnée

- **Statut** : Accepté (hypothèse à vérifier par l'usage, voir [ADR-0011](0011-versionnement.md))
- **Date** : 2026-11-11
- **Inspirations** : [single agent loop kit](../inspirations/single-agent-loop-kit.md) (n° 5 et 6) ; [synthèse](../synthese-des-sources.md), arbitrage A2

## Contexte

Les sources divergent sur le mode par défaut : autonome (AIDD, défaut actuel de Claude Code), supervisé, ou choisi selon la tâche. Le principe 6 de la vision veut que l'humain garde la main.

## Options envisagées

1. **Supervisé par défaut**, autonomie sur demande explicite.
2. **Autonomie gagnée** : supervisé au départ, puis l'agent continue seul tant que tout est propre.
3. **Selon le profil de travail.**

## Décision

**Option 2.** Un travail commence en mode supervisé. Après un premier point d'étape complet et propre, l'agent peut proposer de continuer seul « tant que c'est propre ». Il revient aux arrêts à la première anomalie.

## Conséquences

- Il faut définir ce qu'est un point d'étape « propre » et ce qu'est une « anomalie » : un test qui échoue, un écart au plan, une vérification sautée.
- Il faut borner une exécution autonome : budget, nombre de tâches, conditions de sortie.
- En mode autonome, chaque point d'étape reste écrit sur le support durable, pour que l'humain puisse relire après coup.
- Ce réglage relève de l'instance : chaque développeur peut rester en supervisé ([ADR-0007](0007-concu-pour-l-equipe.md)).

## Précisions du 2026-11-12

- **Le départ supervisé se place sur le premier ticket d'un projet ou d'un lot** : l'agent réalisateur s'y arrête à chaque étape devant un humain. Si ce ticket est propre, les agents font les suivants seuls.
- **Une anomalie qui arrête un agent** : trois renvois de suite du vérificateur vers le réalisateur, ou une action irréversible à venir. Le ticket passe à l'état « bloqué » et pose une question à un humain. Voir l'[ADR-0017](0017-cycle-complet.md).
