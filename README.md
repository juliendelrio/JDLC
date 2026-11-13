# JDLC

**JDLC (Julien Development Life Cycle)** est un SDLC agentique : un cycle de développement logiciel où une partie du travail (cadrage, spécification, conception, implémentation, revue, tests, livraison) est confiée à des agents IA, encadrés par un processus, des conventions et des points de contrôle humains.

> ⚠️ **En conception, pas encore de version.** Rien n'est encore installable : on commence par poser le cadre et la méthode de conception. La version 0.1.0 sera la première implémentation. Tant que la version est inférieure à 1.0, les décisions sont des hypothèses à vérifier par l'usage ([ADR-0011](docs/adr/0011-versionnement.md)).

## Objectifs

1. **Construire un SDLC agentique**, nourri par l'expérience réelle sur mes projets. Il est pensé pour une équipe et reste utilisable par un développeur seul ([ADR-0007](docs/adr/0007-concu-pour-l-equipe.md)).
2. **S'inspirer des SDLC agentiques existants**, en traçant chaque inspiration et en respectant les licences des sources.
3. **Documenter la conception dans un but pédagogique** : chaque décision est expliquée, avec son contexte et les alternatives écartées, pour que d'autres puissent comprendre le *pourquoi* et pas seulement le *quoi*.
4. **Rendre le SDLC installable** dans n'importe lequel de mes projets (mécanisme à affiner, voir [ADR-0005](docs/adr/0005-mecanisme-installation.md)).

## Documentation

| Document | Contenu |
|---|---|
| [docs/vision.md](docs/vision.md) | Vision, principes directeurs, périmètre |
| [docs/adr/](docs/adr/README.md) | Registre des décisions d'architecture (ADR) |
| [docs/licences.md](docs/licences.md) | Politique d'inspiration et de compatibilité des licences |
| [CREDITS.md](CREDITS.md) | Registre des inspirations et des contenus importés |
| [docs/inspirations/](docs/inspirations/README.md) | Notes d'analyse des sources étudiées |
| [docs/synthese-des-sources.md](docs/synthese-des-sources.md) | Synthèse croisée des sources : convergences, contradictions, arbitrages et leurs décisions |
| [docs/journal/](docs/journal/README.md) | Journal de conception (le récit, étape par étape) |

## Licence

[MIT](LICENSE) — voir [ADR-0004](docs/adr/0004-licence-du-projet.md). Les contenus tiers importés conservent leur propre licence, recensée dans [CREDITS.md](CREDITS.md).
