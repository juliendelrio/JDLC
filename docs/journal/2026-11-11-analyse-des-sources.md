# 2026-11-11 — Analyse de toutes les sources listées

## Ce qu'on a fait

Onze sources restaient à étudier. Elles ont été analysées en parallèle par six agents, chacun chargé de deux sources proches :

| Groupe | Sources |
|---|---|
| Développement piloté par les specs | [Spec Kit](../inspirations/spec-kit.md), [Kiro](../inspirations/kiro.md) |
| Méthodes et skills pour agents | [BMad Method](../inspirations/bmad.md), [Superpowers](../inspirations/superpowers.md) |
| Cycles de vie agentiques | [AWS AI-DLC](../inspirations/aws-ai-dlc.md), [ADLC](../inspirations/adlc.md) |
| Recherche et études | [Google — The New SDLC](../inspirations/google-new-sdlc.md), [DORA 2025](../inspirations/dora-2025.md) |
| Capitalisation et pratiques | [Compound engineering](../inspirations/compound-engineering.md), [Claude Code Tips (YK)](../inspirations/claude-code-tips-yk.md) |
| Outil cible | [Documentation Claude Code](../inspirations/claude-code-docs.md) |

**Méthode.** Chaque agent a reçu les mêmes consignes :

- lire la licence au commit exact ;
- reformuler sans copier ;
- suivre le format de la note AIDD ;
- relier chaque idée aux sources déjà étudiées ;
- ne modifier aucun autre fichier.

Les fichiers communs ont été mis à jour ensuite, par le coordinateur seul : [CREDITS.md](../../CREDITS.md), l'[index des notes](../inspirations/README.md) et l'[ADR-0005](../adr/0005-mecanisme-installation.md).

## Ce qu'on a appris sur les licences

La vérification au commit exact a payé plusieurs fois :

- **Les métadonnées GitHub ne suffisent pas.** BMad affiche `NOASSERTION` parce que son fichier `LICENSE` ajoute une mention de marque. C'est bien du MIT, mais le nom « BMad » est protégé.
- **Une même source peut avoir plusieurs licences.**
  - DORA : le rapport PDF est sous CC BY-NC-SA, incompatible avec JDLC ; le site dora.dev est sous CC BY, compatible.
  - AWS, ADLC, *compound engineering* : l'article est sous droits réservés, le dépôt sous licence libre.
  - Règle retenue : toute adaptation part du dépôt, jamais de l'article.
- **Un dépôt sous licence libre peut contenir du contenu tiers.** Superpowers semble embarquer une copie de documentation Anthropic, que sa licence MIT ne couvre probablement pas. Ce fichier est à ne pas importer.
- **Un dépôt populaire peut ne pas être libre.** Le dépôt de conseils de YK est « tous droits réservés ».

## Ce qu'on a appris sur le fond

Les idées nouvelles les plus marquantes :

- **Consignes ou garanties** (documentation Claude Code, Google). `CLAUDE.md` et les skills sont des consignes que le modèle peut ignorer ; les hooks et les règles `deny` sont garantis par l'outil. Une consigne souvent oubliée et détectable mécaniquement doit devenir un hook. Exemple : la discipline de la preuve imposée par un hook `Stop`.
- **Chaque composant se justifie par une défaillance du modèle** (ADLC), sinon on le retire. Cette idée prolonge celle d'Anthropic (les composants sont des hypothèses qui se périment).
- **Deux portes humaines argumentées** (ADLC) : l'intention validée une fois en mots (la spec), une fois en comportement (l'acceptation).
- **Une approbation exige un tour humain observé** (AWS) : l'agent ne peut pas s'auto-approuver.
- **La forme la moins chère qui empêche la répétition** (*compound engineering*) : supprimer la cause, puis un test ou un lint, puis une règle, et en dernier recours une note.
- **Préserver les adaptations locales lors des mises à jour** (Spec Kit, BMad), ce qui concerne directement l'installation.
- **Mesurer l'apport de JDLC** : `claude plugin eval` compare une session avec et sans plugin ; DORA suggère quelques mesures simples.

**Contrainte découverte pour l'installation :** un plugin Claude Code ne peut livrer ni `CLAUDE.md`, ni règles, ni permissions. L'approche hybride (plugin + skill d'initialisation) devient la seule voie ([ADR-0005](../adr/0005-mecanisme-installation.md)).

## Prochaine étape

Toutes les sources listées sont analysées. La suite naturelle est une **synthèse croisée** :

- les convergences, divergences et questions ouvertes ;
- un premier tri de ce que JDLC garde.
