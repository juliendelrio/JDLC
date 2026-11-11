# Anthropic : cookbooks, démos, évals et tutoriels

Six dépôts publics d'Anthropic, étudiés ensemble parce qu'ils ont la même nature : des exemples et des supports de formation, pas des outils. On n'a lu que ce qui touche un SDLC bâti sur Claude Code.

| Dépôt | Auteur | Version étudiée | Licence vérifiée au commit | Reprise possible | Pertinence |
|---|---|---|---|---|---|
| [claude-cookbooks](https://github.com/anthropics/claude-cookbooks) | Anthropic | `ce8b262` (2026-11-04) | **MIT**, « Copyright (c) 2023 Anthropic », un seul `LICENSE` à la racine | Idée, adaptation, import | **Forte** (sections lues seulement) |
| [agent-sdk-workshop](https://github.com/anthropics/agent-sdk-workshop) | Anthropic | `a273fbe` (2026-03-04) | **Apache-2.0**, « Copyright 2026 Anthropic, PBC ». Pas de `NOTICE` | Idée, adaptation, import | Faible |
| [claude-agent-sdk-demos](https://github.com/anthropics/claude-agent-sdk-demos) | Anthropic | `826b268` (2026-03-13) | **Aucune licence à la racine** : idée uniquement. Licences locales : `excel-demo/LICENSE` (MIT d'Electron React Boilerplate, un tiers) ; skills `pdf` et `xlsx` sous licences propriétaires | Idée | Faible |
| [html-effectiveness](https://github.com/anthropics/html-effectiveness) | Anthropic (commits de Thariq Shihipar) | `58c305b` (2026-05-15) | **MIT**, « Copyright (c) 2026 Anthropic PBC » | Idée, adaptation, import | Faible (une idée) |
| [prompt-eng-interactive-tutorial](https://github.com/anthropics/prompt-eng-interactive-tutorial) | Anthropic | `0d27754` (2024-04-07) | **Aucune licence à la racine** : idée uniquement. Le seul `LICENSE` est dans `AmazonBedrock/` (MIT-0, Amazon) | Idée | Nulle |
| [evals](https://github.com/anthropics/evals) | Anthropic | `84fcc67` (2023-01-03) | **CC BY 4.0** | Idée, import (documentation) | Nulle |

- **Consulté le** : 2026-11-11.
- **Niveau actuel** : **Idée** pour tous.

## De quoi il s'agit

**claude-cookbooks.** Un grand recueil de notebooks Jupyter pour construire avec l'API Claude. La plupart des recettes portent sur des fonctions de l'API (classification, RAG, vision, cache). On a lu seulement :

- `managed_agents/` : des tutoriels sur Managed Agents, l'environnement hébergé d'Anthropic. Plusieurs portent sur le code : corriger des tests, aller d'un ticket à une PR fusionnée, explorer un dépôt inconnu, faire vérifier un livrable par un correcteur (*Outcomes*), consolider la mémoire (*dreaming*), relire après coup les sessions d'agents.
- `claude_agent_sdk/` : des agents construits avec le SDK qui fait tourner Claude Code. Les plus utiles : un relecteur de dépôt planifié (`scheduled_repository_reviewer/`), une détection de vulnérabilités en trois passes (`06_…`) et les *dynamic workflows* de Claude Code (`08_Dynamic_workflows.ipynb`).
- `patterns/agents/` : l'implémentation de référence de *Building effective agents* (déjà analysé dans [anthropic.md](anthropic.md), A1).
- `misc/building_evals.ipynb`, `misc/generate_test_cases.ipynb`, `tool_evaluation/` : des bases sur l'écriture d'évals.
- `tool_use/context_engineering/`, `tool_use/memory_cookbook.ipynb`, `misc/session_memory_compaction.ipynb` : compaction, nettoyage des résultats d'outils, mémoire.
- `skills/` : les skills côté API, avec des exemples financiers. Rien de neuf pour JDLC.

**agent-sdk-workshop.** Un atelier pour des managers techniques. Une partie guidée active les capacités une par une (outils, sous-agents, mémoire) sur une même tâche. Des ateliers en groupe assemblent des briques toutes prêtes. Son `DESIGN.md` explique les choix pédagogiques : c'est la partie intéressante.

**claude-agent-sdk-demos.** Des applications de démonstration (assistant mail, Excel, recherche multi-agents, chat). Le dossier `research-agent/.claude/` contient des commandes et skills, mais tournés vers la recherche web. Peu pour JDLC.

**html-effectiveness.** Vingt pages HTML autonomes qui accompagnent un billet de blog : produire du HTML plutôt que du Markdown quand un humain doit lire. Parmi elles : un plan d'implémentation, une PR annotée, une description de PR pour les relecteurs.

**prompt-eng-interactive-tutorial.** Un cours de prompting en 9 chapitres écrit pour Claude 3 Haiku (2024). Les bons conseils (séparer données et instructions, permettre « je ne sais pas ») sont depuis repris dans la documentation officielle. Rien ici ne sert à écrire des skills pour les modèles actuels.

**evals.** Les jeux de données d'un article de recherche de 2022 sur les évaluations écrites par un modèle (persona, complaisance, risques). Ce sont des données, pas une méthode pour écrire des évals.

## Ce que JDLC peut en retenir

### Sur la vérification par un correcteur

Source : `managed_agents/CMA_verify_with_outcome_grader.ipynb`. Un *rédacteur* produit un livrable. Un *correcteur*, dans un contexte neuf, le relit avec une **grille** (*rubric*) et le renvoie avec la liste des manques, jusqu'à un plafond d'itérations.

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 1 | **Par défaut, un correcteur approuve tout.** « Vérifie que le texte parle de X » lui permet de survoler et de valider. La grille doit **l'obliger à produire une preuve** (une page relue, un `fichier:ligne`) avant de valider un critère. | Règle de rédaction des grilles du vérificateur JDLC : chaque critère dit ce qui compte comme preuve. Un critère validé sans preuve est un critère non vérifié. | **Nouvelle** dans sa formulation. Prolonge l'évaluateur calibré ([anthropic.md](anthropic.md), idée 10) et le principe 8 |
| 2 | **Six règles pour une grille utilisable** : chaque critère est vérifiable ; la grille est **plus précise que la tâche** ; elle décrit le but, pas la commande à lancer ; elle anticipe les raccourcis du rédacteur ; elle impose le format du retour (un tableau de score, puis une ligne par échec avec quoi corriger) ; elle dit **quoi ignorer**, et le correcteur revérifie chaque constat avant de le remonter. | Gabarit de grille pour les phases Spécification (critères d'acceptation) et Revue. | **Nouvelle** en tant que liste. « Quoi ignorer » confirme [claude-code-docs.md](claude-code-docs.md), idée 13 |
| 3 | **La consigne et la grille sont deux documents.** Le rédacteur lit la consigne, le correcteur lit la grille. Un rédacteur qui connaît la grille se note encore lui-même ; seul un contexte séparé force la revérification. Les deux documents doivent s'accorder sur l'emplacement et le format du livrable. La grille vit dans un fichier versionné. | Dans JDLC, la spec (pour l'implémenteur) et la grille de vérification (pour le vérificateur) sont deux fichiers distincts, relus ensemble. | Confirmation : vérificateur indépendant ([single-agent-loop-kit.md](single-agent-loop-kit.md), idées 16 et 26 ; [superpowers.md](superpowers.md), idée 4) |
| 4 | **Une boucle qui ne converge pas est un signal.** Si chaque itération bute sur le même type d'échec jusqu'au plafond, le rédacteur ne sait pas agir sur ce retour. Continuer coûte sans rien apporter. | Plafond d'itérations dans les boucles écrire-vérifier de JDLC, et arrêt avec escalade à l'humain quand le même échec revient. | **Nouvelle**. Rejoint « après deux corrections ratées, repartir » ([claude-code-docs.md](claude-code-docs.md), idée 8) |
| 5 | **Pas encore de grille ?** Donner à Claude un exemple réussi du livrable, lui faire dire ce qui le rend bon, puis transformer cette analyse en critères. C'est mieux que partir d'une page blanche. | Méthode pour amorcer les grilles JDLC à partir d'un artefact jugé bon par l'humain. | **Nouvelle** |

### Sur la revue de code

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 6 | **Trouver, puis trier, dans deux passes séparées.** La passe de recherche vise le rappel (tout remonter). Une passe de tri, dans un contexte neuf, relit le code, vérifie chaque constat ligne à ligne, regroupe les doublons par cause racine et **recalcule la gravité sans reprendre celle du chercheur**. Une grille de niveaux dit quoi signaler et quoi laisser passer. (`claude_agent_sdk/06_The_vulnerability_detection_agent.ipynb`) | Revue JDLC en deux temps : un relecteur large, puis un trieur indépendant qui filtre avant que l'humain ne lise. Moins de bruit à la porte humaine. | **Nouvelle**. Rejoint « recevoir une revue sans approbation de façade » ([superpowers.md](superpowers.md), idée 12) |
| 7 | **Un rapport de revue lisible par un programme** : un verdict dans une liste fermée, des constats avec un identifiant. Une revue de suivi liste ce qui est **résolu**, ce qui reste et ce qui est nouveau. Un verdict absent ou inconnu se lit comme « à examiner ». Une session sans verdict est « non relue », jamais « validée ». (`scheduled_repository_reviewer/`) | Format des rapports de revue JDLC et de la re-revue après correction : on suit des constats identifiés, pas des paragraphes. L'absence de preuve ne vaut jamais feu vert. | **Nouvelle** dans sa forme. Prolonge le retour structuré ([single-agent-loop-kit.md](single-agent-loop-kit.md), idée 18) |
| 8 | **Après une reprise, réobserver avant de conclure.** Un relecteur repris d'une session précédente répond de mémoire s'il ne relance pas la liste des fichiers ; il rate alors le fichier ajouté entre-temps. | Règle commune aux skills JDLC : après une reprise ou une compaction, relire l'état réel (fichiers, git, tests) avant toute conclusion. | **Nouvelle** formulation. Prolonge le principe 8 |
| 9 | **Un dépôt qu'on ne contrôle pas ne doit pas configurer son relecteur.** Le relecteur planifié tourne sans les réglages ni le `CLAUDE.md` du dépôt relu, avec des outils en lecture seule, un hook `PreToolUse` qui bloque les lectures hors du dépôt, des plafonds de tours et de budget, et un code de sortie non nul en cas d'échec. | Pour une éventuelle revue JDLC en CI ou sur une contribution externe : isoler le relecteur de la configuration du dépôt relu. | **Nouvelle** pour l'isolation de configuration. Confirme les runs scriptés et le relecteur en lecture seule ([claude-code-docs.md](claude-code-docs.md), idées 2 et 20) |
| 10 | **Partir du code, puis interroger l'humain.** L'agent lit le code et rédige un modèle de menaces avec une liste explicite de **questions auxquelles le code ne répond pas**. L'humain y répond, puis l'agent finalise le document. | Pour le cadrage d'un projet existant : l'agent rédige d'abord, puis pose seulement les questions que le code ne tranche pas. | Confirmation : « lire avant de demander » ([superpowers.md](superpowers.md), idée 15) ; interview → spec ([claude-code-docs.md](claude-code-docs.md), idée 3) |

### Sur l'orchestration

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 11 | **Les *dynamic workflows* de Claude Code.** Claude écrit un script JavaScript d'orchestration ; le moteur l'exécute en tâche de fond. Le script lance des sous-agents en parallèle ou par étapes, garde leurs résultats dans des variables et applique la logique entre les étapes. La question clé est **« qui tient le plan ? »** : Claude tour par tour (agent seul, sous-agents) ou le script. Le script garantit que chaque élément reçoit le même traitement et que la vérification a bien lieu. Il peut être enregistré dans `.claude/workflows/` et relancé par son nom. | Pour les phases JDLC qui traitent beaucoup d'éléments (revue de nombreux fichiers, audit, migration) : un workflow enregistré, livré avec JDLC, où la vérification est imposée par la structure. À réserver aux cas qui dépassent une fenêtre de contexte, car le coût en tokens est élevé. | **Nouvelle** (fonction absente des notes existantes). À vérifier dans la documentation au moment de construire, notamment la distribution par plugin |
| 12 | **Éclater puis contredire** (*fan-out + adversarial verification*) : un agent par élément vérifie dans un contexte propre, puis un sceptique conteste chaque verdict avant de l'accepter. Un faux signalement coûte une minute de relecture humaine ; une fausse validation coûte bien plus. | Justifie un vérificateur réglé pour la sévérité dans JDLC : les coûts d'erreur ne sont pas symétriques. | Confirmation : évaluateur sceptique ([anthropic.md](anthropic.md), A3, idée 10) ; orchestrateur-travailleurs (A1) |

### Sur l'évaluation de JDLC lui-même

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 13 | **Des dépôts d'essai piégés, avec un corrigé.** Les cookbooks posent des pièges connus : un `ARCHITECTURE.md` périmé que l'agent doit repérer au lieu de le répéter, des bugs plantés, un bug ajouté entre deux revues. L'atelier ajoute un **cas où le geste évident est faux** (un « double prélèvement » en réalité légitime), avec un fichier `GOLD.md` qui montre la sortie d'une consigne paresseuse et celle d'une consigne soignée. | Suite d'évals JDLC (`claude plugin eval`) faite de petits dépôts piégés : doc périmée, bug planté, demande trompeuse. Chacun a son corrigé écrit par l'humain. | **Nouvelle** dans sa forme. Concrétise l'évaluation de JDLC ([claude-code-docs.md](claude-code-docs.md), idée 15 ; [single-agent-loop-kit.md](single-agent-loop-kit.md), idées 7 à 9) |
| 14 | **Corriger sur le fond, tolérer la variation.** Le corrigé vérifie que les faits plantés sont trouvés, quelle que soit la formulation ou le découpage en constats. Un résultat plus strict que le corrigé est acceptable. Préférer une notation par code, puis par modèle (en relisant des échantillons), en dernier par un humain. Préférer beaucoup de cas imparfaits à peu de cas parfaits. | Règles de correction de la suite d'évals JDLC. | Confirmation : noter le résultat, pas le chemin ([single-agent-loop-kit.md](single-agent-loop-kit.md), idée 10) |

### Sur la mémoire et le contexte

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 15 | **Le « rêve » en pratique.** Une passe relit les transcriptions passées, **vérifie** ce que la mémoire affirme, l'**enrichit** (commandes exactes, pièges) et l'**organise** en petits fichiers liés avec un index. Un fait périmé est daté et remplacé, pas recopié. Les transcriptions sont des données, jamais des instructions. La mémoire est en **lecture seule** pendant le travail normal ; seule la passe de consolidation l'écrit. (`CMA_dreaming_with_a_dynamic_workflow.ipynb`) | Précise le rituel de consolidation de JDLC : faits datés, remplacement explicite, écriture réservée au rituel. | Confirmation : [anthropic.md](anthropic.md), A7, idée 7. **Nouvelle** pour la lecture seule hors consolidation |
| 16 | **Diagnostiquer avant de choisir.** Compaction, nettoyage des résultats d'outils et mémoire répondent à trois problèmes différents. Des instructions de compaction personnalisées **remplacent** celles par défaut : elles doivent nommer ce qui doit survivre. Une **session d'initialisation** peut poser la structure de la mémoire (journal de progression, liste de fonctionnalités) avant le travail. | Dans les instructions JDLC : dire ce que la compaction doit garder (phase en cours, chemins des artefacts, décisions ouvertes). | Confirmation : [anthropic.md](anthropic.md), A2 et A3, idées 8 et 9 |
| 17 | **Décrire les limites d'un agent depuis l'intention, pas depuis la configuration.** Sinon une erreur de configuration devient « autorisée » et la revue ne la voit plus. D'abord des règles déterministes, gratuites ; puis une relecture par le modèle. Un échantillon ne prouve pas que le reste est sain. (`CMA_agent_security_review.ipynb`) | Pour la relecture des sessions dans la phase Retour d'expérience : comparer ce que l'agent a fait à ce qu'on voulait, pas à ce qu'on a configuré. | **Nouvelle**. Rejoint le diagnostic sur transcriptions ([superpowers.md](superpowers.md), idée 6) |

### Sur les artefacts destinés à l'humain et la pédagogie

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 18 | **Une vue HTML pour les validations humaines.** Un plan avec jalons, flux de données et tableau des risques ; une PR annotée avec une **carte des risques par fichier** (« sûr », « à regarder », « demande attention ») ; une description de PR qui dit où concentrer la relecture. (html-effectiveness, fichiers `03`, `16`, `17`) | Aux portes humaines de JDLC (plan, revue, merge), produire en option une vue HTML qui oriente l'attention. Le Markdown reste la source versionnée. | **Nouvelle**. Sert la lutte contre la fatigue d'approbation ([anthropic.md](anthropic.md), idée 2) |
| 19 | **Enseigner par activation progressive.** Une seule tâche, rejouée en activant une capacité à la fois, pour voir chaque apport. Les distinctions qui embrouillent sont posées en tableaux et répétées : agent principal ou sous-agent, outil ou hook (qui le déclenche, le modèle le voit-il, à quoi il sert). (agent-sdk-workshop, `DESIGN.md`, `01-guided-demo/GUIDE.md`) | Pour la documentation pédagogique de JDLC : un exemple fil rouge, et des tableaux de distinction (skill ou sous-agent, consigne ou hook). | **Nouvelle** pour la pédagogie. Rejoint la classification consigne/garantie ([claude-code-docs.md](claude-code-docs.md), idée 10) |

## Ce qu'on écarte ou relativise

- **Managed Agents et l'API Messages.** La plupart des cookbooks montrent des API hébergées (sessions, budgets, versions de prompts, *advisor*, résidence des données). JDLC vise Claude Code en local ([ADR-0006](../adr/0006-claude-code-uniquement.md)). On garde les méthodes, pas les appels.
- **Les réglages de contexte de l'API** (`compact_…`, `clear_tool_uses_…`) ne sont pas pilotables depuis un plugin Claude Code. Seule l'idée des instructions de compaction est transposable.
- **Le « conseiller »** (un modèle moins cher consulte un modèle plus fort sur les décisions difficiles) est propre à Managed Agents. Dans Claude Code, l'équivalent est le choix du modèle par sous-agent. Pas une priorité.
- **`patterns/agents/`** reprend *Building effective agents*, déjà analysé. Rien de nouveau.
- **Les skills côté API** (`skills/`) montrent des exemples financiers et l'envoi par API. La documentation Claude Code et le kit [single-agent-loop-kit.md](single-agent-loop-kit.md) en disent plus.
- **Le coût des workflows** (idée 11) est élevé : plusieurs dollars pour un exemple de dix éléments. À réserver aux tâches qui dépassent vraiment une fenêtre de contexte.
- **La vue HTML** (idée 18) ajoute un artefact à maintenir. À tester sur une vraie porte humaine avant d'en faire une règle.

## Dépôts sans intérêt pour JDLC

- **evals** : jeux de données de recherche (2022) sur les comportements des modèles. N'enseigne pas comment écrire des évals pour un SDLC.
- **prompt-eng-interactive-tutorial** : cours de 2024 écrit pour Claude 3 Haiku. Ses conseils durables sont déjà dans la documentation officielle ; sans licence à la racine.
- **claude-agent-sdk-demos** : applications de démonstration hors développement logiciel. Seul le verdict à plusieurs niveaux de `research-agent/.claude/commands/fact-check.md` (vrai, faux, partiellement vrai, invérifiable, trompeur) rejoint l'idée 7 ; sans licence à la racine.

## Licence et réutilisation

- **claude-cookbooks (MIT)** : adaptation ou import possibles. Les meilleurs candidats sont les principes de grille (idées 1 et 2) et le schéma de rapport de revue (idée 7). Un import garde la mention « Copyright (c) 2023 Anthropic » et le texte MIT dans `licenses/`.
- **agent-sdk-workshop (Apache-2.0)** : import possible. Garder la licence, signaler les modifications ; pas de `NOTICE` au commit étudié.
- **html-effectiveness (MIT)** : les pages pourraient servir de gabarits HTML. Garder « Copyright (c) 2026 Anthropic PBC » et le texte MIT.
- **claude-agent-sdk-demos, prompt-eng-interactive-tutorial** : pas de licence à la racine, donc tous droits réservés. Niveau « idée » uniquement. Les skills `pdf` et `xlsx` des démos ont des licences propriétaires : ne rien en reprendre.
- **evals (CC BY 4.0)** : import possible avec attribution, mais rien à importer pour JDLC.
- Licences à revérifier au commit exact au moment d'un import.
