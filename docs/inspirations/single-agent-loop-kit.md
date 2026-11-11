# Single agent loop kit (Anthropic)

| | |
|---|---|
| **Source** | [anthropics/single-agent-loop-kit](https://github.com/anthropics/single-agent-loop-kit) |
| **Auteur** | Anthropic PBC |
| **Version étudiée** | Commit `c437962` (2026-10-21) |
| **Article associé** | *Building agents: When to fold subagents into your main agent loop* (claude.com/resources). Non lu à ce stade. |
| **Consulté le** | 2026-11-11 |
| **Licence** | **Apache-2.0**, « Copyright 2026 Anthropic PBC », en-têtes SPDX dans chaque fichier. Pas de fichier `NOTICE`. |
| **Niveau de reprise possible** | Idée, adaptation ou import : Apache-2.0 est compatible avec MIT (voir [licences.md](../licences.md)). Un import doit garder la licence et signaler les modifications. |
| **Niveau actuel** | **Idée** |
| **Statut** | Implémentation de référence, ni maintenue ni ouverte aux contributions. |

## De quoi il s'agit

Un kit pour transformer un système multi-agents (un routeur devant des sous-agents spécialisés) en **une seule boucle d'agent**, qui charge skills et outils à la demande. Le cœur est un **skill Claude Code** (`skill/`) en six étapes :

0. **Audit** : inventaire des sous-agents, avec un verdict pour chacun (*fold*, *merge*, *as-tool*, *keep*).
1. **Baseline** : réutiliser les évals existantes, combler les manques, mesurer le système actuel.
2. **Merge** : fusionner deux sous-agents qui travaillent toujours ensemble.
3. **Fold** : transformer un sous-agent en skill plus outils dans l'agent principal.
4. **As-tool** : garder un sous-agent, mais l'appeler comme un outil à résultat structuré.
5. **Upkeep** : réduire le prompt, élaguer les skills, fixer qui possède quoi.

Le kit fournit aussi quatre scripts Python sans dépendance (comparaison de deux exécutions d'éval, couverture des chaînes, estimation de tokens, carte de couplage), des gabarits (plan, skill, spécification d'outil, schéma de réponse, règles de contribution), une boucle d'agent de référence et un exemple exécutable.

Le domaine visé (des systèmes d'agents en production) n'est pas celui de JDLC. Mais le kit est lui-même **un skill Claude Code long, à étapes et à validations humaines**, écrit par Anthropic. C'est exactement la forme que prendront les composants de JDLC, d'où son intérêt.

## Ce que JDLC peut en retenir

### Sur la forme d'un skill à étapes

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 1 | **Un `SKILL.md` porte d'entrée, un fichier par étape.** « Lis seulement le fichier de l'étape en cours. » Un tableau donne pour chaque étape son fichier et son critère « terminé quand ». | Structure type des skills JDLC multi-phases : un `SKILL.md` court, un fichier par phase, et un critère de sortie explicite par phase. | Confirmation (divulgation progressive, documentation Claude Code), avec un format concret |
| 2 | **Un plan versionné sert d'état de reprise.** Le skill écrit `single-loop/PLAN.md` avec une liste de statuts à cocher. S'il existe déjà, on reprend à la première case non cochée. | Chaque travail JDLC laisse un fichier d'état dans le dépôt cible, commité avec le code, qui permet de reprendre dans une session neuve. | Confirmation : Anthropic A3 (fichiers de progression), passation YK |
| 3 | **Des points d'arrêt fixes, toujours les mêmes** : après l'audit, avant toute dépense, après la baseline, après chaque changement. | Les validations humaines de JDLC sont **des arrêts nommés et prévisibles**, pas des demandes au fil de l'eau. Alimente la question ouverte « 2 ou 3 validations ». | **Nouvelle** dans sa précision. Rejoint la fatigue d'approbation (A4) et les portes ADLC |
| 4 | **Un format de point d'étape imposé** : *Constaté* (réponse directe aux questions, chiffres, surprises, actions hors plan), *Recommandé* (une action et sa raison, une alternative), *Besoin de toi* (**une seule question**, avec les réponses possibles écrites). Une quinzaine de lignes ; le détail reste dans le plan. | Gabarit de point d'étape pour tous les skills JDLC. Une question, une décision, répondable en un mot. | **Nouvelle** |
| 5 | **Règles des arrêts.** Un arrêt est un arrêt : on ne commence même pas à lire pour l'étape suivante. Un « oui » vaut pour un seul arrêt. Une demande qui liste plusieurs étapes ne supprime pas les arrêts, sauf si la personne le dit en toutes lettres. | Règles d'interaction communes aux skills JDLC. Elles rendent l'autonomie explicite au lieu de la laisser dériver. | **Nouvelle** |
| 6 | **Un mode « continue tant que c'est propre »**, proposé seulement après un premier point d'étape complet. On s'arrête de nouveau dès qu'une anomalie apparaît, et chaque point d'étape reste écrit dans le plan. | Une réponse possible à la question « auto ou supervisé par défaut » : supervisé d'abord, autonomie gagnée ensuite, retirée au premier écart. | **Nouvelle** |

### Sur la preuve et la mesure

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 7 | **Mesurer avant de changer.** Une baseline, un seul changement par mesure, une comparaison avec la dernière version gardée. Plusieurs répétitions, pour connaître le bruit. Si le résultat est ambigu, on recommande plus de répétitions plutôt que de trancher. | Méthode pour faire évoluer JDLC lui-même (avec `claude plugin eval`) : un changement de skill à la fois, toujours comparé. | Confirmation : principe 1 (empirique), principe 11 |
| 8 | **Le test avec et sans skill.** On rejoue les tâches d'un domaine sans le skill, outils conservés. À score égal, le modèle n'en a pas besoin et le skill est supprimé. Test à refaire à chaque nouveau modèle. | Critère concret pour « JDLC doit savoir maigrir » : chaque skill JDLC prouve son apport, et ce test est rejoué à chaque montée de modèle. | **Nouvelle** en tant que procédure |
| 9 | **L'humain fixe l'oracle.** L'agent corrige un bug dans une vérification, mais ne fixe jamais seul un résultat attendu. Sinon l'éval dérive vers ce que fait le système aujourd'hui, bugs compris. | Règle pour les phases Spécification et Tests : les critères d'acceptation sont validés par l'humain. L'agent peut signaler un attendu qu'il croit faux, pas le changer. | **Nouvelle** formulation. Rejoint le principe 9 et « terminé défini avant de coder » |
| 10 | **Noter le résultat, pas le chemin.** Un test vérifie ce qui a changé et ce qui a été dit, pas quel agent ou outil a tourné. | Consigne pour les tests produits par JDLC : vérifier le comportement, pas l'implémentation. | Confirmation : C1 (tests de bout en bout minimalistes), ADLC |
| 11 | **La carte des ruptures.** Avant un changement, lister les façons dont il peut casser (règle perdue, raccourci, mauvais choix, écriture non voulue, chemin d'erreur, dérive de coût) et, pour chacune, le test qui la verrait. Une case vide est un risque assumé, écrit noir sur blanc. | Pour la phase Revue & vérification : une carte des régressions possibles et du test qui les détecte. Un trou est une décision humaine, tracée. | **Nouvelle** |
| 12 | **Chaque verdict cite sa preuve** (fichier et ligne, ou un chiffre), dit quand il est déduit et donne un niveau de confiance (haut, moyen, bas). | Format des constats JDLC : preuve, inférence signalée, confiance. | Confirmation : principe 8 (discipline de la preuve) |
| 13 | **Dire ce qui a vraiment été exécuté.** Le README sépare ce qui a tourné sur un vrai modèle de ce qui n'a été vérifié qu'hors ligne. Il dit aussi ce qui a été retiré des comptes rendus, et ce qu'aucune session n'a encore suivi. | Pratique pour la documentation de JDLC : pour chaque composant, ce qui a été éprouvé en vrai, et sur quoi. Cohérent avec un projet public et pédagogique. | **Nouvelle** dans sa forme. Prolonge la discipline de la preuve |
| 14 | **Prouver une règle par un appel direct.** Une limite déplacée du prompt vers un outil est testée en appelant l'outil avec un cas interdit. Une éval qui passe parce que le modèle n'a jamais tenté l'action interdite ne prouve rien. | Pour les garde-fous JDLC (hooks) : un test direct qui montre que le garde-fou bloque, sans passer par le modèle. | Confirmation : consignes ou garanties (hook `Stop`) |
| 15 | **Annoncer le coût avant de dépenser**, avec une estimation fondée sur une mesure, puis comparer l'estimation au réel. | Pour les phases JDLC coûteuses (plusieurs sous-agents, évals) : annoncer la taille avant de lancer. | **Nouvelle** pour JDLC |

### Sur l'architecture des agents

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 16 | **Une seule boucle par défaut.** Un sous-agent doit *mériter* son contexte, pour l'une de trois raisons : il lit beaucoup plus que ce dont l'agent principal a besoin ensuite ; il faut des contextes indépendants (exploration en parallèle, **vérification sans voir comment le travail a été produit**) ; il y a un conflit d'outils que les skills ne règlent pas. | Critère pour décider qu'un composant JDLC est un sous-agent plutôt qu'un skill. Le vérificateur indépendant est justement un cas légitime. | **Nouvelle** grille. Confirme la simplicité (A1, C2) |
| 17 | **Ce qui ne justifie pas un sous-agent** : un autre propriétaire, une persona, un long prompt, beaucoup d'outils, des étapes fixes. Des étapes fixes sont un workflow, donc du code. | Les étapes déterministes de JDLC vont dans des scripts, pas dans des agents ; le jugement va dans les skills. | **Nouvelle** pour la liste ; confirmation pour « le déterministe en script » (C2) |
| 18 | **Une réponse de sous-agent structurée et validée par du code** : conclusion, données utilisées, actions faites, confiance et réserves. Les faits indispensables sont des **champs obligatoires**, pas du texte libre, car un fait laissé dans un briefing libre peut être omis. Une réponse vide est une erreur. | Complète le gabarit de briefing (note [anthropic-talks-erik.md](anthropic-talks-erik.md), idée 9) avec un format de retour commun à tous les sous-agents JDLC. | **Nouvelle** |
| 19 | **Les limites strictes vivent dans le harnais, pas dans le prompt** : budget de tours et de tokens, plafond d'appels par lot, une porte d'approbation devant tout outil qui écrit, un vérificateur optionnel (une réponse rejetée deux fois n'est pas rendue). | Confirme l'usage des hooks et permissions de Claude Code pour les garanties de JDLC. | Confirmation : A4, consignes ou garanties |

### Sur l'écriture et l'entretien des skills

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 20 | **La description est la seule partie vue avant le chargement.** Une ou deux phrases, dans les mots de l'utilisateur, distincte de toutes les autres. Deux skills qui se ressemblent seront choisis au hasard. | Règle de rédaction des descriptions des skills JDLC, et une revue de recouvrement entre elles. | Confirmation : documentation Claude Code, Superpowers |
| 21 | **Le corps du skill, comme le briefing d'un collègue compétent** : le but, les étapes non évidentes, la raison de chaque règle. On omet ce que le modèle ferait de toute façon, et on supprime les consignes écrites pour d'anciens modèles (majuscules, « réfléchis étape par étape »). | Guide de style des skills JDLC. | Confirmation : A2, documentation Claude Code |
| 22 | **Une liste de contrôle avant de publier un skill** : un propriétaire nommé, un nom unique, une description sans recouvrement, les limites strictes dans des outils, une éval jointe, le test avec et sans, rien d'ajouté au prompt système. | Base d'une liste de contrôle « definition of done » pour les skills de JDLC. | **Nouvelle** en tant que liste |
| 23 | **Supprimer par défaut.** Quand le comportement dérape, la solution est rarement une phrase de plus dans le prompt système, mais un skill plus clair ou un meilleur résultat d'outil. | Règle d'hygiène pour le CLAUDE.md que JDLC installera : chaque ajout se justifie par un échec observé. | Confirmation : principe 11, « la forme la moins chère qui empêche la répétition » |
| 24 | **Des scripts sans dépendance**, parce que le skill est copié seul dans d'autres dépôts. | Contrainte d'installabilité pour les scripts embarqués par JDLC (ADR-0005). | **Nouvelle** contrainte concrète |
| 25 | **S'appuyer sur les commandes existantes** (`/claude-api build-eval`, `hillclimb`, `prompt-audit`, `/skill-creator`, `/skill-doctor`) plutôt que les refaire. Si l'une manque, le dire et faire la partie à la main. | JDLC délègue à l'outillage de Claude Code quand il existe (ADR-0006), avec un repli explicite. | Confirmation : ADR-0006 |
| 26 | **Protéger l'évaluation de toute contamination.** Le CLAUDE.md du kit interdit d'ouvrir le corrigé pendant l'audit, pour que les verdicts viennent du code. | Pour le vérificateur indépendant de JDLC : définir ce qu'il n'a pas le droit de lire. | **Nouvelle** formulation de l'indépendance du vérificateur |

## Ce qu'on écarte ou relativise

- **Le domaine lui-même** (migrer un système multi-agents de production) est hors périmètre. On retient la méthode, pas l'objet.
- **L'outillage d'éval complet** (répétitions, estimations de tokens, scripts de comparaison) est lourd pour un développeur seul. On garde le principe, à une échelle proportionnée.
- **La gouvernance par équipes** (équipe centrale, équipes de domaine) est hors périmètre : JDLC ne vise pas le travail en équipe pour l'instant.
- **Les commandes `/claude-api …`** dépendent d'un plugin et d'une version de Claude Code. À revérifier avant d'en dépendre.

## Licence et réutilisation

- Apache-2.0 est dans la colonne « OK » de la matrice de [licences.md](../licences.md). Le kit est donc **le premier candidat sérieux à une adaptation ou un import**, par exemple : le format de point d'étape, le gabarit de skill, la liste de contrôle de publication.
- Un import devra garder l'en-tête `Copyright 2026 Anthropic PBC` et `SPDX-License-Identifier: Apache-2.0`, copier le texte de la licence dans `licenses/` et signaler les modifications (section 4 de la licence). Il n'y a pas de fichier `NOTICE` à reprendre au commit étudié.
- Licence à revérifier au commit exact au moment de l'import.
