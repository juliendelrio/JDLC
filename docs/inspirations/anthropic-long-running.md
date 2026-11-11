# Anthropic : travail autonome de longue durée (4 dépôts)

Quatre dépôts publics d'Anthropic qui montrent comment un agent travaille seul pendant des heures, sur plusieurs sessions. Les deux premiers viennent de la conférence *Code with Claude* 2026 (« cwc »).

| | Harnais longue durée | Ateliers CwC | Compilateur C de Claude | Quickstart « autonomous coding » |
|---|---|---|---|---|
| **Source** | [anthropics/cwc-long-running-agents](https://github.com/anthropics/cwc-long-running-agents) | [anthropics/cwc-workshops](https://github.com/anthropics/cwc-workshops) | [anthropics/claudes-c-compiler](https://github.com/anthropics/claudes-c-compiler) | [anthropics/claude-quickstarts](https://github.com/anthropics/claude-quickstarts), dossier `autonomous-coding/` seulement |
| **Auteur** | Anthropic PBC | Anthropic PBC | Anthropic (code et doc écrits par Claude Opus 4.6) | Anthropic |
| **Version étudiée** | Commit `ad107a9` (2026-05-12) | Commit `2c65d16` (2026-11-04) | Commit `6f1b99a` (2026-02-05) | Commit `4a4224b` (2026-11-06) |
| **Consulté le** | 2026-11-11 | 2026-11-11 | 2026-11-11 | 2026-11-11 |
| **Licence (vérifiée au commit)** | **Apache-2.0** (`LICENSE`), en-têtes « Copyright 2026 Anthropic PBC » et SPDX dans chaque fichier. Pas de `NOTICE`. | **Apache-2.0** (`LICENSE` racine, plus une copie dans `agent-battle/`). En-têtes SPDX dans la plupart des fichiers. Pas de `NOTICE`. | **CC0 1.0** (`LICENSE`) : domaine public. | **MIT** à la racine (« Copyright (c) 2023 Anthropic »). **Mais** le README du dossier dit « License: Internal Anthropic use ». Contradiction non résolue. |
| **Niveau de reprise possible** | Idée, adaptation ou import | Idée, adaptation ou import | Idée, adaptation ou import, sans condition | Idée seulement, tant que la contradiction n'est pas levée |
| **Niveau actuel** | **Idée** | **Idée** | **Idée** | **Idée** |
| **Statut** | Démo d'événement, non maintenue | Matériel d'atelier, non maintenu | Expérience, « ne pas utiliser » | Démo |

Article lié, cité par le premier dépôt mais **non lu** : *Effective Harnesses for Long-Running Agents* (Anthropic, novembre 2025). Il décrit le schéma « initialiseur + agent de code » du quickstart. Il manque à la liste de [anthropic.md](anthropic.md). L'article sur le compilateur (*Building a C compiler*, février 2026) n'a pas été lu non plus.

## De quoi il s'agit

**cwc-long-running-agents.** Le « kit à emporter » d'un atelier sur les agents de longue durée. Ce n'est pas un harnais complet : ce sont des briques. Un `.claude/` à copier dans un projet, avec un `CLAUDE.md` de conventions, cinq hooks et un sous-agent évaluateur. Trois briques forment la « boucle de qualité » :

- un **contrat en échec par défaut** : chaque critère part à `false`, et un hook refuse de le passer à `true` tant que l'agent n'a pas ouvert une preuve ;
- un **évaluateur à contexte neuf** : un sous-agent sans droit d'écriture, qui n'a pas vu la construction, rend `PASS` ou `NEEDS_WORK` ;
- une **passation tenue par l'agent** : un `PROGRESS.md` relu à chaque démarrage, et des commits fréquents.

Deux hooks de contrôle s'y ajoutent : un arrêt d'urgence et un canal pour réorienter l'agent en cours de route. Le README compare ce harnais fait main à la commande intégrée `/goal` de Claude Code, et recommande d'essayer les deux.

**cwc-workshops.** Dix ateliers. La plupart portent sur *Claude Managed Agents* (agents hébergés par Anthropic), hors périmètre. Trois choses concernent le développement avec Claude Code :

- `how-we-claude-code/` : un flux en trois phases. Un entretien pour écrire la spec, quatre designs divergents en HTML statique, puis une application React **conçue pour être vérifiée par un agent à l'exécution** ;
- les `CLAUDE.md` et skills d'atelier (`ship-your-first-managed-agent/`, `research-desk/`, `agent-decomposition/`) : ils règlent finement **quand l'agent s'arrête** et comment il rend compte ;
- `rightmodel/` : un skill qui audite une suite d'évaluation avant de s'en servir pour choisir un modèle.

**claudes-c-compiler.** Un compilateur C en Rust écrit entièrement par Claude. On n'a pas lu le code. Le harnais lui-même (boucle, conteneurs) n'est pas dans le dépôt. Il reste des **traces du processus** :

- `current_tasks/` : un fichier texte par tâche en cours (« verrou ») ;
- `ideas/` : un carnet d'idées, avec la priorité dans le nom de fichier ;
- `projects/` : des chantiers de nettoyage chiffrés ;
- `ideas/docs_verified_*.txt` : un journal d'audit de la documentation contre le code ;
- `ideas/new_projects.txt` : l'état PASS/FAIL de vrais projets compilés, par architecture.

Le README précise le rôle humain : écrire les tests à faire passer, sans jamais programmer en binôme avec Claude.

**claude-quickstarts / autonomous-coding.** Un harnais minimal en Python (Agent SDK) qui construit une application sur de nombreuses sessions. Deux prompts :

- **initialiseur** (session 1) : lit la spec, écrit `feature_list.json` (200 tests de bout en bout, tous à `"passes": false`), un script `init.sh`, initialise git ;
- **agent de code** (sessions suivantes) : un rituel en dix étapes, une fonctionnalité par session, vérification dans un vrai navigateur.

Une boucle Python relance une session neuve tant qu'il reste du travail. La sécurité repose sur un sandbox, une restriction au dossier du projet et une liste blanche de commandes Bash.

## Comparaison avec Anthropic A3

On peut lire ces dépôts comme les étapes d'une même ligne, déjà résumée en partie dans [anthropic.md](anthropic.md) (A3) :

| Date | Étape | Ce qu'elle ajoute |
|---|---|---|
| Nov. 2025 | Quickstart + article *Effective harnesses* | Initialiseur et agent de code. Liste de fonctionnalités, fichier de progression, git comme mémoire, vérification en navigateur. |
| Fév. 2026 | Compilateur C | Plusieurs agents en parallèle sur un même dépôt, coordination par fichiers-verrous, oracle = suites de tests réelles. |
| Mars 2026 | A3 (*Harness design*) | Planificateur, générateur, **évaluateur séparé**, contrats de sprint. Puis retrait des sprints quand le modèle progresse. |
| Mai 2026 | cwc-long-running-agents | Réduction à trois briques, chacune dans un fichier, appliquées par hooks. Renvoi vers `/goal`, intégré au produit. |

Deux leçons pour JDLC :

- **Le harnais est absorbé par l'outil.** Ce qui demandait un script Python en 2025 est une commande de Claude Code en 2026. JDLC doit d'abord s'appuyer sur `/goal`, les hooks et les sous-agents natifs (ADR-0006), et ne coder lui-même que l'écart.
- **La passation est la couche la plus sensible au modèle.** Le dépôt le dit : les modèles récents dérivent moins et se cadrent mieux, donc on revoit la taille du `CLAUDE.md` à chaque nouveau modèle. C'est l'idée d'A3 (« chaque composant encode une hypothèse »), appliquée ici à un fichier précis.

## Ce que JDLC peut en retenir

### Organiser le travail sur plusieurs sessions

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 1 | **Deux rôles de session : initialiseur et travailleur.** La première session ne code presque pas. Elle prépare le terrain : liste de fonctionnalités, script d'environnement, structure, premier commit. Les suivantes travaillent sur ce terrain. | La phase Planification de JDLC produit les artefacts de reprise (liste, état, script). L'Implémentation ne commence qu'ensuite, dans des sessions neuves. | Confirmation partielle : A3 (planificateur). **Nouvelle** pour la séparation nette en deux prompts |
| 2 | **Un script d'environnement (`init.sh`)** écrit par l'initialiseur et lancé au début de chaque session : dépendances, serveurs, URL d'accès. | Le skill d'initialisation de JDLC vérifie ou crée une commande unique « démarrer l'environnement » dans le projet cible. | **Nouvelle** |
| 3 | **Un rituel de démarrage fixe.** Où suis-je, lire la spec, la liste, la progression, `git log`, compter ce qui reste, lancer l'environnement. Puis lancer le test de fumée pour savoir qu'on part d'un arbre qui marche. | Section « Toujours commencer ici » dans le `CLAUDE.md` installé par JDLC. | Confirmation : A2, kit #2, passation YK. Forme concrète **nouvelle** |
| 4 | **Vérifier les acquis avant d'ajouter.** Avant tout travail neuf, rejouer une ou deux fonctionnalités déjà validées, les plus centrales. Si l'une est cassée, elle repasse à `false` et on la répare d'abord. | Règle d'Implémentation : une session commence par un test de non-régression sur l'existant, pas par la tâche suivante. | **Nouvelle** |
| 5 | **Une fonctionnalité par session.** Si l'humain demande autre chose en cours de route, on l'ajoute à la liste et on finit d'abord la tâche en cours. | Règle d'Implémentation par tranches. La demande imprévue entre dans le plan, elle ne l'interrompt pas. | Confirmation : A3, SFEIR. La règle de la demande imprévue est **nouvelle** |
| 6 | **Un `PROGRESS.md` en quatre sections** : *Done*, *In progress*, *Next*, *Notes*. Mis à jour après chaque élément. Écrit pour être lu « à froid ». | Gabarit du fichier d'état JDLC. | Confirmation : A2, kit #2. Gabarit **nouveau** |
| 7 | **Git comme seconde mémoire.** Commits fréquents avec messages descriptifs. Un hook `Stop` fait un commit de secours des fichiers suivis en fin de session. Il ignore volontairement les fichiers non suivis (captures, journaux). | Hook de filet de sécurité dans le plugin JDLC, à côté de la consigne « commiter aux points de passage ». À peser contre la préférence « commit seulement sur demande ». | Confirmation : A3 (git). Le hook de secours est **nouveau** |
| 8 | **Finir proprement.** Avant que le contexte ne sature : tout commiter, mettre l'état à jour, ne rien laisser cassé. | Critère de fin de session JDLC, vérifiable : arbre propre, état à jour, tests verts. | Confirmation : passation YK, Erik #7 |

### Faire de « terminé » une structure, pas une déclaration

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 9 | **La liste de fonctionnalités comme oracle figé.** Chaque entrée est un test de bout en bout avec ses étapes. Toutes partent à `false`. L'agent ne peut changer que le champ `passes` : jamais supprimer, reformuler, fusionner ou réordonner. | Format de la liste d'acceptation JDLC : fichier structuré (JSON plutôt que prose), validé par l'humain, puis **verrouillé** sauf le statut. | Confirmation : kit #9 (l'humain fixe l'oracle). Format concret **nouveau** |
| 10 | **Contrat en échec par défaut, appliqué par hook.** Un hook `PreToolUse` refuse toute écriture dans le fichier de résultats tant que l'agent n'a pas lu une preuve (capture, journal). La preuve est « consommée » : la modification suivante en demande une nouvelle. | Candidat direct pour un hook JDLC : pas de case « terminé » sans sortie réelle lue juste avant. | **Nouvelle** comme mécanisme. Applique le principe 8 et « consignes ou garanties » |
| 11 | **Dire les trous du garde-fou.** Le hook avoue ses limites : Bash peut réécrire le fichier, n'importe quelle preuve débloque n'importe quelle ligne. « Exemple pédagogique, pas une frontière de sécurité. » | Chaque hook JDLC documente ce qu'il ne bloque pas. | Confirmation : kit #13 et #14 |
| 12 | **Un évaluateur à contexte neuf, sans écriture.** Il lit la spec, le diff, ouvre chaque preuve et regarde ce qu'elle montre vraiment. « La plausibilité n'est pas la justesse. » Une preuve qui ne s'ouvre pas compte comme absente. | Le vérificateur indépendant de JDLC : outils en lecture seule, consigne de scepticisme, preuve manquante = échec. | Confirmation : A3, kit #16 et #26 |
| 13 | **Un verdict lisible par un script.** Le premier mot est `PASS` ou `NEEDS_WORK`, seul sur sa ligne. En cas d'échec, une liste de constats corrigeables, qui devient le prompt de la session suivante (`NEXT_FINDINGS.md`). | Format de sortie du vérificateur JDLC, et boucle « constats → session suivante ». | Confirmation : kit #18. Format **nouveau** |
| 14 | **Seul le harnais écrit le résultat validé.** Variante proposée : c'est le script, et non le constructeur, qui passe le contrat à `true` sur un `PASS`. « Tout vrai » veut alors dire « confirmé de façon indépendante ». | Option pour JDLC : le statut final d'une tâche est écrit par la vérification, jamais par l'agent qui a codé. | **Nouvelle** |
| 15 | **Tester comme un utilisateur.** Vérifier par l'interface réelle, avec des captures à chaque étape. Pas seulement `curl`, pas de JavaScript pour contourner l'interface. Regarder aussi le visuel et la console. | Consigne de vérification pour les projets web. | Confirmation : A3, Erik #13 |

### Concevoir pour la vérification

Source : `cwc-workshops/how-we-claude-code/phase-3-verify`.

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 16 | **Un contrat lisible par une machine à la surface.** Chaque composant expose son état dans des attributs (`data-verify-*`). Les vérifications lisent ce contrat, pas les détails internes. On peut tout réécrire tant que le contrat tient. | Exigence de Conception, pour les projets d'interface : l'état observable est un contrat. | Confirmation : Erik #3 (concevoir pour la vérifiabilité), Erik #4 |
| 17 | **Quatre verdicts, et `BLOCKED` n'est pas `FAIL`.** `PASS`, `FAIL`, `BLOCKED` (impossible d'observer), `SKIP`. Dans le doute, on échoue : un faux succès livre un bug, un faux échec coûte un coup d'œil. | Vocabulaire commun aux vérifications JDLC. « Je n'ai pas pu vérifier » est un résultat à part, jamais un succès. | **Nouvelle** |
| 18 | **Au moins une « sonde » adverse par unité.** Une unité testée seulement sur le cas nominal est refusée par la suite de tests. Une sonde est même conçue pour échouer, pour prouver que le système détecte un mensonge. | Règle de la phase Tests : chaque unité a au moins un cas limite. Un test-témoin qui doit échouer prouve que la vérification mord. | **Nouvelle**. Rejoint ADLC (*hollow-test*) |
| 19 | **Un seul chemin de vérification, trois lecteurs.** Le tableau de bord humain, l'agent et la CI appellent le même code. « L'agent et l'humain voient la même vérité. » | Une seule commande de vérification par projet cible, utilisée par l'humain, l'agent et la CI. | Confirmation : documentation Claude Code (commande de vérification propre au projet) |

### Garder la main sur un agent autonome

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 20 | **Arrêt d'urgence par fichier.** Tant qu'un fichier `AGENT_STOP` existe, un hook bloque tous les appels d'outils. On le supprime pour reprendre. | Hook optionnel du plugin JDLC pour les exécutions longues. | **Nouvelle** |
| 21 | **Réorienter sans redémarrer.** L'humain écrit dans `STEER.md`. Un hook transmet le contenu une fois à l'agent, avec priorité sur son plan, puis vide le fichier. C'est « un canal de commodité, pas une frontière de confiance ». | Canal de pilotage asynchrone pour les sessions longues. | **Nouvelle** |
| 22 | **Observer sans tableau de bord.** Tout ce que fait l'agent est sur disque : `tail PROGRESS.md`, `git log`, les captures. | Documentation JDLC : comment suivre une exécution longue avec des commandes ordinaires. | **Nouvelle**, petite |
| 23 | **Trois conditions de sortie d'une boucle.** Plus rien en échec, un cycle sans changement, ou un budget atteint. Plus un arrêt manuel. | Toute boucle autonome JDLC déclare ses conditions de sortie avant de démarrer. | Confirmation : kit #19 (limites dans le harnais). La condition « cycle sans changement » est **nouvelle** |
| 24 | **Commencer par l'intégré.** `/goal` fournit déjà une boucle générateur / vérificateur. Le harnais fait main ne se justifie que pour un évaluateur sur mesure ou un contrat appliqué par hook. | JDLC préfère `/goal` et ne code un harnais que pour l'écart démontré. | Confirmation : documentation Claude Code (`/goal`), ADR-0006 |
| 25 | **Défense en profondeur pour l'autonomie.** Sandbox du système, fichiers limités au projet, liste blanche de commandes Bash vérifiée par un hook. Validation fine des commandes risquées : `pkill` seulement pour les serveurs de dev, `chmod` seulement `+x`, seulement `./init.sh`. | Profil de permissions « exécution longue » fourni par JDLC. | Confirmation : A4, documentation Claude Code #10. La validation par argument est **nouvelle** |

### Régler les arrêts et le compte rendu (ateliers)

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 26 | **Une partie par demande, puis stop.** Même si la suivante a l'air facile. Seule exception : une demande explicite du type « fais tout ». L'agent lance lui-même la vérification jusqu'à `PASS`, s'arrête, puis dit quoi taper ensuite. | Règle d'arrêt des skills JDLC à étapes. | Confirmation : kit #5 |
| 27 | **Une table « ils disent quelque chose comme → tu fais ».** « Repère l'intention, pas les mots. » « Suivant » veut dire la plus petite partie non faite. | Section de routage dans les skills JDLC multi-phases, et une commande « suivant » qui se déduit de l'état. | **Nouvelle** |
| 28 | **Deux modes, proposés une fois.** « Guide-moi » (l'humain code, l'agent donne des indices) ou « fais et explique ». Le choix est mémorisé dans un fichier d'état. | Option d'un mode apprentissage dans JDLC, cohérent avec son but pédagogique et le principe 9 (ne pas déléguer ce qu'on ne sait pas évaluer). | **Nouvelle** |
| 29 | **Un compte rendu en cinq temps après chaque étape** : ce qui a changé, pourquoi ça marche, le concept en jeu, où le voir, une piste à essayer. | Variante pédagogique du format de point d'étape (kit #4). | **Nouvelle** variante |
| 30 | **Ne pas ouvrir le corrigé.** Les ateliers interdisent de lire `solutions/` ou l'historique git pour copier une réponse. | Liste de lecture interdite pour le vérificateur et pour les tâches avec attendu caché. | Confirmation : kit #26 |
| 31 | **Expliquer puis faire, sans faire barrage.** Une explication courte avant la modification, puis la modification. Une seule question de compréhension, pas un interrogatoire. | Ton des skills JDLC en mode apprentissage. | **Nouvelle**, mineure |
| 32 | **Lever les ambiguïtés par entretien, puis diverger.** Phase 1 : interviewer l'humain (outil `AskUserQuestion`) pour écrire la spec. Phase 2 : explorer quatre designs différents en HTML statique, à comparer côte à côte. | Cadrage par entretien ; en Conception, plusieurs options produites avant d'en choisir une. | Confirmation : AWS AI-DLC, ADLC #6. Les designs divergents sont **nouveaux** |
| 33 | **Auditer l'éval avant de s'y fier.** Le skill `rightmodel` vérifie d'abord la suite d'évaluation, car « une mesure sur une éval cassée donne des chiffres trompeurs ». | Avant de juger un changement de JDLC sur une éval, on vérifie l'éval. | Confirmation : kit #7 |

### Plusieurs agents sur un même dépôt (compilateur C)

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 34 | **Un fichier par tâche prise.** `current_tasks/` contient un court fichier par tâche en cours : quoi, état par cible, date de début, parfois un plan. Ces fichiers semblent servir de verrous pour que deux agents ne prennent pas la même tâche (mécanisme décrit dans l'article, non lu). | Si JDLC fait travailler plusieurs agents en parallèle : un dossier de tâches prises, versionné. | **Nouvelle** |
| 35 | **Un carnet d'idées hors du code.** `ideas/` range une fiche par idée, avec la priorité dans le nom (`high_`, `low_`). Chaque fiche dit le problème, l'état actuel avec des références fichier et ligne, et quoi faire. | Gabarit de fiche « idée / dette » dans le projet cible, alimentant la Planification et la Dépréciation. | **Nouvelle** pour le format |
| 36 | **Des chantiers de qualité chiffrés.** `projects/cleanup_code_quality.txt` liste « ce qu'un expert signalerait », avec `DONE` / `PARTIAL` et des chiffres avant et après. | Revue de dette JDLC sous forme de liste mesurable. | **Nouvelle** |
| 37 | **La doc écrite par l'agent ment, on l'audite.** Un journal trace l'audit des README contre le code et chaque fausse affirmation corrigée. Le README général prévient : « la doc peut contenir de fausses affirmations ». | Tâche périodique JDLC : vérifier la doc contre le code et tracer les corrections. | Confirmation : ADLC #11 (caches à invalider), principe 10 |
| 38 | **L'humain écrit l'oracle, l'agent le satisfait.** Le rôle humain se limite à écrire les tests à passer. L'oracle est massif et externe : de vraies bases de code et leurs suites de tests. | Confirme que l'investissement humain le plus rentable pour une tâche longue est l'oracle. | Confirmation : kit #9, principe 9 |

## Ce qu'on écarte ou relativise

- **L'autonomie sans humain pendant des heures** (« tu as un temps illimité », boucle qui relance seule). Elle contredit le principe 6 de JDLC. On garde les mécanismes (état, preuve, arrêt d'urgence) pour des exécutions longues **bornées**, pas le mode par défaut.
- **200 fonctionnalités dès la première session.** C'est un réglage de démo. JDLC vise une liste proportionnée au besoin.
- **Le ton des prompts du quickstart** (majuscules, « IT IS CATASTROPHIC… »). Il contredit la consigne du kit (#21) contre ces procédés écrits pour d'anciens modèles. On garde la règle, pas la forme.
- **Le verrou de preuve au niveau de la session** : n'importe quelle preuve débloque n'importe quelle ligne. À durcir (une preuve par test) avant d'en faire une garantie.
- **Le commit automatique en fin de session** peut heurter la règle « commit seulement sur demande ». À proposer en option, pas imposé.
- **Le quickstart est daté** : ancien paquet `claude_code_sdk`, modèle Sonnet 4.5, outils Puppeteer. On retient le schéma, pas l'outillage.
- **Le compilateur C** ne livre pas son harnais. On n'en voit que les traces. Les déductions sur la coordination restent à confirmer par l'article.
- **Le parallélisme multi-agents** est hors du premier périmètre de JDLC (solo, incrémental). Les idées 34 à 36 restent utiles en solo, comme simples fichiers de suivi.

## Dépôts sans intérêt pour JDLC

Aucun des quatre dépôts n'est sans intérêt. En revanche, dans les dépôts retenus :

- `cwc-workshops` : `agent-battle`, `agents-that-remember`, `building-blocks`, `eval-driven-agent-development`, `production-ready-agent`, `research-desk` (hors skill d'atelier) et `ship-your-first-managed-agent` (hors `CLAUDE.md`) portent sur Managed Agents ou l'API. Sans intérêt direct. Une idée mineure à noter : `eval-driven-agent-development` note chaque variante avec des notes calculées par code **et** un juge LLM.
- `claude-quickstarts` : tous les autres dossiers (support client, analyse financière, computer use, navigateur, Managed Agents, `agents/`) sont sans rapport avec un SDLC.

## Licence et réutilisation

- **cwc-long-running-agents (Apache-2.0)** : meilleur candidat à une **adaptation ou un import**. Le `CLAUDE.md` de conventions, `agents/evaluator.md` et les hooks (`verify-gate`, `track-read`, `kill-switch`, `steer`, `commit-on-stop`) sont courts et autonomes. Un import garde l'en-tête « Copyright 2026 Anthropic PBC » et `SPDX-License-Identifier: Apache-2.0`, copie la licence dans `licenses/` et signale les modifications. Pas de `NOTICE` à ce commit. Les hooks demandent `bash` et `python3` : à vérifier sous Windows avant tout import.
- **cwc-workshops (Apache-2.0)** : mêmes obligations. Les candidats sont le `CLAUDE.md` de `ship-your-first-managed-agent` (règles d'arrêt), le skill `research-desk/.claude/skills/workshop` (modes et compte rendu) et le README de `phase-3-verify` (vocabulaire de vérification). Quelques fichiers n'ont pas d'en-tête SPDX, mais la licence racine les couvre.
- **claudes-c-compiler (CC0 1.0)** : aucune obligation. Créditer reste une courtoisie. Seuls les formats de fichiers de suivi nous intéressent.
- **claude-quickstarts (MIT à la racine)** : la licence racine autoriserait l'import avec la mention « Copyright (c) 2023 Anthropic ». Mais le README de `autonomous-coding/` affirme « Internal Anthropic use ». Par prudence, on reste au **niveau idée** pour ce dossier (les prompts initialiseur et agent de code), tant que la contradiction n'est pas levée.
- Licences à revérifier au commit exact au moment de tout import.
