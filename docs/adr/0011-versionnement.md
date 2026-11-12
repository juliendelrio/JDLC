# ADR-0011 — Versionner JDLC : 0.1.0 à la première implémentation, 1.0 après vérification par l'usage

- **Statut** : Accepté
- **Date** : 2026-11-11
- **Inspirations** : principe 1 de la [vision](../vision.md) (empirique d'abord) ; [single agent loop kit](../inspirations/single-agent-loop-kit.md) (dire ce qui a vraiment été exécuté)

## Contexte

Une relecture a relevé un écart avec le principe 1 : JDLC compte beaucoup de notes, de principes et d'ADR, mais aucun composant utilisé sur un vrai projet. Plusieurs ADR sont acceptés « en principe », sans usage réel.

Ce n'est pas une contradiction si on le dit clairement : on prépare la première version du cadre, et ses décisions sont des hypothèses.

## Décision

- **JDLC est versionné.**
- **Aujourd'hui, JDLC n'a pas encore de version** : rien n'est implémenté. C'est la phase de conception.
- **La version 0.1.0 sera la première implémentation** : le premier composant installable et essayé.
- **Tant que la version est inférieure à 1.0, les décisions sont des hypothèses de travail.** Un ADR « Accepté » signifie « retenu pour être essayé ».
- **Le passage en 1.0 demande la vérification empirique** de ces décisions : elles ont été utilisées sur un vrai projet et ont tenu, ou elles ont été corrigées.

## Conséquences

- Chaque décision devra pouvoir dire, à terme, sur quoi elle a été vérifiée. La forme de ce suivi reste à concevoir.
- Une décision démentie par l'usage est remplacée par un nouvel ADR, comme le prévoit l'[ADR-0001](0001-tracer-les-decisions.md).
- Le numéro de version sera porté par le futur `plugin.json` ([ADR-0005](0005-mecanisme-installation.md)).
- La priorité, après les arbitrages en cours, est de construire un premier composant et de l'essayer : c'est lui qui fera la 0.1.0.
- Reste à décider : faut-il un numéro pour la phase de conception (par exemple 0.0.x), ou aucun.

## Précisions du 2026-11-12

- **Périmètre de la 0.1.0** : elle outille d'abord les étapes où des agents travaillent seuls (découpage, réalisation, vérification, bilan). Le cadrage et la rétrospective se font d'abord sans outillage JDLC. Voir l'[ADR-0017](0017-cycle-complet.md).
