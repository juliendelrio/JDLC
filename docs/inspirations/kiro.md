# Kiro (AWS) : développement piloté par les specs

Kiro est l'environnement de développement agentique d'AWS (IDE, CLI, Web). Son approche se distingue par les **specs** (exigences, conception, tâches), le **steering** (contexte projet persistant) et les **hooks**. L'article [SFEIR](sfeir-sdlc-ai.md) le cite parmi les approches convergentes. On l'étudie uniquement à travers sa documentation officielle.

| | |
|---|---|
| **Source** | Documentation officielle : https://kiro.dev/docs/specs/, https://kiro.dev/docs/specs/feature-specs/, https://kiro.dev/docs/specs/bugfix-specs/, https://kiro.dev/docs/specs/analyze-requirements/, https://kiro.dev/docs/specs/correctness/, https://kiro.dev/docs/specs/plan/, https://kiro.dev/docs/specs/best-practices/, https://kiro.dev/docs/steering/, https://kiro.dev/docs/hooks/ |
| **Auteur** | Amazon Web Services (Amazon.com, Inc. ou ses filiales) |
| **Version étudiée** | Pages datées du 2026-08-04 au 2026-10-21 (date « last updated » de chaque page) |
| **Consulté le** | 2026-11-11 |
| **Licence** | Page *License* du site : « ©2026 Amazon.com, Inc. or its affiliates […] All Rights Reserved. » Le logiciel est diffusé comme *AWS Content* sous l'AWS Customer Agreement ; **aucune licence de réutilisation** n'est indiquée pour la documentation. Kiro n'est pas open source. |
| **Niveau de reprise possible** | **Idée uniquement** : synthèse reformulée, citations courtes et attribuées ([politique](../licences.md)) |
| **Niveau actuel** | Idée |

> Les pages ont été lues via une extraction automatique (résumés de contenu). Quelques détails (liste complète des déclencheurs de hooks, page *Quick Spec*) n'ont pas été consultés directement.

---

## Synthèse

### Les specs : trois documents, trois phases

Une spec formalise une fonctionnalité ou une correction, de l'idée au plan d'implémentation suivi. Chaque spec a son dossier, `.kiro/specs/<nom>/`, versionné avec le code. La documentation recommande **une spec par fonctionnalité** plutôt qu'une spec globale.

| Phase | Document | Contenu |
|---|---|---|
| Exigences (ou analyse du bug) | `requirements.md` (ou `bugfix.md`) | User stories et critères d'acceptation |
| Conception | `design.md` | Architecture, diagrammes de séquence, flux de données, gestion des erreurs, stratégie de test |
| Tâches | `tasks.md` | Plan d'implémentation découpé en tâches suivies |

Dans le parcours standard, **chaque document est relu avant que le suivant ne soit généré**. La documentation présente cela comme adapté aux équipes qui veulent des points d'approbation explicites. Une revue Markdown permet de commenter des passages précis et de les renvoyer ensemble à l'agent.

### Notation EARS pour les exigences

Les critères d'acceptation suivent **EARS** (*Easy Approach to Requirements Syntax*), sur le modèle *« WHEN [condition/event] THE SYSTEM SHALL [expected behavior] »*. Pour Kiro, cette forme rend les exigences claires, testables et traçables. EARS est une notation antérieure à Kiro, généralement attribuée à Alistair Mavin et al. (Rolls-Royce, 2009 ; référence à vérifier avant de la citer) ; Kiro l'utilise, il ne l'a pas inventée.

### Deux ordres possibles : exigences d'abord ou conception d'abord

- **Requirements-First** (exigences → conception → tâches) : quand on sait quel comportement on veut et que l'architecture est ouverte.
- **Design-First** (conception → exigences → tâches) : quand l'architecture est déjà décidée, que les contraintes non fonctionnelles dominent, ou qu'il faut d'abord vérifier la faisabilité.
- Les deux peuvent coexister dans un projet, par exemple une spec Design-First pour valider la faisabilité puis une spec Requirements-First pour le développement.

### Variantes selon la taille du travail

| Mode | Usage recommandé | Artefacts |
|---|---|---|
| **Plan mode** | Travail moyen (de l'ordre de 15 à 60 min d'implémentation) | Un plan dans la conversation, pas de fichiers. L'agent est en **lecture seule** (lecture, recherche, web ; ni écriture, ni commande). Le plan approuvé part à l'agent d'exécution. |
| **Quick Spec** | Fonctionnalité bien comprise | Les trois documents générés d'un coup, **sans portes d'approbation** |
| **Feature Spec** | Travail complexe, collaboration, besoin de piste d'audit | Les trois documents, relus un par un |

### Bugfix specs : décrire aussi ce qui ne doit pas changer

`bugfix.md` remplace le document d'exigences et contient trois parties, chacune au format condition/résultat :

1. le **comportement actuel** (le défaut) ;
2. le **comportement attendu** ;
3. le **comportement inchangé**, c'est-à-dire ce qui doit continuer à fonctionner (prévention des régressions).

L'agent explore ensuite le code, identifie la cause racine et rédige la conception : cause, correctif, propriétés à tester. Les tâches incluent des tests qui prouvent que le bug existe, qu'il est corrigé, et que le comportement inchangé l'est resté.

### Analyse des exigences

Étape optionnelle entre exigences et conception. L'agent examine l'ensemble des exigences **ensemble** pour y chercher :

- des incohérences logiques entre exigences individuellement valides ;
- des termes ambigus ;
- des conflits entre contraintes fonctionnelles et non fonctionnelles ;
- des hypothèses implicites (concepts jamais définis) ;
- des cas limites manquants (échecs, bornes, accès concurrents).

Chaque constat arrive sous forme de question, avec les exigences concernées et des corrections proposées. L'utilisateur choisit une correction, répond librement ou écarte la question si l'ambiguïté est voulue ; `requirements.md` est mis à jour en conséquence. Recommandée pour les fonctionnalités complexes ou sensibles, et après une Quick Spec non relue.

### Correction par tests de propriétés

Kiro peut extraire des **propriétés** des exigences EARS (une règle qui doit tenir pour toute entrée) et générer des tests de propriétés (*property-based testing*). Chaque propriété reste liée à son exigence et à sa tâche. En cas d'échec, le contre-exemple est réduit à sa forme minimale (*shrinking*), et l'on décide avec l'agent s'il faut corriger le code, le test ou **l'exigence**. La documentation est honnête sur les limites : c'est une preuve empirique, pas une preuve formelle, et une propriété mal posée peut passer à tort.

### Exécution et synchronisation des tâches

- Les tâches affichent leur état (en cours, terminée) et peuvent être lancées une par une ou toutes ensemble.
- En exécution groupée, Kiro construit un **graphe de dépendances** et lance les tâches par **vagues** : en parallèle dans une vague, les vagues en séquence.
- **Synchronisation** : après une modification des exigences ou de la conception, on demande de mettre à jour les documents en aval. Une action *Sync Files* met à jour l'état des tâches et crée les tâches correspondant aux nouvelles exigences. L'agent peut aussi parcourir le code pour marquer les tâches déjà réalisées.

### Steering : le contexte projet persistant

Des fichiers Markdown qui évitent de répéter les conventions à chaque conversation.

- **Trois fichiers de base**, générables automatiquement en analysant le projet :
  - `product.md` : but, utilisateurs, fonctionnalités, objectifs ;
  - `tech.md` : frameworks, bibliothèques, outils, contraintes ;
  - `structure.md` : organisation des fichiers, nommage, architecture.
- **Portée** : `.kiro/steering/` pour le projet, `~/.kiro/steering/` pour l'utilisateur ; le projet l'emporte en cas de conflit.
- **Modes d'inclusion**, déclarés dans l'en-tête du fichier :

  | Mode | Chargé… |
  |---|---|
  | `always` (défaut) | à chaque interaction |
  | `fileMatch` | quand on travaille sur des fichiers correspondant à un motif |
  | `manual` | quand on le référence explicitement |
  | `auto` | quand la demande correspond à sa description |

- **Références de fichiers** : un fichier de steering peut pointer vers un fichier du projet (un schéma d'API, par exemple) plutôt que d'en recopier le contenu. Une référence non résolue reste visible au lieu d'échouer en silence.
- Prise en charge du standard `AGENTS.md`, y compris dans les sous-dossiers.
- Conseils : fichiers ciblés, noms clairs, jamais de secrets.

### Hooks

Des actions déclenchées automatiquement par un événement de session.

- **Déclencheurs** : par exemple après l'enregistrement d'un fichier, avant ou après l'utilisation d'un outil, à l'arrêt de l'agent. Un filtre restreint le déclenchement (motif de chemin, outil, serveur MCP).
- **Deux types d'action** : une **commande shell** (qui reçoit le contexte en JSON) ou un **prompt injecté** dans la conversation de l'agent.
- **Usages cités** : lint, formatage ou vérification de types après modification ; bloquer un outil tant qu'une précondition n'est pas remplie ; générer tests, documentation ou traductions pour un nouveau fichier ; contrôler la qualité avant que l'agent ne termine.
- Stockés en JSON dans `.kiro/hooks/` (projet) ou `~/.kiro/hooks/` (utilisateur), désactivables sans suppression. Dans l'IDE, on peut décrire un hook en langage naturel et laisser l'agent générer sa configuration.

---

## Ce que JDLC peut en retenir

### Idées qui confirment des choix ou pistes existants

| # | Idée | Ce que ça donnerait dans JDLC | Convergence |
|---|---|---|---|
| 1 | **Trois documents (exigences, conception, tâches), relus un par un** | Même ossature que Spec Kit ; confirme qu'un cycle spec → conception → tâches avec relecture entre chaque étape est devenu un standard de fait. | [Spec Kit](spec-kit.md), SFEIR (Define / Plan), AIDD (contrat / plan), principe 6 |
| 2 | **Le niveau de formalisme suit la taille du travail** (plan mode, Quick Spec, Feature Spec) | Trois chemins dans JDLC : correction directe, plan léger, spec complète. Critère de choix documenté. | AIDD (plan proportionné), principe 7 |
| 3 | **Contexte persistant découpé en fichiers ciblés** (produit / technique / structure) | Gabarit du contexte initial d'un projet cible, produit par la future commande `init`. Équivalent natif dans Claude Code : `CLAUDE.md` + règles. | SFEIR (Setup), AIDD (mémoire projet thématique), principe 10 |
| 4 | **Chargement conditionnel du contexte** (always / fileMatch / manual / auto) | Exploiter les équivalents Claude Code : règles limitées à certains chemins, skills chargés sur description, commandes manuelles. | Anthropic A2 (contexte juste à temps), AIDD (skills routeurs) |
| 5 | **Hooks pour les contrôles automatiques** (après écriture, avant outil, à l'arrêt) | Brancher la discipline de la preuve sur les hooks Claude Code (par exemple vérifier à l'arrêt que les tests ont tourné). | Principe 8, SFEIR, [Spec Kit](spec-kit.md) (hooks avant/après étape) |
| 6 | **Analyse des exigences par questions, avec choix de l'humain** | Même idée que `clarify` / `analyze` de Spec Kit ; l'humain peut écarter une ambiguïté volontaire. | [Spec Kit](spec-kit.md), règle AIDD « demander plutôt que deviner » |
| 7 | **Tâches exécutées par vagues selon leurs dépendances** | Parallélisation par sous-agents des tâches indépendantes. | [Spec Kit](spec-kit.md) (tâches `[P]`), Anthropic A1 (parallélisation) |

### Idées nouvelles

| # | Idée | Ce que ça donnerait dans JDLC | Convergence |
|---|---|---|---|
| 8 | **Notation EARS pour les critères d'acceptation** | Format recommandé des critères dans la spec JDLC (« QUAND … LE SYSTÈME DOIT … ») : chaque critère est testable et traçable. Adopter une notation (une idée de syntaxe) relève du niveau « idée » ; on la créditera à sa source d'origine, pas à Kiro. | Nouveau ; renforce la vérifiabilité (principe 8) |
| 9 | **Bugfix spec à trois volets : actuel, attendu, inchangé** | Gabarit du chemin « correction de bug » : expliciter ce qui ne doit **pas** changer, et le prouver par un test de non-régression. | Complète le processus bug de [Spec Kit](spec-kit.md) ; principe 8 |
| 10 | **Exigences → propriétés → tests de propriétés** | Option pour les projets qui s'y prêtent : dériver des invariants des critères d'acceptation. Un échec peut remettre en cause **l'exigence**, pas seulement le code. | Nouveau ; prolonge la preuve vers le niveau spec |
| 11 | **Requirements-First ou Design-First** | Admettre que l'ordre spec → conception n'est pas universel : quand l'architecture est imposée, la conception peut précéder les exigences. | Nouveau ; nuance les phases de la vision |
| 12 | **Mode planification en lecture seule** | Utiliser le mode plan natif de Claude Code comme chemin intermédiaire, sans fichiers. Garantie structurelle : l'agent ne peut rien modifier pendant qu'il planifie. | Principe 6 ; Anthropic A4 (permissions par paliers) |
| 13 | **Références de fichiers dans le contexte plutôt que copie** | Les fichiers de contexte JDLC pointent vers la source (schéma, ADR) au lieu de la dupliquer, pour éviter les contenus périmés. Équivalent Claude Code : imports `@chemin` dans `CLAUDE.md`. | Anthropic A7 (une note fausse se propage) |
| 14 | **Synchronisation spec → tâches et état des tâches recalculé depuis le code** | Une commande de resynchronisation après modification de la spec, qui ajoute les tâches manquantes. | Proche de `converge` ([Spec Kit](spec-kit.md)) |
| 15 | **Partage de specs entre dépôts** (dépôt central de specs) | À garder en tête pour plus tard ; peu utile en solo. | — |

## Ce qu'on écarte ou relativise

- **L'outil lui-même** : Kiro est un produit propriétaire (IDE, CLI, Web) ; JDLC cible uniquement Claude Code ([ADR-0006](../adr/0006-claude-code-uniquement.md)). On retient les idées, transposées vers les fonctions natives de Claude Code (`CLAUDE.md`, règles, skills, hooks, mode plan).
- **L'interface graphique** (bouton de revue, panneau de steering, *Sync Files*) : propre à l'IDE. Dans JDLC, l'équivalent passe par des commandes et des fichiers.
- **Quick Spec sans portes** : contraire au principe 6 tel quel. Acceptable seulement si la relecture est déplacée (par exemple une analyse des exigences obligatoire derrière).
- **Le steering d'équipe** (distribution par MDM, apprentissage à partir des commentaires de PR) : hors périmètre solo.
- **Les tests de propriétés** : puissants, mais dépendants du langage et de la nature du projet. Option, pas règle par défaut.
- **Le statut des sources** : documentation produit d'un éditeur commercial, sans détail sur l'implémentation des prompts. On ne voit que ce que le produit expose.

## Sources secondaires

- **EARS** (*Easy Approach to Requirements Syntax*), généralement attribuée à Alistair Mavin et al. (IEEE RE 2009) : la notation elle-même, à créditer à sa source d'origine (référence à vérifier) si JDLC l'adopte.
- Page *Quick Spec* (https://kiro.dev/docs/specs/quick-spec/) et page *Hook types*, non lues en détail.
