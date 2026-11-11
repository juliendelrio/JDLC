# GitHub Spec Kit

Spec Kit est la boîte à outils open source de GitHub pour le *Spec-Driven Development* (SDD) : on écrit une spécification, puis on la fait dériver en plan, en tâches et en code avec un agent de développement. C'est aussi l'une des références citées par l'article [SFEIR](sfeir-sdlc-ai.md).

| | |
|---|---|
| **Source** | https://github.com/github/spec-kit (documentation : https://github.github.io/spec-kit/) |
| **Auteur** | GitHub, Inc. et contributeurs (gouvernance communautaire depuis janvier 2026, d'après la page *History*) |
| **Version étudiée** | commit `a91b63ab91edaf4155d4985647b833f2e1e5db61` (2026-11-11), sur `main`, juste après la release `v1.4.0` (2026-11-10) |
| **Consulté le** | 2026-11-11 |
| **Licence** | **MIT**, « Copyright GitHub, Inc. » (fichier `LICENSE` lu à ce commit) |
| **Niveau de reprise possible** | Idée, adaptation ou import (licence compatible ; conserver la mention de copyright et le texte de licence) |
| **Niveau actuel** | Idée |

---

## Synthèse

### En bref

Le README présente Spec Kit comme une boîte à outils qui donne aux agents de développement des *« structured processes, reusable templates, and documented outcomes »*. Le projet a beaucoup évolué depuis sa sortie (août 2025) : à la version étudiée, ce n'est plus seulement un enchaînement de commandes SDD, mais une plateforme de processus composables.

- **Trois processus prêts à l'emploi**, présentés comme des points d'entrée indépendants et non comme des phases obligatoires :
  - le **SDD**, livré dans le cœur ;
  - la **correction de bug** (extension `bug`) ;
  - l'**évaluation d'idée** (extension `assess`).
- **Des primitives pour construire son propre processus** : intégrations, extensions, presets, workflows, bundles, catalogues.
- **Multi-agents** : une quarantaine d'intégrations (Copilot, Claude Code, Gemini, Cursor, Codex, Kiro CLI…).
- **Installation** : une CLI Python, `specify` (installée via `uv`), génère les fichiers dans le projet cible.

### Le cycle SDD

Une **constitution** est rédigée une fois par projet ; les autres étapes se répètent pour chaque fonctionnalité. Le README demande de lancer les commandes une par une, dans le chat de l'agent, **en relisant chaque résultat avant de continuer**.

| Étape | Commande | Artefact produit | Rôle |
|---|---|---|---|
| 0 | `constitution` | `.specify/memory/constitution.md` | Principes non négociables du projet |
| 1 | `specify` | `specs/NNN-feature/spec.md` | Le *quoi* et le *pourquoi*, sans technique |
| 1b | `clarify` *(optionnel)* | `spec.md` mis à jour | Au plus 5 questions ciblées, réponses réinjectées dans la spec |
| 1c | `checklist` *(optionnel)* | `checklists/*.md` | Contrôle de la *qualité des exigences* |
| 2 | `plan` | `plan.md`, `research.md`, `data-model.md`, `contracts/`, `quickstart.md` | Le *comment* : choix techniques justifiés |
| 3 | `tasks` | `tasks.md` | Tâches ordonnées par dépendances, regroupées par user story |
| 3b | `analyze` *(optionnel)* | Rapport | Cohérence croisée spec / plan / tâches, en lecture seule |
| 4 | `implement` | Code | Exécute les tâches et les coche |
| 5 | `converge` | Nouvelles tâches ajoutées à `tasks.md` | Compare le code à la spec, au plan et aux tâches ; boucle avec `implement` jusqu'à `Converged` |

Chaque fonctionnalité est numérotée (`001-`, `002-`…) et a son propre dossier, éventuellement sa branche (extension `git`).

### Ce que disent les gabarits

Le document de méthode (`spec-driven.md`) explique que les gabarits ne sont pas de simples formulaires : ils **contraignent le comportement du modèle**. Les mécanismes principaux, reformulés :

- **Séparer le quoi du comment.** La spec interdit les détails d'implémentation (pile technique, API, structure du code) ; ils n'apparaissent qu'au plan.
- **Marquer l'incertitude au lieu de deviner.** Toute ambiguïté devient un marqueur `[NEEDS CLARIFICATION: …]`. Le modèle ne doit pas combler les trous avec des hypothèses plausibles.
- **Des « tests unitaires pour l'anglais ».** Les checklists vérifient que les exigences sont complètes, claires, cohérentes et testables, et pas que le code fonctionne. Une case cochée appartient au relecteur humain : la commande ne coche jamais elle-même.
- **Des user stories priorisées (P1, P2, P3) et testables indépendamment.** Chacune doit pouvoir être livrée et démontrée seule ; la P1 constitue le MVP.
- **Des exigences et critères identifiés** (`FR-001`, `SC-001`), avec des critères de succès mesurables, et une section *Assumptions* explicite.
- **Des portes constitutionnelles dans le plan.** Le plan contient une section *Constitution Check* ; toute complexité qui viole un principe (par exemple la simplicité) doit être justifiée dans un tableau *Complexity Tracking*.
- **Pas de fonctionnalité spéculative.** Tout doit remonter à une user story.
- **Des tâches parallélisables marquées `[P]`**, rattachées à leur user story, organisées en phases : mise en place, fondations bloquantes, une phase par story, puis finitions.

### La constitution

La constitution est un fichier de principes que le plan et l'analyse doivent respecter. Le document de méthode donne un exemple en neuf articles : bibliothèque d'abord, interface CLI, tests d'abord, simplicité, anti-abstraction, tests d'intégration en environnement réel… Trois articles sont laissés au projet. Elle se modifie par un processus d'amendement explicite : justification, approbation, analyse de compatibilité, avec des amendements datés.

### Convergence et évolution des specs

- **`converge` est en ajout seul.** Il lit spec, plan et tâches comme *seule source d'intention*, évalue le code actuel, et ajoute le travail restant comme nouvelles tâches traçables. Il ne modifie jamais la spec, le plan, les tâches existantes ni le code. On boucle `implement` → `converge` jusqu'à convergence.
- **Le code n'est pas une source d'exigences.** Le guide *evolving specs* demande de distinguer un défaut par rapport à l'intention convenue d'un changement de l'intention elle-même. Si l'intention change, on met d'abord à jour les artefacts, puis on relance le cycle.
- **Quatre modèles de conservation des specs après livraison** (guide *persisting specs*) :

  | Modèle | Principe |
  |---|---|
  | *Flow-back* | Les découvertes remontent dans les artefacts, puis on réconcilie |
  | *Flow-forward* | Une nouvelle exigence = un nouveau dossier de feature (piste d'audit) |
  | *Living spec* | La spec est le contrat qu'on édite ; le reste est régénéré |
  | *Constitution-only* | On ne garde que la constitution ; les artefacts de feature sont retirés |

  Le projet ne tranche pas : c'est un choix d'équipe, avec ses risques (dérive silencieuse, contexte fragmenté, justification perdue).

### Les autres processus livrés

- **Correction de bug** (`bug-assess` → `bug-fix` → `bug-test`) : cause évaluée, correctif circonscrit, vérification enregistrée. Le verdict final est `verified`, `partial` ou `failed` ; seul `verified` confirme le correctif.
- **Évaluation d'idée** (`intake` → `research` → `define` → `shape` → `decide`) : décision argumentée `go`, `needs-clarification` ou `kill`. *S'arrêter avec une raison documentée* est présenté comme un résultat utile. Un `go` transmet un résumé à `specify`.

### Mécanismes d'extension et de distribution

- **Commandes en Markdown avec en-tête** : description, scripts associés (bash, PowerShell, Python) et **handoffs**, c'est-à-dire les étapes suivantes suggérées à l'utilisateur.
- **Hooks d'extension** `before_<commande>` / `after_<commande>`, déclarés dans `.specify/extensions.yml`, **optionnels** (proposés) ou **obligatoires** (exécutés).
- **Presets empilables** pour modifier gabarits, commandes et terminologie sans forker. Ordre de résolution : surcharge locale du projet, puis presets, puis extensions, puis cœur. Un preset `lean` retire la cérémonie des gabarits complets.
- **Workflows** automatisant l'enchaînement des commandes, avec *overlays* pour les modifier sans fork, et **bundles** pour distribuer un ensemble cohérent.
- **Mise à jour qui respecte les modifications locales** : un manifeste d'installation enregistre l'empreinte des fichiers gérés. La mise à jour s'arrête si un fichier a été modifié localement, sauf `--force`, et la constitution existante n'est jamais écrasée.
- **Introspection** : `specify artifact list/info` indique quelle couche fournit chaque commande, gabarit ou hook.
- **Intégration Claude Code** : les commandes sont installées comme skills `.claude/skills/speckit-<commande>/SKILL.md`. Une option les rend invocables par l'utilisateur seulement, pour ne pas encombrer le contexte avec leurs descriptions. Les événements natifs passent par `.claude/settings.json`.

### Spec Kit appliqué à Spec Kit

Le guide *agentic SDLC* décrit comment le projet se développe lui-même. Des agents évaluent les demandes de fonctionnalité et les bugs (workflows déclenchés par label), Spec Kit est utilisé sur ses propres changements importants, et les mainteneurs gardent les décisions de lancer ou de merger. Le guide distingue nettement trois modes de travail : l'automatisation déterministe (scripts, CI), l'automatisation par agent et le travail humain.

---

## Ce que JDLC peut en retenir

### Idées qui confirment des choix ou pistes existants

| # | Idée | Ce que ça donnerait dans JDLC | Convergence |
|---|---|---|---|
| 1 | **Spec (quoi/pourquoi) séparée du plan (comment)** | Deux artefacts distincts, validés séparément ; la spec n'a pas le droit de contenir de technique. | SFEIR (Define / Plan), AIDD (contrat puis plan), phases Spécification / Conception de la vision |
| 2 | **Relire chaque artefact avant l'étape suivante** | Les portes humaines tombent naturellement entre spec, plan et tâches. | Principe 6, AIDD (3 pauses), SFEIR (portes) |
| 3 | **Constitution = principes non négociables versionnés, amendés explicitement** | Équivalent du `CLAUDE.md` / des règles du projet cible, mais avec un processus d'amendement tracé. | Principe 10 (le contexte est du code), ADR |
| 4 | **Portes de simplicité dans le plan + justification de la complexité** | Le plan doit justifier toute complexité au regard des principes. | Principe 7, AIDD (plan proportionné), Anthropic A1 (commencer simple) |
| 5 | **Tâches par user story, chacune livrable et testable seule** | Découpage en incréments vérifiables, MVP d'abord. | SFEIR (Build par tranches), Anthropic A3 (une fonctionnalité à la fois), manifeste AIDD (principe 1) |
| 6 | **Spec Kit appliqué à Spec Kit** | Tracer les évolutions de JDLC avec ses propres artefacts. | AIDD (#12) |
| 7 | **Handoffs** : chaque commande propose l'étape suivante | Chaque skill JDLC se termine en orientant vers la suite logique. | AIDD (onboarding qui oriente) |

### Idées nouvelles

| # | Idée | Ce que ça donnerait dans JDLC | Convergence |
|---|---|---|---|
| 8 | **Marqueurs d'incertitude explicites** (`[NEEDS CLARIFICATION]`) et clarification bornée (au plus 5 questions) | L'agent ne devine pas : il marque, puis pose un petit nombre de questions ciblées. Une spec avec des marqueurs ouverts ne passe pas la porte. | Règle AIDD « demander plutôt que choisir un défaut en silence », rendue concrète et vérifiable |
| 9 | **Checklists de qualité des exigences** (« tests unitaires pour l'anglais »), cochées par l'humain seulement | Critère de sortie de la phase Spécification ; l'agent ne coche pas à la place du relecteur. | Principe 9 (ne pas déléguer ce qu'on ne sait pas évaluer) |
| 10 | **Analyse de cohérence croisée en lecture seule** (spec ↔ plan ↔ tâches) | Contrôle avant implémentation qui *signale* sans corriger. | Prolonge le vérificateur indépendant d'AIDD, mais en amont du code |
| 11 | **Boucle de convergence en ajout seul** | Après implémentation, un contrôle compare le code à l'intention et ajoute les tâches manquantes sans jamais réécrire l'intention pour coller au code. | Principe 8 (preuve) ; complète le *checker* d'AIDD par une règle forte : **le code ne redéfinit pas l'intention** |
| 12 | **Distinguer défaut et changement d'intention** | Un écart entre code et spec est soit un bug (on corrige le code), soit une nouvelle intention (on met d'abord la spec à jour). | AIDD (aiguillage des constats selon leur nature) |
| 13 | **Choisir explicitement un modèle de conservation des specs** (living, flow-forward…) | Question à trancher par ADR : que deviennent les specs après livraison dans un projet cible JDLC ? | Nouveau ; touche la mémoire projet et la dépréciation |
| 14 | **Processus d'évaluation d'idée avec verdict `go` / `needs-clarification` / `kill`** | Phase Cadrage outillée : s'arrêter avec une raison documentée est un résultat valable. | Vision (exploration pure hors périmètre), AIDD (demande prête à planifier) |
| 15 | **Processus bug distinct, avec verdict `verified` / `partial` / `failed`** | Un chemin court pour les corrections, sans spec complète, avec un verdict fondé sur la preuve. | Principe 8 ; plan proportionné d'AIDD |
| 16 | **Mise à jour par manifeste d'empreintes** : on n'écrase pas un fichier modifié localement | Mécanisme concret pour « mettre à jour sans écraser les adaptations locales ». | Principe 5, [ADR-0005](../adr/0005-mecanisme-installation.md) |
| 17 | **Surcharge locale des gabarits par le projet cible** | Un projet peut remplacer un gabarit JDLC sans forker. | Principe 5 |
| 18 | **Skills invocables par l'utilisateur seulement** | Pour les étapes de cycle lancées délibérément, éviter que leurs descriptions consomment le contexte. | Anthropic A2 (contexte fini) |
| 19 | **Hooks avant/après étape, obligatoires ou optionnels** | Brancher des contrôles (lint, tests, preuve) sur les étapes du cycle. | Discipline de la preuve (piste hook de SFEIR) |

## Ce qu'on écarte ou relativise

- **Le multi-agents** (une quarantaine d'intégrations, scripts en trois langages) : hors périmètre, JDLC cible uniquement Claude Code ([ADR-0006](../adr/0006-claude-code-uniquement.md)). La CLI Python d'installation est aussi plus lourde que la marketplace native envisagée.
- **La plateforme de composition** (presets, workflows, overlays, bundles, catalogues privés, authentification, mode hors ligne) : pensée pour des organisations. Pour un usage solo, seules la surcharge locale et la mise à jour respectueuse des modifications sont utiles.
- **Les articles d'exemple de la constitution** (bibliothèque d'abord, CLI obligatoire, maximum 3 projets) : ce sont des choix d'architecture discutables, pas des principes de SDLC. On retient le *mécanisme*, pas le contenu.
- **La rhétorique de l'« inversion »** (le code comme simple produit régénérable de la spec) : le document de méthode est très ambitieux. JDLC reste empirique (principe 1) et ne présuppose pas que l'on régénère le code depuis la spec.
- **Les gains de temps annoncés** (« 12 heures de documentation en 15 minutes ») : illustration de l'éditeur, non vérifiable.
- **La lourdeur des gabarits complets** : le projet lui-même propose un preset `lean`. À rapprocher du plan proportionné d'AIDD.

## Sources secondaires

- Le guide *How Spec Kit develops Spec Kit* (`docs/guides/agentic-sdlc.md`) : cas d'étude d'un SDLC agentique réel, avec la séparation automatisation / agent / humain.
- *Living Specs in SpecKit* (Alfredo Perez, 2026-10-15), cité dans le README : maintenir des specs au niveau des capacités dans la durée.
