# Crédits et inspirations

JDLC se construit à partir de mon expérience **et** du travail d'autres personnes. Ce fichier recense, par respect pour leurs auteurs, tout ce qui a été repris, à quel niveau, et sous quelle licence.

Politique et définitions des niveaux (idée / adaptation / import) : [docs/licences.md](docs/licences.md).

## Registre

| Source | Auteur(s) | Version | Licence | Niveau | Ce qui est repris | Où dans JDLC |
|---|---|---|---|---|---|---|
| [Documenting Architecture Decisions](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions) | Michael Nygard | Article de 2011 | — (non importé) | Idée | Le format ADR (contexte, décision, conséquences, statut) | [docs/adr/](docs/adr/README.md) |
| [Un SDLC piloté par l'IA : le cycle SFEIR à 11 phases](https://www.sfeir.com/articles/sdlc-ai/) | SFEIR | 2026-06-16, mis à jour le 2026-09-21 (consulté le 2026-11-11) | © SFEIR, tous droits réservés | Idée | Discipline de la preuve (principe 8), phase de dépréciation, grille du domaine d'application. Pistes encore à l'étude : validations humaines, capitalisation en deux temps, phase Setup | [Vision](docs/vision.md), [note d'analyse](docs/inspirations/sfeir-sdlc-ai.md) |

| [AI-Driven Dev Framework](https://github.com/ai-driven-dev/framework) | AI-Driven Dev contributors | commit `6f70b65` (2026-10-26) | MIT | Idée | Distribution par marketplace Claude Code (ADR-0005, ADR-0006). Pistes à l'étude : cycle Frame/Deliver/Check, 3 pauses selon le mode, vérificateur indépendant, mémoire projet, skill `learn`, anatomie des skills | [ADR-0005](docs/adr/0005-mecanisme-installation.md), [note d'analyse](docs/inspirations/aidd.md) |
| [The Manifesto for AI-Driven Development](https://ai-driven-development.org) | AI-Driven Dev | v1.1.1, commit `b4a7fe2` | CC BY 4.0 (texte), MIT (code) | Idée | Principes 9 (ne pas déléguer ce qu'on ne sait pas évaluer), 10 (le contexte se traite comme du code) et 11 (réparer le système avant de pousser le modèle) | [Vision](docs/vision.md), [note d'analyse](docs/inspirations/aidd.md) |
| [Building effective agents](https://www.anthropic.com/research/building-effective-agents) | Erik S., Barry Zhang (Anthropic) | 2024-12-19 (consulté le 2026-11-11) | © Anthropic PBC | Idée (en étude) | Motifs de workflow, simplicité d'abord, conception des outils | [Note d'analyse](docs/inspirations/anthropic.md) |
| [Effective context engineering for AI agents](https://www.anthropic.com/research/effective-context-engineering-for-ai-agents) | P. Rajasekaran, E. Dixon, C. Ryan, J. Hadfield (Anthropic) | 2025-09-29 (consulté le 2026-11-11) | © Anthropic PBC | Idée (en étude) | Contexte minimal à fort signal, contexte juste à temps, compaction, notes, sous-agents | [Note d'analyse](docs/inspirations/anthropic.md) |
| [Harness design for long-running application development](https://www.anthropic.com/research/harness-design-long-running-apps) | Prithvi Rajasekaran (Anthropic) | 2026-03-24 (consulté le 2026-11-11) | © Anthropic PBC | Idée (en étude) | Générateur/évaluateur séparés, contrats de sprint, composants comme hypothèses à réévaluer | [Note d'analyse](docs/inspirations/anthropic.md) |
| [How we built Claude Code auto mode](https://www.anthropic.com/research/claude-code-auto-mode) | John Hughes (Anthropic) | 2026-03-25 (consulté le 2026-11-11) | © Anthropic PBC | Idée (en étude) | Fatigue d'approbation, permissions par paliers, modèle de menace | [Note d'analyse](docs/inspirations/anthropic.md) |
| [Scaling Managed Agents](https://www.anthropic.com/research/managed-agents) | L. Martin, G. Cemaj, M. Cohen (Anthropic) | 2026-04-08 (consulté le 2026-11-11) | © Anthropic PBC | Idée (en étude) | Interfaces stables, journal durable hors contexte | [Note d'analyse](docs/inspirations/anthropic.md) |
| [An update on recent Claude Code quality reports](https://www.anthropic.com/research/april-23-postmortem) | Anthropic Engineering | 2026-04-23 (consulté le 2026-11-11) | © Anthropic PBC | Idée (en étude) | Un changement d'instructions est un changement de comportement : évaluer, limiter, déployer progressivement | [Note d'analyse](docs/inspirations/anthropic.md) |
| [The components of agent memory](https://www.anthropic.com/research/components-of-agent-memory) | Lance Martin (Anthropic) | 2026-11-06 (consulté le 2026-11-11) | © Anthropic PBC | Idée (en étude) | Hiérarchie de mémoire, contexte injecté minimal, consolidation (« rêve ») | [Note d'analyse](docs/inspirations/anthropic.md) |
| [Senior Engineer's best practices for using coding agents](https://www.youtube.com/watch?v=6b5GyqAdSyM) (vidéo) | « YK » et « Lydia » (d'après la transcription) | Consultée le 2026-11-11 (extrait de transcription) | Contenu YouTube, pas de licence de réutilisation connue | Idée (en étude) | Simplification comme angle de revue, merge comme tri, contrôler les résultats plutôt que les lignes | [Note d'analyse](docs/inspirations/video-senior-engineers-coding-agents.md) |

## Sources à étudier

Pistes identifiées, **pas encore analysées**. Les licences indiquées ici devront être vérifiées sur la version exacte au moment de toute reprise.

| Source | Pourquoi l'étudier | Licence |
|---|---|---|
| GitHub Spec Kit | Développement piloté par les spécifications (*spec-driven development*) | À vérifier |
| BMAD Method | Méthode agentique avec rôles d'agents spécialisés | À vérifier |
| Superpowers (obra) | Bibliothèque de skills et de workflows pour Claude Code | À vérifier |
| AI-Driven Development Lifecycle (AWS) | Proposition de cycle de vie piloté par l'IA | À vérifier |
| ADLC (Chris Williams, 2026) | Cycle agentique en 8 phases, 2 portes humaines, « mesurer la stack, pas le modèle » (cité par SFEIR) | À vérifier |
| The New SDLC With Vibe Coding (Addy Osmani et al., Google, 2026) | « Agent = Model + Harness » ; le système qui produit le code comme livrable principal (cité par SFEIR) | À vérifier |
| DORA 2025 (Google Cloud) | Données sur l'IA comme amplificateur des pratiques existantes (cité par SFEIR) | À vérifier |
| Kiro | Environnement de développement piloté par les specs (cité par SFEIR) | À vérifier |
| Compound engineering | Capitalisation des leçons d'un cycle à l'autre (cité par SFEIR) | À vérifier |
| Documentation et autres publications Anthropic (Claude Code, skills, plugins, *cookbooks* mémoire) | Référence de l'outil cible de JDLC | À vérifier |
| Dépôt de conseils Claude Code de « YK » (cité dans la vidéo ci-dessus) | Recueil de bonnes pratiques concrètes (au moins 38 conseils) ; dépôt à identifier | À vérifier |

Pour chaque source étudiée, une note d'analyse sera rédigée dans `docs/inspirations/` (ce qu'on en retient, ce qu'on écarte, pourquoi).
