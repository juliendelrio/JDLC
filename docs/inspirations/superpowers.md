# Superpowers (obra/superpowers)

| | |
|---|---|
| **Source** | https://github.com/obra/superpowers (annonce d'origine : https://blog.fsck.com/2025/10/09/superpowers/) |
| **Auteur** | Jesse Vincent (obra) et l'équipe Prime Radiant |
| **Version étudiée** | v7.0.2, commit `22f0b463e7a5e579904dd6211a520321035526f7` (2026-11-03), HEAD de `main` au moment de la consultation |
| **Consulté le** | 2026-11-11 |
| **Licence** | **MIT**, « Copyright (c) 2025 Jesse Vincent » (fichier `LICENSE` lu à ce commit ; `plugin.json` déclare aussi `MIT`) |
| **Niveau de reprise possible** | Idée, adaptation ou import (licence compatible), en conservant la mention de copyright. **Exception** : le fichier `skills/writing-skills/references/anthropic-best-practices.md` semble reproduire la documentation d'Anthropic ; la licence MIT du dépôt ne couvre vraisemblablement pas ce contenu tiers : ne pas l'importer. |
| **Niveau actuel** | Idée |

---

## Synthèse

### En bref

Superpowers se décrit comme une **méthodologie complète de développement logiciel** pour agents de code, construite sur une bibliothèque de **skills composables** et sur des instructions de départ qui obligent l'agent à s'en servir. Sa promesse : l'agent ne se jette pas sur le code ; il commence par comprendre ce qu'on veut vraiment, fait valider une conception, écrit un plan détaillé, puis l'exécute de façon autonome, souvent pendant des heures, sans dériver du plan.

Philosophie affichée, en quatre points : TDD toujours, le **systématique plutôt que l'improvisé**, la **réduction de la complexité**, et **la preuve plutôt que l'affirmation**.

### Contenu du dépôt

- `skills/` : 15 skills, chacun un dossier avec un `SKILL.md` et, au besoin, des prompts de sous-agents, des références et des scripts.
- `hooks/` : un hook `SessionStart` (déclenché au démarrage, après `/clear` et après compaction).
- `.claude-plugin/` : `plugin.json` et un `marketplace.json` de développement ; une dizaine d'autres dossiers adaptent le même contenu à d'autres outils (Codex, Cursor, Gemini, OpenCode, Pi…).
- `docs/superpowers/specs/` et `docs/plans/` : le projet conserve ses propres conceptions et plans.
- `tests/` : tests de l'infrastructure du plugin ; les tests de comportement des skills passent par un harnais d'évaluation séparé (dépôt `superpowers-evals`).

### Installation et distribution

- **Claude Code** : plugin disponible dans la **marketplace officielle** d'Anthropic (`/plugin install superpowers@claude-plugins-official`), ou via la marketplace de l'auteur (`/plugin marketplace add obra/superpowers-marketplace`).
- Le même dépôt sert une quinzaine d'outils (Codex, Cursor, Copilot CLI, Gemini CLI, OpenCode…), chacun avec son mode d'installation.
- Mises à jour : généralement automatiques, selon l'outil.

### Le mécanisme d'amorçage

C'est la clé de voûte du système :

1. le hook `SessionStart` lit le skill `using-superpowers` et l'**injecte en entier** dans le contexte, à chaque démarrage, `/clear` et compaction ;
2. ce skill impose une règle : **vérifier s'il existe un skill applicable avant toute réponse ou action**, y compris avant une question de clarification ; s'il y a la moindre chance qu'un skill s'applique, il faut l'invoquer ;
3. un tableau de **« signaux d'alerte »** liste les rationalisations typiques de l'agent (« c'est juste une question simple », « je vais d'abord explorer le code »…) et leur oppose la règle ;
4. les instructions de l'utilisateur (`CLAUDE.md`, demandes directes) restent prioritaires sur les skills.

Résultat : l'utilisateur n'a rien à invoquer, les skills se déclenchent seuls à partir de leur description.

### Le flux de travail de base

| # | Skill | Rôle |
|---|---|---|
| 1 | `brainstorming` | Faire émerger ce que l'utilisateur veut **et pourquoi**, avant tout code |
| 2 | `using-git-worktrees` | Isoler le travail dans un worktree sur une nouvelle branche, vérifier que les tests passent au départ |
| 3 | `writing-plans` | Découper en petites tâches avec fichiers exacts, code et étapes de vérification |
| 4 | `subagent-driven-development` ou `executing-plans` | Exécuter le plan : un sous-agent neuf par tâche avec revue après chacune, ou tout en ligne avec une revue finale |
| 5 | `test-driven-development` | Cycle rouge-vert-refactor strict |
| 6 | `requesting-code-review` | Revue au regard du plan, constats par sévérité ; les critiques bloquent |
| 7 | `finishing-a-development-branch` | Vérifier les tests, puis proposer : fusionner, ouvrir une PR, garder ou abandonner la branche |

**Portes humaines** : choix de l'approche et approbation du document de conception (dans `brainstorming`), relecture du plan, choix du mode d'exécution, puis décision d'intégration de la branche. Entre le plan et la fin, l'exécution est volontairement **continue**.

### Mécanismes notables par skill

**`brainstorming`** (v7.0.2, retravaillé à partir de tests) :

- d'abord se demander ce qu'on sait déjà ; si la demande est complète, on la fait sans cérémonie ;
- **lire avant de demander** : ne jamais poser une question à laquelle le projet ou le contexte répond déjà ;
- **une question à la fois**, et seulement si la réponse changerait ce qui sera construit ; demander **pourquoi** en premier si ce n'est pas dit ; quand un choix se nomme, présenter les options avec une recommandation ;
- proposer deux ou trois approches avec leurs compromis, faire choisir, puis écrire un document de conception **dimensionné** (court pour un petit changement, complet pour un projet), avec une section *Approaches* qui garde la trace des options écartées ;
- rien n'est implémenté avant l'approbation du document, sauf un *spike* explicitement accepté.

**`writing-plans`** :

- le plan s'adresse à un ingénieur compétent **sans aucun contexte** sur le projet : il doit contenir ce qu'il ne peut pas deviner (fichiers, noms, signatures, valeurs de la spec, tests qui prouvent chaque tâche) ;
- en-tête imposé : objectif, architecture, pile, lien vers la spec, **contraintes globales** recopiées à l'identique, et une section **Review Focus** (les cas que la spec implique mais qu'aucun test ne couvre) ;
- une tâche est la plus petite unité qui porte son propre cycle de test et mérite la porte d'un relecteur neuf.

**`subagent-driven-development`** :

- un **sous-agent implémenteur neuf par tâche**, qui ne reçoit que ce qu'on lui construit (pas l'historique de session) ;
- une **revue par tâche en deux verdicts** : conformité à la spec (ni plus, ni moins), puis qualité du code ; puis une **revue finale** de toute la branche ;
- **exécution continue** : pas de « je continue ? » entre les tâches. Les ambiguïtés sont tranchées par l'agent, et chaque décision est consignée dans un **registre** sous la forme : décision, raison, coût si elle est fausse ;
- **quatre seuls motifs d'arrêt** : opération irréversible ou destructive, action sensible pour la sécurité, effet de bord hors du worktree (fusion, push sur une branche partagée, publication), plan si cassé que toute suite serait une supposition.

**`verification-before-completion`** : une « loi d'airain » : aucune déclaration de réussite sans preuve fraîche. Procédure : identifier la commande qui prouve l'affirmation, l'exécuter entièrement, lire la sortie, puis seulement conclure. Un tableau associe chaque affirmation (« les tests passent », « le bug est corrigé », « l'agent a terminé ») à la preuve exigée et à ce qui ne suffit pas (un rapport de sous-agent n'est pas une preuve, un diff l'est).

**`systematic-debugging`** : pas de correctif sans investigation de la cause racine, en quatre phases, avec des techniques associées (remonter la cause, défense en profondeur, attente sur condition plutôt que sur délai).

**`receiving-code-review`** : recevoir une revue avec rigueur technique, pas avec une **approbation de façade** : reformuler, vérifier contre le code, évaluer, puis accepter ou contester avec arguments, un point à la fois.

**`writing-skills`** : **écrire un skill, c'est du TDD appliqué à la documentation**. On écrit d'abord un scénario de pression joué par un sous-agent *sans* le skill, on observe l'échec et les rationalisations, on écrit le skill minimal qui les corrige, puis on ferme les échappatoires. Principe : *si l'on n'a pas vu un agent échouer sans le skill, on ne sait pas si le skill enseigne la bonne chose*. Une référence s'appuie sur une étude sur l'effet des principes de persuasion sur les LLM pour justifier le ton impératif des skills.

**`diagnosing-superpowers`** : quand une session se passe mal (skill qui ne se déclenche pas, plan ignoré, travail répété, coût excessif), l'agent relit les transcriptions de session sur disque et produit un rapport où **chaque constat cite un fichier et une ligne**, avec option d'un paquet anonymisé pour un rapport de bug.

### Pratiques du projet

- Les **notes de version mesurent les changements de comportement** : par exemple, nombre de questions inutiles et d'implémentations prématurées avant et après, sur des conversations de test notées à l'aveugle.
- Les contributions de nouveaux skills sont rarement acceptées, et les PR écrites par un agent pour corriger le ticket de quelqu'un d'autre sont refusées sauf demande d'un mainteneur.
- Une **télémétrie optionnelle** (logo chargé depuis le site de l'éditeur dans le compagnon visuel de brainstorming) est documentée, avec les variables pour la désactiver.

---

## Ce que JDLC peut en retenir

### Idées qui confirment des pistes existantes

| # | Idée | Ce que ça donnerait dans JDLC | Convergence |
|---|---|---|---|
| 1 | **Pas de déclaration de réussite sans preuve fraîche**, avec un tableau affirmation → preuve exigée | Gabarit concret pour le principe 8 : une table de correspondance « ce que l'agent affirme / ce qu'il doit montrer ». La licence MIT permettrait une adaptation. | Principe 8, SFEIR (discipline de la preuve), AIDD (checker) |
| 2 | **Comprendre l'intention avant de construire, et demander pourquoi** ; conception approuvée avant tout code | Base du skill de cadrage / spécification de JDLC. | AIDD (Frame), SFEIR (Define), BMad (intention bien définie), principe 6 |
| 3 | **Dimensionner le document de conception** (court ou complet) et garder la trace des approches écartées | Rejoint le plan proportionné ; la section « approches » est l'équivalent léger d'un ADR. | AIDD (plan proportionné), BMad (rationale et options rejetées), principe 7 |
| 4 | **Sous-agent neuf par tâche, revue indépendante** de la conformité puis de la qualité, revue finale de la branche | Confirme le vérificateur indépendant ; ajoute la séparation en deux verdicts. | AIDD (checker neuf), Anthropic A3, BMad (lentilles) |
| 5 | **Plan écrit pour un exécutant sans contexte**, avec contraintes globales et tâches à vérification propre | Critère de qualité du plan JDLC. | BMad (« prêt pour le développement »), Anthropic A2 (notes structurées) |
| 6 | **Diagnostiquer les sessions ratées à partir des transcriptions, avec citations** | Outille la phase Retour d'expérience et la « revue de JDLC ». | Anthropic A6 (les retours d'usage sont un signal), principe 11, manifeste AIDD |
| 7 | **Mesurer l'effet d'un changement de skill** (tests notés à l'aveugle, chiffres avant/après) | Confirme l'idée d'une petite suite d'évaluations de JDLC. | Anthropic A6, BMad (`bmad-eval`) |

### Idées nouvelles pour JDLC

| # | Idée | Ce que ça donnerait dans JDLC | Convergence |
|---|---|---|---|
| 8 | **Amorçage par hook `SessionStart` (démarrage, `/clear`, compaction)** qui injecte un skill d'aiguillage | Mécanisme pour que les skills JDLC se déclenchent de façon fiable, sans invocation manuelle, et survivent à la compaction. À peser contre le coût en contexte à chaque session. | ADR-0005, ADR-0006 ; BMad cite une étude où un skill non signalé n'est pas invoqué dans plus de la moitié des cas |
| 9 | **Écrire un skill comme du TDD** : scénario de pression sans le skill, échec observé, skill minimal, fermeture des échappatoires | Méthode de conception de chaque skill JDLC, cohérente avec le principe 1 (empirique d'abord) et 11 (partir des échecs). | Anthropic A2 (partir minimal, ajouter à partir des échecs), BMad (comparer au modèle nu) |
| 10 | **Tables de rationalisations** (« signaux d'alerte ») dans les skills | Pour chaque règle importante de JDLC, lister les excuses que l'agent se donne pour la contourner, observées en test. | — |
| 11 | **Exécution continue avec registre des décisions** (décision, raison, coût si faux) et **liste fermée des motifs d'arrêt** | Rend l'autonomie auditable : l'humain relit les décisions après coup au lieu d'être interrompu. Éclaire le débat 2 ou 3 portes. | Anthropic A4 (fatigue d'approbation, paliers de risque), AIDD (mode auto) |
| 12 | **Recevoir une revue sans approbation de façade** | Règle transverse : toute remarque (humaine ou d'agent) est vérifiée contre le code avant d'être appliquée ou contestée. | — |
| 13 | **Débogage par cause racine** comme skill à part entière | Un skill JDLC hors cycle nominal, déclenché par un échec de test ou un bug. | Principe 11 (réparer le système), SFEIR (« un bug vu deux fois ») |
| 14 | **Isolation par worktree et menu de fin de branche** (fusionner / PR / garder / abandonner) | Matérialise la porte humaine « merge » de la vision ; vérifier une base de tests verte avant de commencer. | Principe 6 |
| 15 | **« Lire avant de demander »** et une seule question à la fois, seulement si la réponse change ce qui est construit | Règle de conduite des skills de cadrage, contre les questions de remplissage. | AIDD (demander plutôt que choisir un défaut en silence) |

## Ce qu'on écarte ou relativise

- **Le multi-outils** (une quinzaine d'outils, dossiers d'adaptation par outil) : hors périmètre ([ADR-0006](../adr/0006-claude-code-uniquement.md)).
- **Le ton coercitif** (règle du « 1 % de chance », blocs `EXTREMELY-IMPORTANT`, « vous n'avez pas le choix ») : efficace selon l'auteur, mais il peut provoquer des déclenchements excessifs et coûte du contexte à chaque session. À n'adopter que si des tests montrent qu'il est nécessaire avec les modèles actuels.
- **Le TDD dogmatique** (supprimer le code écrit avant les tests) : la discipline de la preuve de JDLC demande une preuve, pas forcément l'ordre test-puis-code. À garder comme option forte, pas comme règle universelle.
- **L'exécution continue sans pause** : compatible avec « l'humain garde la main » seulement si les portes amont (conception, plan) et aval (fusion) restent obligatoires. Le défaut de JDLC en usage solo reste à trancher à l'usage, comme pour le mode auto d'AIDD.
- **La télémétrie** (même optionnelle) : JDLC n'en embarque pas.
- **Le contenu tiers dans le dépôt** : la copie des bonnes pratiques d'Anthropic sur l'écriture de skills n'est pas couverte par la licence MIT de Superpowers ; si on en a besoin, on cite la source Anthropic directement (niveau idée).

## Sources secondaires à étudier

- L'annonce d'origine : https://blog.fsck.com/2025/10/09/superpowers/ (contexte et motivations de l'auteur ; licence du blog non vérifiée).
- Le harnais d'évaluation **superpowers-evals** : https://github.com/prime-radiant-inc/superpowers-evals (licence non vérifiée) : méthode de test du comportement des skills.
- `skills/writing-skills/references/testing-skills-with-subagents.md` et `persuasion-principles.md` : méthode de test des skills et l'étude citée (Meincke et al., 2025) sur la persuasion des LLM, à lire à la source.
- `docs/porting-to-a-new-harness.md` : utile seulement si JDLC s'ouvre un jour à d'autres outils.
