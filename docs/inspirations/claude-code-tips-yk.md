# YK Sugi — « 45+ Claude Code Tips: From Basics to Advanced »

Dépôt GitHub de conseils numérotés sur Claude Code, tenu par YK Sugi (« YK », compte `ykdojo`). C'est le dépôt cité dans la [vidéo « Senior Engineer's best practices for using coding agents »](video-senior-engineers-coding-agents.md). Il contient aussi un plugin Claude Code, `dx`, qui regroupe quelques skills tirés des conseils.

| | |
|---|---|
| **Source** | https://github.com/ykdojo/claude-code-tips |
| **Auteur** | YK Sugi (`ykdojo`) |
| **Version étudiée** | commit `577ac89252195d61283b334c4b9d292b52c82203` (2026-09-25) : conseils numérotés de 0 à 49 |
| **Consulté le** | 2026-11-11 |
| **Licence** | **Aucune licence libre** : le fichier `LICENSE` lu au commit étudié indique « Copyright (c) YK Sugi. All Rights Reserved. » (il précise seulement que les contributeurs cèdent une licence sur leurs contributions à l'auteur) |
| **Niveau de reprise possible** | **Idée uniquement** : ni texte, ni skill, ni script copiés ou adaptés ([politique](../licences.md)) |
| **Niveau actuel** | Idée |

### Identification du dépôt

L'identification est faite avec une bonne confiance, sur un faisceau d'indices :

- l'auteur se présente comme « YK », compte GitHub `ykdojo` (fichier `GLOBAL-CLAUDE.md` du dépôt) ;
- le dépôt est une liste numérotée de plus de 45 conseils sur Claude Code ;
- le conseil « Simplify overcomplicated code » existe. Il est **n° 40** à la version étudiée, mais l'historique git montre qu'il a porté le **n° 38** au moins entre le 2026-06-10 et le 2026-06-21 (numérotation décalée au fil des ajouts). Cela correspond au « conseil n° 38 » cité dans la vidéo et suggère, sans le prouver, que la vidéo date d'environ juin 2026.

La page YouTube n'a pas pu être lue en détail (chaîne et description non accessibles) : le lien vidéo ↔ dépôt repose donc sur ces indices, pas sur un lien explicite.

## Synthèse

Le README est un recueil personnel et pragmatique, écrit à la première personne, mêlant astuces d'ergonomie et conseils de méthode. On le regroupe ici par thème (numéros de la version étudiée).

### Gérer le contexte

- **Le contexte se périme** (n° 5) : l'agent est meilleur en début de conversation ; on repart à neuf à chaque nouveau sujet ou dès que la qualité baisse. L'auteur compare le contexte à du lait, meilleur frais.
- **Compacter soi-même, par un document de passation** (n° 8) : plutôt que la compaction automatique, on demande à l'agent un `HANDOFF.md` (objectif, avancement, ce qui a marché, ce qui n'a pas marché, prochaines étapes), on le relit, puis on démarre une session neuve avec ce seul fichier. Un skill `handoff` l'automatise.
- **Bifurquer ou tronquer une conversation** (n° 21) pour essayer une autre piste ou alléger le contexte ; un hook peut le suggérer.
- **Voir sa consommation de contexte** en permanence via une barre d'état (n° 0), et préférer des réponses concises (n° 49).

### Découper, planifier, choisir son niveau d'abstraction

- **Découper les gros problèmes** (n° 3) jusqu'à ce que chaque morceau soit soluble par l'agent ; c'est la compétence d'ingénierie classique, toujours valable.
- **Planifier, mais prototyper vite** (n° 39) : prendre tôt les décisions structurantes (technologie, découpage, emplacement des fonctions), et utiliser un prototype jetable pour les trancher.
- **Choisir son niveau d'abstraction** (n° 30, 35) : le *vibe coding* convient pour du jetable ou du non critique ; ailleurs on descend dans la structure, les fonctions, les lignes, les dépendances. Ce n'est pas binaire, et l'humain règle la profondeur et le rythme.

### Vérifier

- **Boucler le cycle écrire-tester** (n° 9) : pour qu'un agent travaille seul (par exemple un `git bisect`), il lui faut un moyen de vérifier chaque résultat, quitte à être créatif (tmux pour une interface terminal, Playwright pour une application web).
- **TDD, avec les tests commités avant le code** (n° 34) : écrire les tests, constater qu'ils échouent, les commiter, puis implémenter. Les tests commités forment un contrat. Relire les tests pour s'assurer qu'ils ne sont pas triviaux.
- **Faire vérifier ses affirmations à l'agent** (n° 26) : lui demander de revérifier chaque affirmation et de terminer par un tableau de ce qu'il a pu vérifier ; relire les changements dans un client git visuel ; passer par des PR en brouillon.
- **Relire un `TESTING.md` plutôt que le code** (n° 48) : pour un gros volume de code, l'agent teste tout et documente comment il a testé ; l'humain relit ce document, qu'on ajuste jusqu'à ce qu'il soit lisible. Tenu dans git, il devient un journal de ce qui a été vérifié.
- **Simplifier le code trop compliqué** (n° 40) : l'agent a un biais pour écrire trop de code et faire des changements non demandés. On lui demande de simplifier et de justifier chaque ajout. Comprendre tout le code reste possible, à condition de poser assez de questions. Le même travers touche la prose (résumés redondants en fin de paragraphe).
- **Revue de PR interactive** (n° 24) : une conversation au rythme de l'humain plutôt qu'une revue en un coup.

### Git et prudence

- **Laisser l'agent gérer git, avec des garde-fous** (n° 4) : `pull` autorisé automatiquement, `push` non ; PR en brouillon pour relire avant publication. Le `GLOBAL-CLAUDE.md` de l'auteur demande de séparer commit et push.
- **Worktrees pour le travail en parallèle** (n° 14).
- **Autonomie dans un environnement isolé** (n° 19) : conteneur ou machine dédiée pour les tâches longues et risquées sans demande de permission ; le mode auto (n° 31) réduit l'approbation machinale mais ne supprime pas le risque.
- **Sous-agents en arrière-plan ou en parallèle** (n° 36) pour analyser un gros code sous plusieurs angles.

### Entretenir son environnement

- **Investir dans son propre outillage** (n° 11) : au minimum, un `CLAUDE.md` aussi concis que possible.
- **`CLAUDE.md` simple, enrichi seulement quand on se répète, et revu périodiquement** (n° 28) : on peut partir de rien et ajouter une consigne quand on la redit souvent. Un skill `review-claudemd` analyse l'historique des conversations récentes (via des sous-agents) pour proposer des améliorations des `CLAUDE.md` global et projet.
- **Comprendre `CLAUDE.md`, skills, commandes et plugins** (n° 23) : `CLAUDE.md` est toujours chargé ; un skill ne l'est qu'à la demande, donc plus économe ; un plugin empaquette le tout pour l'installer facilement.
- **L'automatisation de l'automatisation** (n° 41) : ce qu'on répète passe successivement dans `CLAUDE.md`, puis dans un skill, puis dans un script.
- **Installation transparente** (n° 44, 45) : le dépôt est une marketplace avec le plugin `dx` ; un script d'installation liste tout ce qu'il va configurer et laisse sauter chaque élément.

### Posture

- On progresse en pratiquant (n° 20) ; les compétences d'ingénieur restent décisives (n° 3, 9) ; on peut s'aventurer dans l'inconnu en itérant avec l'agent (n° 35) ; partager et contribuer en retour (n° 42) ; suivre les notes de version (n° 43).

### Ergonomie (non détaillée ici)

Saisie vocale, alias de terminal, raccourcis clavier, copier-coller, recherche dans l'historique, multi-comptes, artifacts, pilotage depuis un téléphone, GitHub comme base de connaissances (n° 2, 6, 7, 10, 12, 13, 15 à 18, 22, 25, 27, 29, 32, 33, 37, 38, 46, 47). Utile au quotidien, peu structurant pour un SDLC.

## Ce que JDLC peut en retenir

| # | Idée | Ce que ça donnerait dans JDLC | Convergence |
|---|---|---|---|
| 1 | **Document de passation** (objectif, avancement, ce qui a marché, ce qui n'a pas marché, prochaines étapes) pour repartir d'un contexte neuf | Artefact standard de JDLC entre deux sessions ou deux phases longues, relu par l'humain avant de repartir. | **Confirmation** : prise de notes structurée (Anthropic A2), reconstruire le contexte (manifeste AIDD) ; le gabarit concret est **nouveau** |
| 2 | **Faire émerger les règles de l'historique des conversations** (`review-claudemd`) | Tâche périodique : analyser les sessions récentes pour repérer les consignes répétées ou les corrections, et proposer des mises à jour de `CLAUDE.md` et des règles, validées par l'humain. | **Nouveau** comme source de capitalisation ; rejoint « vu deux fois → règle » (SFEIR) et l'entretien du stock ([compound engineering](compound-engineering.md)) |
| 3 | **`CLAUDE.md` minimal, enrichi seulement sur répétition** | Règle d'hygiène : on n'ajoute une consigne qu'après l'avoir constatée nécessaire. | **Confirmation** : principe 1 (empirique), instructions ajoutées à partir des échecs (Anthropic A2), contexte maigre (compound engineering) |
| 4 | **Tests commités avant l'implémentation** | Dans la phase Implémentation, un commit de tests qui échouent précède le code : le contrat est figé et visible dans l'historique. | **Confirmation** : contrats de sprint (Anthropic A3), discipline de la preuve ; le commit préalable est un détail **nouveau** et simple à vérifier |
| 5 | **`TESTING.md` : relire la preuve plutôt que le code** | Forme concrète de la discipline de la preuve : l'agent livre un compte rendu de ce qu'il a testé et comment ; c'est ce que l'humain valide. | **Nouveau** comme artefact ; prolonge le principe 8 et « contrôler les résultats, pas chaque ligne » ([vidéo](video-senior-engineers-coding-agents.md)) |
| 6 | **Tableau de vérification des affirmations** | Pour les livrables non codés (analyses, recherches, notes comme celle-ci), l'agent termine par la liste de ce qu'il a vérifié et de ce qu'il n'a pas pu vérifier. | **Nouveau** ; étend la discipline de la preuve au-delà du code |
| 7 | **Simplifier et faire justifier chaque ajout** | Angle simplification dans la revue, avec la question « pourquoi cette ligne ? ». | **Confirmation** : [vidéo](video-senior-engineers-coding-agents.md), étape Simplify du [compound engineering](compound-engineering.md) |
| 8 | **Profondeur de revue selon la criticité** | La validation humaine n'a pas la même profondeur pour un script jetable et pour du code critique ; JDLC peut le rendre explicite. | **Confirmation** : plan proportionné (AIDD), « ne pas déléguer ce qu'on ne sait pas évaluer » (principe 9) |
| 9 | **Garde-fous git : pull libre, push validé, PR en brouillon** | Défauts de permissions proposés par JDLC : les actions irréversibles ou publiques restent une porte humaine. | **Confirmation** : permissions par paliers (Anthropic A4), principe 6 |
| 10 | **Échelle d'automatisation** : répété → `CLAUDE.md` → skill → script | Grille pour décider où ranger une pratique récurrente. | **Confirmation** ; même logique que « la forme la moins chère » du [compound engineering](compound-engineering.md), vue côté outillage |
| 11 | **Installation qui montre tout et laisse choisir** | La future commande `init` de JDLC liste ce qu'elle va écrire ou configurer et permet d'exclure chaque élément. | **Confirmation** : « Trust and safety » (AIDD), [ADR-0005](../adr/0005-mecanisme-installation.md) ; la sélection élément par élément est **nouvelle** |
| 12 | **Planifier, mais prototyper pour trancher** | Dans la phase Conception, un prototype jetable est un moyen légitime de lever une incertitude technique. | **Confirmation** : domaine d'application de la vision (« on prototype d'abord ») |

Les idées vraiment nouvelles sont la 2 (capitalisation à partir de l'historique), la 5 (`TESTING.md`) et la 6 (tableau de vérification). Les autres confirment des pistes déjà relevées.

## Ce qu'on écarte ou relativise

- **L'ergonomie personnelle** (voix, alias, raccourcis, multi-comptes, macOS, téléphone) : utile, mais relève de la configuration de chacun, pas d'un SDLC.
- **L'orchestration multi-modèles en conteneurs** (Codex et autres CLI) : hors périmètre, JDLC cible uniquement Claude Code ([ADR-0006](../adr/0006-claude-code-uniquement.md)).
- **L'autonomie complète** (`--dangerously-skip-permissions` en conteneur) : à documenter éventuellement comme option, pas comme défaut.
- **Le mode auto comme défaut** : choix personnel de l'auteur ; JDLC n'impose pas de mode de permission.
- **La « règle du milliard de tokens »** (progresser en consommant beaucoup) : posture d'apprentissage, sans conséquence sur la conception de JDLC.
- **Le dépôt évolue vite** (numérotation changeante, environ 285 commits sur le seul README) : toute référence future doit citer le commit, pas le numéro du conseil.

## Sources secondaires

- Le dépôt contient des articles longs (`content/`), dont un sur le « spectre du codage agentique » et un sur GitHub comme base de connaissances, non étudiés ici.
- L'auteur cite une autre conférence (« Claude Code Masterclass ») et une lettre d'information, non étudiées.
