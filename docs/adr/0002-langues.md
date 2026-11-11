# ADR-0002 — Langues de la documentation et du code

- **Statut** : Accepté
- **Date** : 2026-11-11

## Contexte

Le projet est public. Son auteur travaille en français, mais le code et les artefacts destinés aux agents (instructions, prompts, skills) gagnent à être en anglais, langue la plus largement comprise et celle de la majorité des sources d'inspiration.

## Options envisagées

1. **Tout en anglais** — audience maximale, mais documentation de conception moins naturelle à écrire.
2. **Tout en français** — cohérent, mais les artefacts agents seraient moins réutilisables.
3. **Mixte** — documentation de conception en français, code et artefacts techniques en anglais.

## Décision

- Documentation de conception (vision, ADR, journal, politiques) : **français**.
- Code, commentaires, messages de commit, noms de branches, artefacts destinés aux agents (instructions, skills, gabarits installés dans les projets) : **anglais**.

## Conséquences

- Les noms de fichiers restent en ASCII, sans accents.
- Une traduction anglaise de la documentation pourra être envisagée plus tard si le projet trouve une audience.
