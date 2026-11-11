# Compound engineering (Every) : essais et plugin

Le *compound engineering* (d'abord appelé *compounding engineering*) est une pratique formulée chez **Every** par Kieran Klaassen : chaque unité de travail d'ingénierie doit rendre la suivante plus facile, en capturant ses leçons dans le système. Elle est décrite dans deux essais et outillée par un plugin open source. Les trois sources sont étudiées ensemble ici.

| | Essais | Plugin |
|---|---|---|
| **Source** | E1 : [My AI Had Already Fixed the Code Before I Saw It](https://every.to/source-code/my-ai-had-already-fixed-the-code-before-i-saw-it) ; E2 : [Compound Engineering: How Every Codes With Agents](https://every.to/chain-of-thought/compound-engineering-how-every-codes-with-agents) | https://github.com/EveryInc/compound-engineering-plugin (page de présentation : https://every.to/compound-engineering) |
| **Auteur** | E1 : Kieran Klaassen ; E2 : Dan Shipper et Kieran Klaassen (Every) | Every ; mainteneurs Kieran Klaassen et Trevin Chow, avec des contributeurs |
| **Version étudiée** | E1 : publié le 2025-08-18 ; E2 : publié le 2025-12-11 | commit `4ba48871919650d43a412e3faf0374272fdc209d` (2026-11-11), plugin v3.37.1 |
| **Consulté le** | 2026-11-11 | 2026-11-11 |
| **Licence** | Aucune mention de licence ni de copyright trouvée sur les pages : tous droits réservés par défaut | **MIT**, « Copyright (c) 2025 Every » (fichier `LICENSE` lu au commit étudié) |
| **Niveau de reprise possible** | **Idée uniquement** | Idée, adaptation ou import (licence compatible) |
| **Niveau actuel** | Idée | Idée |

Le dépôt est le plus visible de ceux étudiés jusqu'ici (environ 26 000 étoiles à la consultation). L'article de [SFEIR](sfeir-sdlc-ai.md) le cite parmi les approches convergentes, et ses deux phases *Compound* en reprennent le nom.

---

## 1. Les essais

### E1 — L'origine du terme (2025-08)

- **Le déclic.** L'auteur ouvre une pull request et découvre que Claude Code l'a déjà relue, en appliquant de lui-même des remarques faites sur des PR précédentes.
- **Définition.** Construire des systèmes de développement qui s'améliorent : chaque itération rend la suivante plus rapide, plus sûre et meilleure. Il l'oppose à l'usage courant de l'IA, centré sur le gain immédiat (on demande, on génère, on livre, on recommence de zéro). Ici, chaque PR, chaque bug et chaque revue deviennent une leçon durable.
- **Exemple : un détecteur de frustration** pour Cora, l'assistant e-mail d'Every.
  - Une conversation d'exemple, puis un test écrit d'abord (TDD) qui échoue.
  - L'agent écrit la logique de détection et itère sur le prompt jusqu'à ce que le test passe.
  - Le test est lancé dix fois : 4/10 au départ. L'agent analyse les échecs, trouve le cas manquant (la frustration polie) et corrige : 9/10, jugé suffisant.
  - Le déroulé est ensuite consigné dans `CLAUDE.md` pour être réutilisé.
- **Où ranger les leçons :**
  - `CLAUDE.md` pour les préférences de code personnelles ;
  - `llms.txt` pour les décisions d'architecture ;
  - des **tests, alertes et évaluations** qui transforment un échec en contrôle permanent ;
  - des **agents de revue spécialisés** (le style de l'auteur, Rails, performance).
- **Cinq conseils :** apprendre au système en travaillant ; transformer les échecs en améliorations ; paralléliser (un agent planifie, un implémente, un relit) ; garder un contexte maigre et spécifique, en élaguant les règles inutiles ; faire confiance au système mais vérifier.
- **Résultats annoncés** (non étayés) : délai de livraison passé d'une semaine à un à trois jours, revues de PR en heures au lieu de jours.

### E2 — La boucle (2025-12)

- **Thèse.** En ingénierie classique, chaque fonctionnalité rend la suivante plus difficile, car la complexité s'accumule. En *compound engineering*, la base de code se complexifie aussi, mais la connaissance que l'IA en a grandit plus vite, car chaque bug, test raté ou découverte est capturé. Les auteurs parlent d'une *« learning loop »*.
- **Quatre étapes :**
  1. **Plan** : les agents explorent le code, l'historique git et les bonnes pratiques, puis écrivent un plan (objectif, architecture, pistes, sources, critères de succès). L'humain réfléchit et oriente.
  2. **Work** : le plan devient une liste de tâches ; l'agent utilise l'application comme un utilisateur (Playwright, simulateur) pour trouver et corriger ses erreurs.
  3. **Assess** : l'agent se relit, l'humain aussi ; linters, tests, et une revue par une douzaine de sous-agents en parallèle (sécurité, performance, sur-construction…) dont les constats sont synthétisés pour que l'humain trie.
  4. **Compound** : les leçons deviennent des prompts ou des règles dans le dépôt. Exemple : avant d'ajouter quoi que ce soit, l'agent doit vérifier où le changement doit vivre, si quelque chose de similaire existe et si une solution passée est réutilisable.
- **Répartition de l'effort** : environ 80 % en planification et revue, 20 % en exécution et capitalisation.
- **Les leçons vivent dans le dépôt** : toute l'équipe en profite, et un nouvel arrivant démarre avec la connaissance des anciens.
- **Affirmations fortes** : un développeur bien outillé ferait le travail de cinq ; écrire des tests à la main ou du code fortement documenté deviendrait superflu. Ce sont des opinions d'auteur, sans données.

## 2. Le plugin

### En bref

Le plugin se présente comme *« AI skills that make each unit of engineering work easier than the last »*. À la version étudiée :

- 37 skills, un seul plugin ; le dépôt est une marketplace Claude Code (installation par `/plugin marketplace add`), et vise 14 outils agentiques ;
- configuration par projet dans `.compound-engineering/config.yaml`, créée par `/ce-setup` ;
- artefacts rangés par défaut dans `docs/plans/` et `docs/solutions/` (relocalisables).

### La boucle en six étapes

| Étape | Skill | Rôle |
|---|---|---|
| Brainstorm | `ce-brainstorm` | Questions-réponses pour clarifier le besoin ; produit un plan réduit aux exigences |
| Plan | `ce-plan` | Enrichit ce même document jusqu'à un plan d'implémentation |
| Work | `ce-work` | Exécute le plan, vérifie, commite |
| Simplify | `ce-simplify-code` | Simplifie le code tout juste écrit, élague tests et commentaires inutiles, avant la revue |
| Review | `ce-code-review` | Revue multi-agents au regard du plan, en mode rapport par défaut |
| Compound | `ce-compound` | Capture la leçon là où le prochain `ce-plan` la retrouvera |

Le point central est la **flèche de retour** : `ce-compound` écrit, et `ce-brainstorm`, `ce-plan` et `ce-code-review` relisent. Le README résume : la première exécution apprend, la deuxième se souvient. Un mode autonome (`/lfg`) enchaîne tout, jusqu'à la PR et la surveillance de la CI, sans merger sans autorisation.

### `ce-compound` : le cœur du dispositif

- **Quand capitaliser.** Une leçon n'est écrite que si elle est à la fois :
  - **non évidente** : le raisonnement ne se déduit pas du code, des tests ou de la doc ;
  - **durable** : un invariant, une cause racine, une approche rejetée, une décision ;
  - **importante** : l'oublier ferait probablement répéter l'erreur ou refaire une longue enquête.

  Test contrefactuel : sans cette trace, un futur développeur partant du code actuel referait-il l'erreur ? Si non, rien n'est écrit.
- **La forme la moins chère qui empêche la répétition**, par ordre de préférence :
  1. supprimer la cause : s'il existe deux façons de faire, n'en garder qu'une (signalé au propriétaire du code, car cela touche le code produit) ;
  2. un **test ou une règle de lint** dont l'échec dit quoi corriger, gardé seulement après l'avoir vu passer sur le code actuel ;
  3. une règle dans `CODING_STANDARDS.md` ;
  4. en dernier recours, une **fiche de leçon** dans `docs/solutions/` : symptômes, cause racine, ce qui n'a pas marché, solution, prévention.
- **Chaque connaissance a sa place** : comportement vérifiable par une machine → test ou type ; justification locale → commentaire ; historique d'un changement → commit ou PR ; vocabulaire métier → `CONCEPTS.md` ; raisonnement durable transverse → `docs/solutions/`.
- **Les pièges évités**, d'après la doc : la solution reste dans un fil de discussion ; elle est documentée mais introuvable ; deux fiches divergentes pour le même problème ; ce qui n'a *pas* marché, souvent le plus coûteux, n'est jamais écrit ; la capture attend la fin de session, quand le contexte s'est évaporé.
- **Mécanismes** : capture au moment où le contexte est le plus frais ; vérification de recouvrement (mettre à jour plutôt que dupliquer) ; vérification que le fichier d'instructions du projet pointe vers `docs/solutions/` ; une leçon par exécution.
- **Entretien du stock** (`/ce-compound refresh`) : relire les leçons au regard du code actuel, puis les mettre à jour, fusionner, remplacer ou supprimer.

### Autres mécanismes notables

- **Simplification systématique** (`ce-simplify-code`) : plusieurs relectures ciblées (réutilisation de l'existant, qualité, efficacité, valeur des tests, commentaires). Un test qui passerait même si le code était cassé est candidat à la suppression ; un commentaire est supprimé par défaut sauf exceptions listées. Comportement préservé ; appliqué automatiquement dans la boucle, en rapport préalable quand l'utilisateur l'appelle lui-même.
- **Revue par personas choisies selon le diff** (`ce-code-review`) : la correction est toujours relue, les autres angles (sécurité, tests, maintenabilité…) seulement si la surface touchée le justifie. Constats avec sévérité, niveau de confiance et propriétaire ; l'accord de plusieurs relecteurs ne rend pas important un point mineur ; une suggestion rejetée ne revient pas.
- **Critères de revue détenus par le dépôt** : ce qu'on écrit dans `CODING_STANDARDS.md` devient ce que la revue vérifie. La capitalisation alimente donc directement la revue.
- **Le projet s'applique sa propre méthode** : le dépôt contient environ 70 fiches dans `docs/solutions/`.
- **Compound Packs** (expérimental) : des règles partagées entre dépôts d'une organisation.

---

## Ce que JDLC peut en retenir

| # | Idée | Ce que ça donnerait dans JDLC | Convergence |
|---|---|---|---|
| 1 | **La flèche de retour est le cœur** : la capitalisation n'a de valeur que si la planification et la revue *relisent* les leçons | La phase Planification et la phase Revue consultent obligatoirement le stock de leçons ; la phase Retour d'expérience n'est pas une fin de cycle mais une entrée du suivant. | **Confirmation** : Compound-1/2 réinjectés dans Plan (SFEIR), skill `learn` (AIDD), principe 11 |
| 2 | **La forme la moins chère qui empêche la répétition** : supprimer la cause > test/lint > règle écrite > fiche de leçon | Règle de routage des leçons dans JDLC : préférer ce qu'une machine vérifie à ce qu'un agent doit lire. Prolonge la discipline de la preuve à la capitalisation. | **Nouveau** dans sa hiérarchie ; AIDD route vers mémoire/ADR/règle/skill, mais sans privilégier le contrôle automatique |
| 3 | **Critère d'entrée : non évident, durable, important** + test contrefactuel | Filtre avant d'écrire en mémoire, pour éviter que le contexte enfle de leçons inutiles. | **Nouveau** ; rejoint « le plus petit ensemble de tokens à fort signal » (Anthropic A2) |
| 4 | **Gabarit de leçon** : symptômes, cause racine, ce qui n'a pas marché, solution, prévention | Gabarit de fiche de leçon JDLC. Insister sur *ce qui n'a pas marché*, première information perdue. | **Nouveau** (gabarit précis) ; rejoint le « What didn't work » du HANDOFF de [YK](claude-code-tips-yk.md) |
| 5 | **Capturer quand le contexte est frais**, une leçon à la fois, en mettant à jour plutôt qu'en dupliquant | La capitalisation se déclenche à la clôture d'une tâche, pas en fin de session ; vérification de doublon avant écriture. | **Nouveau** (moment et dédoublonnage) |
| 6 | **Entretenir le stock de leçons** (refresh) | Tâche périodique de relecture des leçons et des règles contre le code actuel : mettre à jour, fusionner, supprimer. | **Confirmation** : le contexte est du code (principe 10), revue périodique de `CLAUDE.md` ([YK](claude-code-tips-yk.md)), composants comme hypothèses périssables (Anthropic A3) |
| 7 | **Une étape de simplification entre l'implémentation et la revue**, y compris l'élagage des tests sans valeur et des commentaires redondants | Étape ou skill de simplification avant la revue. Précise le « test qui passerait même si le code était cassé » comme critère d'élagage. | **Confirmation** de l'idée 1 de la [vidéo](video-senior-engineers-coding-agents.md) et du conseil de [YK](claude-code-tips-yk.md) ; le volet tests/commentaires est **nouveau** |
| 8 | **Revue par angles choisis selon le diff**, constats avec confiance et sévérité, rapport sans modification par défaut | Les sous-agents de revue ne sont lancés que si la surface touchée le justifie : la revue multi-angles reste proportionnée. | **Confirmation** de la revue multi-angles (SFEIR) et du plan proportionné (AIDD) ; la sélection par diff est **nouvelle** |
| 9 | **Les règles écrites par la capitalisation deviennent les critères de la revue** (`CODING_STANDARDS.md`) | Un seul fichier de règles du projet cible, lu par la planification et vérifié par la revue. | **Nouveau** comme boucle explicite capitalisation → revue |
| 10 | **Plan unique qui grossit** : le même document passe des exigences au plan d'implémentation | Éviter la multiplication d'artefacts pour un usage solo : un document par changement, enrichi de phase en phase. | Partiellement **nouveau** ; rejoint le « contrat » d'AIDD |
| 11 | **80 % planification et revue, 20 % exécution** | Argument pédagogique pour la place des phases amont et de la revue. | **Confirmation** : « spécifier et vérifier » (SFEIR), rôle produit de l'ingénieur ([vidéo](video-senior-engineers-coding-agents.md)) |
| 12 | **Le projet s'applique sa méthode** (`docs/solutions/` dans le dépôt du plugin) | JDLC capitalise ses propres leçons avec ses propres artefacts. | **Confirmation** : idée 12 d'[AIDD](aidd.md) |

En résumé : la boucle elle-même confirme ce que SFEIR et AIDD avaient déjà apporté. L'apport propre du compound engineering est **l'ingénierie de la capitalisation** : quand écrire (idée 3), sous quelle forme (idée 2), avec quel gabarit (idée 4), à quel moment (idée 5) et comment entretenir (idée 6).

## Ce qu'on écarte ou relativise

- **Le multi-outils** (14 hôtes, convertisseurs) : hors périmètre, JDLC cible uniquement Claude Code ([ADR-0006](../adr/0006-claude-code-uniquement.md)).
- **L'échelle** (37 skills) et les **Compound Packs** partagés à l'échelle d'une organisation : JDLC démarre petit et solo.
- **Le mode autonome `/lfg`** (jusqu'au push, à la PR et aux réparations de CI) : intéressant, mais pour JDLC l'humain garde la main aux étapes clés (principe 6). À reconsidérer à l'usage.
- **Les affirmations de productivité** (un développeur vaut cinq, délais divisés) : non étayées, et l'essai E2 est aussi une vitrine pour Every. On ne s'en sert pas comme arguments.
- **« Écrire les tests à la main devient superflu »** : à lire comme « l'agent écrit les tests », pas comme « moins de tests ». La discipline de la preuve de JDLC (principe 8) s'appuie justement sur eux.

## Sources secondaires

- La page de présentation https://every.to/compound-engineering (sans date ni auteur) reprend le contenu du README du plugin.
- La documentation du plugin (`docs/guides/`) contient une page par skill : c'est la référence pour une éventuelle adaptation, possible grâce à la licence MIT en suivant la procédure de [licences.md](../licences.md).
