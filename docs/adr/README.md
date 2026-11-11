# Registre des décisions (ADR)

Un **ADR** (*Architecture Decision Record*) est une courte fiche qui documente une décision : son contexte, les options envisagées, le choix fait et ses conséquences. Le format est inspiré de Michael Nygard (« Documenting Architecture Decisions », 2011) — voir [CREDITS.md](../../CREDITS.md).

## Règles

- Un fichier par décision : `NNNN-titre-court.md`, numérotation continue.
- Statuts : `Proposé` → `Accepté` | `Rejeté` ; un ADR accepté peut devenir `Remplacé par ADR-XXXX`.
- On ne réécrit pas un ADR accepté : on en crée un nouveau qui le remplace. L'historique des raisonnements fait partie de la valeur pédagogique.
- Gabarit : [template.md](template.md).

## Index

| N° | Titre | Statut |
|---|---|---|
| [0001](0001-tracer-les-decisions.md) | Tracer les décisions avec des ADR | Accepté |
| [0002](0002-langues.md) | Langues de la documentation et du code | Accepté |
| [0003](0003-politique-inspirations-licences.md) | Politique d'inspiration et de licences | Accepté |
| [0004](0004-licence-du-projet.md) | Licence du projet JDLC | Accepté |
| [0005](0005-mecanisme-installation.md) | Mécanisme d'installation dans les projets | Proposé — à affiner |
| [0006](0006-claude-code-uniquement.md) | Compatibilité : Claude Code uniquement (pour l'instant) | Accepté |
| [0007](0007-concu-pour-l-equipe.md) | Conçu pour l'équipe, utilisable seul | Accepté |
| [0008](0008-ticket-unite-de-travail.md) | Le ticket comme unité de travail, stockage au choix | Accepté (principe) |
| [0009](0009-spec-a-deux-niveaux.md) | Une spec à deux niveaux : principale et de ticket | Accepté (principe) |
| [0010](0010-deux-memoires.md) | Deux mémoires : celle de l'utilisateur, celle du projet | Accepté (principe) |
