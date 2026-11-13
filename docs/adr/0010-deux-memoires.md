# ADR-0010 — Deux mémoires : celle de l'utilisateur, celle du projet

- **Statut** : Accepté (principe). Le format, l'emplacement et les règles d'écriture de la mémoire de projet restent à concevoir.
- **Date** : 2026-11-11
- **Inspirations** : [synthèse croisée](../synthese-des-sources.md) (arbitrage A4) ; [Anthropic A7](../inspirations/anthropic.md) (hiérarchie et consolidation de la mémoire) ; [AIDD](../inspirations/aidd.md) (mémoire projet versionnée) ; [compound engineering](../inspirations/compound-engineering.md) (fiches de leçons dans le dépôt) ; [kits d'ingénierie Anthropic](../inspirations/anthropic-engineering-kits.md) (journal de leçons et promotion)

## Contexte

Claude Code tient une mémoire automatique, enregistrée par machine et par projet. Elle est utile, mais invisible pour les autres instances et pour les autres membres de l'équipe ([ADR-0007](0007-concu-pour-l-equipe.md)). Une leçon qui y reste ne profite qu'à un seul environnement.

Les sources mettent pourtant la capitalisation au cœur du cycle (convergence 1.6 de la synthèse). Elles le font souvent avec des fichiers versionnés : la mémoire projet d'AIDD, les fiches de leçons de compound engineering, le journal de leçons des kits d'Anthropic.

## Options envisagées

1. **Tout dans la mémoire de Claude Code.** Rien à construire, mais l'apprentissage reste local à une machine.
2. **Tout dans des fichiers versionnés.** C'est partagé, mais les préférences personnelles d'un développeur se retrouvent imposées à toute l'équipe.
3. **Deux mémoires, selon la portée de ce qu'on apprend.**

## Décision

- **La mémoire de Claude Code garde ce qui est propre à l'utilisateur** : ses préférences, sa façon de travailler, ses corrections personnelles.
- **Une mémoire de projet, faite de fichiers versionnés dans le dépôt**, reçoit tout ce qui doit être un **apprentissage transverse** : ce qui vaut pour toutes les instances et pour toute l'équipe.

## Conséquences

- Chaque leçon doit être classée : propre à l'utilisateur, ou transverse au projet. En cas de doute, elle reste dans la mémoire de l'utilisateur jusqu'à ce qu'elle se confirme.
- La mémoire de projet est du contexte versionné. Elle se relit et s'entretient comme du code (principe 10) : elle est datée, consolidée, et ses entrées périmées sont supprimées.
- Elle ne remplace pas les autres artefacts de projet. Une leçon peut ensuite être **promue** sous une forme plus forte : règle, test, hook, ADR (voir la convergence 1.6 de la synthèse).
- Restent à décider :
  - **qui écrit dans la mémoire de projet, et quand** : librement, ou sur proposition validée (arbitrage A4 de la synthèse) ;
  - son emplacement et son format ;
  - comment elle est chargée sans alourdir le contexte toujours présent.

## Précisions du 2026-11-12

- **« Qui écrit dans la mémoire de projet, et quand » est tranché** par l'[ADR-0013](0013-validation-humaine-regles-memoire.md) : un agent propose, un humain valide. L'emplacement, le format et le chargement restent à décider.
