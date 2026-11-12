# ADR-0013 — Les règles et la mémoire de projet changent sur validation humaine

- **Statut** : Accepté (hypothèse à vérifier par l'usage, voir [ADR-0011](0011-versionnement.md))
- **Date** : 2026-11-11
- **Inspirations** : [AIDD](../inspirations/aidd.md) (skill `learn` validé par l'humain) ; [kits d'ingénierie Anthropic](../inspirations/anthropic-engineering-kits.md) (règles en lecture seule pendant le travail) ; [synthèse](../synthese-des-sources.md), arbitrage A4

## Contexte

L'[ADR-0010](0010-deux-memoires.md) sépare la mémoire de l'utilisateur et la mémoire de projet. Il restait à décider qui écrit dans la mémoire de projet et dans les règles, et quand.

## Options envisagées

1. L'instance s'écrit librement ; le projet ne change que sur proposition validée.
2. Tout sur validation humaine.
3. Tout libre, avec une consolidation périodique.

## Décision

**Option 2, pour tout ce qui est au niveau projet.** L'agent ne modifie ni la mémoire de projet ni les règles (règles, skills, hooks, `CLAUDE.md`) sans validation de l'humain. Il **propose** le changement ; l'humain l'accepte ou le refuse.

Le but premier est d'**informer** l'humain de ce qui change, plus que de contrôler : il doit savoir ce que le projet a appris.

## Conséquences

- Le principe 11 est précisé : « réparer le système » veut dire, pour l'agent, **proposer une réparation**.
- Les propositions doivent être courtes et groupées, pour ne pas créer de fatigue d'approbation. Leur format reste à concevoir, par exemple un diff avec une ligne « pourquoi ».
- En équipe, la validation passe naturellement par la revue du changement, puisque ces fichiers sont versionnés.
- La mémoire automatique de Claude Code, propre à l'utilisateur, reste régie par les consignes de chaque utilisateur.

## Précisions du 2026-11-11

- **JDLC devra décrire comment un agent propose de lui-même une amélioration.** La voie retenue par l'auteur : une *issue* dans le dépôt GitHub. L'issue porte la proposition, et la validation humaine se fait là.
- À concevoir :
  - le dépôt visé. Une amélioration de JDLC lui-même va dans le dépôt de JDLC. Pour une amélioration propre à un projet, la proposition passerait par l'outil de tickets de ce projet ([ADR-0008](0008-ticket-unite-de-travail.md)). *Cette lecture est celle de l'agent, à confirmer par l'auteur ;*
  - le gabarit de l'issue : le constat, la preuve, le changement proposé ;
  - ce qui déclenche une proposition ;
  - la protection des données : une issue dans un dépôt public ne doit rien contenir du projet où le problème a été vu. Créer une issue est une action visible, qui demande une permission explicite.
