# AWS : AI-Driven Development Life Cycle (AI-DLC)

AWS publie l'AI-DLC sous deux formes, étudiées ensemble ici :

- la **méthodologie**, présentée dans un billet de blog AWS (et un *white paper* / *Method Definition Paper* cités par le billet) ;
- le dépôt **`awslabs/aidlc-workflows`**, une implémentation installable de cette méthodologie dans plusieurs outils agentiques, dont Claude Code.

| | Méthodologie (blog) | Implémentation (dépôt) |
|---|---|---|
| **Source** | https://aws.amazon.com/blogs/devops/ai-driven-development-life-cycle/ | https://github.com/awslabs/aidlc-workflows |
| **Auteur** | Raja SP (Principal Solutions Architect, AWS) | Amazon.com, Inc. or its affiliates (awslabs) |
| **Version étudiée** | Publié le 2025-07-31 | commit `1102324f9f7df7b2fca57c52329e00273106c9c9` (2026-11-11), entre la release stable v2.11.3 (2026-11-04) et les préversions v2.11.4 |
| **Consulté le** | 2026-11-11 | 2026-11-11 |
| **Licence** | « © 2026, Amazon Web Services, Inc. or its affiliates. All rights reserved. » (pied de page, conditions du site AWS) | **MIT-0** (*MIT No Attribution*), « Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved. », lu dans `LICENSE` au commit étudié |
| **Niveau de reprise possible** | **Idée uniquement** | Idée, adaptation ou import (licence sans condition ; conserver la mention par courtoisie) |
| **Niveau actuel** | Idée | Idée |

Les références du README du dépôt (*Method Definition Paper* hébergé sur Amplify, spécification PDF `assets/AI-DLC-Workflows-2.0-Specification.pdf`) n'ont pas été lues en détail : voir « Sources secondaires ».

---

## 1. La méthodologie (billet de blog)

### Thèse

Les méthodes de développement actuelles ont été pensées pour des processus humains et longs ; une grande partie du temps part en planification, réunions et rituels. Le billet critique deux usages de l'IA :

- l'IA **assistante**, cantonnée à des tâches ponctuelles, qui plafonne vite ;
- l'IA **autonome**, qui construit seule une application sans intervention humaine, avec une qualité insuffisante.

L'AI-DLC propose une troisième voie : l'IA est au centre de l'exécution, l'humain garde les décisions.

### Modèle mental

Un motif unique, répété pour chaque activité du cycle : **l'IA produit un plan, pose des questions de clarification, et n'implémente qu'après validation humaine.** Les décisions critiques restent humaines, car c'est l'humain qui porte le contexte métier.

### Trois phases

| Phase | Ce que fait l'IA | Rituel humain |
|---|---|---|
| **Inception** | Transforme l'intention métier en exigences, *stories* et *units of work* | *Mob Elaboration* : toute l'équipe valide les questions et propositions de l'IA |
| **Construction** | Propose architecture logique, modèle de domaine, code et tests | *Mob Construction* : l'équipe tranche en temps réel les choix techniques |
| **Operations** | Gère l'infrastructure as code et les déploiements à partir du contexte accumulé | Supervision par l'équipe |

Chaque phase enrichit le contexte de la suivante. Les artefacts (plans, exigences, *stories*, conception, code, tests, IaC) sont stockés dans le dépôt pour que le travail se poursuive d'une session à l'autre.

### Vocabulaire

- **Bolts** à la place des sprints : des cycles de quelques heures ou jours.
- **Units of Work** à la place des *epics*.

## 2. L'implémentation `aidlc-workflows`

### En bref

Le dépôt se présente comme « un cœur, plusieurs harness » : un cœur neutre (Markdown + moteur TypeScript) généré vers Claude Code, Kiro, Codex, Cursor, opencode et Copilot. Installation par un exécutable natif (`aidlc config --harness claude`), puis invocation par `/aidlc <demande>`.

Ordres de grandeur à la version étudiée : 5 phases, 33 *stages*, 14 agents, 11 profils de workflow, 134 types d'événements d'audit.

### Principes affichés (reformulés)

1. L'utilisateur décide, l'IA exécute : toute décision importante passe par une porte d'approbation.
2. Profondeur adaptative : les projets simples sautent les étapes lourdes.
3. Artefacts traçables : chaque étape produit des documents Markdown versionnés.
4. Expertise multi-rôles : chaque étape est guidée par un agent « expert du domaine ».
5. Pas de comportement émergent : menus d'approbation, messages et transitions sont standardisés.
6. Des questions plutôt que des suppositions.
7. Détection des contradictions entre les réponses (périmètre, risque, technologies).

Un parti pris structurant : *« Small Mob, Broad Agents »*. Plutôt que des dizaines de spécialistes étroits (qui recréeraient des chaînes de passation façon cycle en V), peu d'agents polyvalents présents sur plusieurs étapes.

### Cinq phases

| Phase | Rôle |
|---|---|
| **Initialization** | Détection de l'espace de travail, échafaudage, état initial |
| **Ideation** | Valider l'initiative : intention, marché, faisabilité, périmètre, maquettes |
| **Inception** | Exigences, *stories*, conception du domaine et des contrats, découpage en *units*, plan de livraison ; *reverse engineering* pour l'existant |
| **Construction** | Conception fonctionnelle, exigences non fonctionnelles, infrastructure, génération de code, build et tests, CI |
| **Operation** | Environnements, déploiement, observabilité, incidents, performance, retours |

### Mécanismes notables

- **Profils de workflow (*scopes*)** : `express`, `bugfix`, `refactor`, `poc`, `mvp`, `feature`, `infra`, `security-patch`, `enterprise`… Chacun fixe une route d'étapes, une profondeur (minimale, standard, complète), une stratégie de test et un plafond de revue. L'agent propose un profil à partir de la demande ; l'humain confirme. Un « compositeur » peut aussi proposer une route sur mesure.
- **Moteur déterministe + conducteur** : un moteur décide de l'étape suivante ; la session `/aidlc` l'exécute. Le routage n'est pas laissé au modèle.
- **Questions dans un fichier** : chaque étape écrit ses questions dans un fichier, qui reste la source de vérité. L'humain choisit une fois son mode de réponse : guidé, édition directe du fichier, ou discussion libre dont l'agent extrait les décisions.
- **Portes d'approbation** à la fin de chaque étape (hors initialisation). L'approbation exige un **tour humain observé** : un hook enregistre chaque message de l'utilisateur, et le moteur refuse une approbation sans tour humain depuis l'ouverture de la porte. L'agent ne peut pas s'approuver lui-même.
- **Approbation du plan** avant toute génération de code, gardée par un hook.
- **Construction par *units*** : une *unit* à la fois par défaut, avec des points de contrôle vérifiés ; option d'un **squelette ambulant** (*walking skeleton*) : la première *unit* est la plus petite tranche intégrée qui fonctionne, approuvée avant les suivantes. La commande de vérification est choisie une fois par l'humain puis réutilisée.
- **Revue par étape, en deux classes** : *adversarial* (boucle réfuter-réparer, par défaut en Construction, où les constats sont vérifiables) et *advisory* (une seule passe, dont les constats sont remis à l'humain à la porte, pour les étapes de prose où la décision relève du jugement). Le relecteur ne reçoit ni le journal du constructeur ni son raisonnement.
- **Capteurs (*sensors*)** : contrôles déterministes déclarés par étape (lint, typage, sections obligatoires, traçabilité), consultatifs ou bloquants. Un capteur bloquant ferme la porte ; seul l'humain peut passer outre, et c'est tracé.
- **Traçabilité par identifiants** : exigences, *stories*, critères d'acceptation, *units* et règles métier portent des identifiants stables (`FR1`, `US1.3`, `AC1.3.2`…). Chaque étape produit un fichier de couverture vérifié automatiquement ; une vérification de cohérence a lieu à chaque changement de phase.
- **Boucle d'apprentissage** : quand l'humain corrige l'agent, celui-ci demande une seule fois « je le garde pour la prochaine fois ? », en reprenant ses mots. Les corrections ponctuelles (une coquille, un nom) ne sont pas proposées. Ce qui est gardé devient une règle en prose dans une mémoire en couches : organisation → équipe → projet → phase (→ étape, prévu). Les couches s'additionnent, sans mécanisme de surcharge ; les conflits sont vérifiés au moment de l'ajout.
- **Journal d'étape** (`memory.md`) : pendant une étape, l'agent consigne ses interprétations, ses écarts au protocole, ses arbitrages et ses questions ouvertes. Il n'est jamais transmis au relecteur.
- **Espaces et intentions** : chaque demande devient une *intent* datée avec son propre dossier d'artefacts, d'état et d'audit.
- **Audit** : journal d'événements détaillé (décisions, réponses humaines citées, portes, capteurs).

---

## Ce que JDLC peut en retenir

| # | Idée | Ce que ça donnerait dans JDLC | Convergence |
|---|---|---|---|
| 1 | **Plan → questions → validation → exécution, à chaque activité** | Motif de base de chaque skill JDLC. *Confirmation.* | AIDD (Frame → contrat), SFEIR (Define/Plan), principe 6 |
| 2 | **Profils de workflow nommés** (`bugfix`, `refactor`, `poc`, `feature`…) | Plutôt qu'un seul cycle modulé, quelques profils explicites qui fixent les phases parcourues et le niveau de preuve. L'agent propose, l'humain confirme. *Nouveau dans sa forme* : la proportionnalité devient un choix nommé et visible. | AIDD (plan proportionné), ADLC (triage), Anthropic A3 (évaluateur proportionné), principe 7 |
| 3 | **L'approbation exige un tour humain observé** | Un hook qui rend mécanique la règle « l'agent ne valide pas à la place de l'humain » : pas de passage de porte sans message humain depuis son ouverture. *Nouveau*. | Principe 6 ; Anthropic A4 (ce que l'agent choisit seul n'est pas autorisé) |
| 4 | **Fichier de questions comme source de vérité, trois modes de réponse** | Les questions de cadrage sont écrites dans un fichier versionné ; l'humain répond au fil de l'eau, dans le fichier, ou en discutant. Les décisions survivent à la session. *Nouveau*. | Anthropic A5 (journal durable hors contexte), principe 10 |
| 5 | **« Je le garde pour la prochaine fois ? » seulement après une correction** | Déclencheur précis pour la capitalisation : pas de rituel systématique, une question unique quand l'humain a corrigé l'agent, et rien pour les corrections ponctuelles. *Précise une idée existante*. | AIDD (`learn`), SFEIR (Compound), principe 11 |
| 6 | **Règles en couches, dont une couche par phase** | Mémoire projet découpée par portée, avec des règles propres à une phase (ex. « toute décision d'architecture documente au moins deux alternatives »). Additif, sans surcharge. *Nouveau* pour la couche « phase ». | AIDD (mémoire thématique), Anthropic A7 (hiérarchie de mémoire) |
| 7 | **Journal d'étape de l'agent, caché au relecteur** | L'agent note interprétations, écarts et arbitrages ; l'humain les voit à la porte, le relecteur indépendant non. *Nouveau*. | AIDD (*checker* neuf), Anthropic A3 (séparer génération et évaluation) |
| 8 | **Deux classes de revue** : réfuter-réparer pour le code, une passe consultative pour la prose | Le code est vérifiable, la boucle converge ; une spec relève du jugement humain, donc la revue l'éclaire sans boucler. *Nouveau* comme distinction explicite. | ADLC (*prosecution*), SFEIR (revues parallèles), Anthropic A3 |
| 9 | **Capteurs déterministes rattachés aux étapes, consultatifs ou bloquants** | Hooks de vérification déclarés par phase ; un échec bloquant ne se contourne que par décision humaine tracée. *Confirmation*, avec une forme concrète. | Principe 8 (preuve), ADLC (portes déterministes) |
| 10 | **Squelette ambulant en premier** | La première tranche est la plus petite tranche intégrée qui fonctionne, validée avant le reste. *Nouveau*. | SFEIR (Build par tranches), Anthropic A3 (une fonctionnalité à la fois) |
| 11 | **Commande de vérification choisie une fois par l'humain** | L'humain fixe *comment* on prouve ; l'agent ne peut pas changer la preuve en cours de route. *Nouveau*, proche des *rails* d'ADLC. | Principe 8, ADLC (rails gelés) |
| 12 | **Étape de *reverse engineering* pour l'existant** | Une phase optionnelle qui documente un code existant avant d'y travailler. *Confirmation*. | SFEIR (explorations amont), domaine d'application de la vision (modernisation) |
| 13 | **Identifiants stables et couverture exigence → story → critère → code** | Version légère : des identifiants pour les critères d'acceptation, repris dans les tests et dans la revue. La version complète (fichiers JSON par étape) est trop lourde pour JDLC. *Nouveau, à doser*. | Principe 2 (traçabilité), ADLC (critère → méthode de vérification) |

## Ce qu'on écarte ou relativise

- **Les rituels de *mob*** (*Mob Elaboration*, *Mob Construction*) et le mode atelier : pensés pour une équipe. JDLC est solo. On garde l'idée que l'humain tranche *en direct* les questions de l'IA, pas la cérémonie collective.
- **Une porte d'approbation après chaque étape** : avec 33 étapes, c'est l'exact risque de fatigue d'approbation décrit par Anthropic (A4). JDLC vise peu de portes, mais significatives.
- **Les personas d'agents** (14 « experts du domaine ») : ADLC les rejette explicitement comme du « costume » qui coûte des tokens. JDLC préfère des skills et des consignes de mission plutôt que des rôles.
- **L'échelle et l'orientation entreprise** : 33 étapes, 134 événements d'audit, conformité, agents AWS (plateforme, CDK, *Well-Architected*). Disproportionné pour un usage solo.
- **Le multi-outils** et le moteur natif à installer : hors périmètre ([ADR-0006](../adr/0006-claude-code-uniquement.md)) ; JDLC reste du Markdown et des fonctions natives de Claude Code ([ADR-0005](../adr/0005-mecanisme-installation.md)).
- **Le vocabulaire (*bolts*, *units of work*)** : intéressant pour la pédagogie, sans valeur opératoire pour JDLC.
- **Les bénéfices annoncés** par le billet (vitesse, qualité) : affirmations de l'éditeur, non chiffrées et non vérifiables.

## Sources secondaires

- *AI-DLC Method Definition Paper* (lien depuis le README du dépôt) et la spécification `AI-DLC-Workflows-2.0-Specification.pdf` du dépôt : non lus en détail. Le PDF est couvert par la licence MIT-0 du dépôt au commit étudié ; le *Method Definition Paper* hébergé à part n'a pas fait l'objet d'une vérification de licence.
- Le guide utilisateur du dépôt (`docs/guide/`), notamment *Workflow Profiles*, *Interaction Modes* et *Rules and the Learning Loop*, a servi de base à la section 2.
