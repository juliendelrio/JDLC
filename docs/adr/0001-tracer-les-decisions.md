# ADR-0001 — Tracer les décisions avec des ADR

- **Statut** : Accepté
- **Date** : 2026-11-11
- **Inspirations** : Michael Nygard, « Documenting Architecture Decisions » (2011)

## Contexte

JDLC est un projet public dont la conception doit être documentée dans un but pédagogique. Les décisions vont s'accumuler et évoluer avec l'expérience ; il faut pouvoir retrouver *pourquoi* on a fait un choix, et ce qu'on a écarté.

## Options envisagées

1. **Un document de conception unique** mis à jour au fil de l'eau — simple, mais l'historique des raisonnements se perd à chaque réécriture.
2. **Uniquement l'historique git** — trace le *quoi*, rarement le *pourquoi* ni les alternatives.
3. **Des ADR** — une fiche courte et immuable par décision, plus un journal narratif.

## Décision

On utilise des ADR (`docs/adr/`) pour les décisions, complétés par un journal de conception (`docs/journal/`) qui raconte la démarche.

## Conséquences

- Chaque décision structurante demande quelques minutes de rédaction.
- La documentation pédagogique se construit naturellement au fil du projet.
- Les ADR pourront eux-mêmes devenir une pratique proposée par JDLC aux projets qui l'installent.
