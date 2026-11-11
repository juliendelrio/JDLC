# AI-Driven Dev : framework et manifeste

La communauté française *AI-Driven Dev* publie deux ressources liées, étudiées ensemble ici :

- le **framework AIDD**, un SDLC installable sous forme de plugins ;
- le **manifeste AIDD**, des valeurs et des principes.

| | Framework | Manifeste |
|---|---|---|
| **Source** | https://github.com/ai-driven-dev/framework | https://www.ai-driven-development.org/ (dépôt : https://github.com/ai-driven-dev/manifest) |
| **Auteur** | AI-Driven Dev contributors | AI-Driven Dev |
| **Version étudiée** | commit `6f70b656faeeacb9ba96e51a6ab1e79ff830803b` (2026-10-26) | v1.1.1, commit `b4a7fe23c7b245d29a281a2e72191f439121c580` |
| **Consulté le** | 2026-11-11 | 2026-11-11 |
| **Licence** | **MIT**, « Copyright (c) 2026 AI-Driven Dev contributors » | Texte et documentation : **CC BY 4.0** ; code : **MIT** |
| **Niveau de reprise possible** | Idée, adaptation ou import (licence compatible) | Idée, adaptation ou import avec attribution (licence compatible) |
| **Niveau actuel** | Idée | Idée |

Pour un import futur depuis le manifeste, l'attribution demandée est : *« The Manifesto for AI-Driven Development — https://ai-driven-development.org — Licensed under CC BY 4.0 »*.

---

## 1. Le framework AIDD

### En bref

Le framework se présente comme un *« enterprise-grade SDLC »* qui s'installe dans l'outil de développement IA. Il fournit des skills, des agents, des commandes, des règles et des hooks, et emmène une idée brute jusqu'à une pull request relue.

- Contenu à la version étudiée : 9 plugins, 51 skills, 2 agents.
- Compatibilité : Claude Code nativement (marketplace) ; Cursor, Copilot, Codex, OpenCode et Kilo Code via une CLI qui traduit les plugins dans le format de chaque outil.
- Le contenu livré est du Markdown, sans dépendance à l'exécution. Seuls les hooks demandent Node.

### Installation et distribution

- Le dépôt **est** une marketplace Claude Code : un fichier `.claude-plugin/marketplace.json` liste les plugins rangés dans `plugins/<nom>/`.
- Installation : `/plugin marketplace add ai-driven-dev/framework`, puis `/plugin install <plugin>@aidd-framework`.
- Mise à jour : `/plugin marketplace update aidd-framework`.
- La marketplace et les plugins sont versionnés indépendamment ; les releases sont automatisées (release-please).
- Les manifestes sont validés contre les schémas publics (SchemaStore) dans un hook pre-commit et en CI.

### Découpage en plugins par responsabilité

| Plugin | Responsabilité | Couche |
|---|---|---|
| `aidd-context` | Connaissance du projet : onboarding, mémoire projet, génération de règles, skills, agents et hooks, apprentissage, exploration | Connaissance |
| `aidd-pm` | Produit : user stories, PRD, spec, epic, spike, three amigos, défauts | Connaissance |
| `aidd-refine` | Métacognition : brainstorm, *challenge*, zones d'ombre, *fact-check*, amélioration | Connaissance |
| `aidd-dev` | Code : plan, implémentation, assertions, audit, revue, tests, refactor, debug | Exécution |
| `aidd-vcs` | Git : commits, PR, tags, issues, conflits | Externe |
| `aidd-orchestrator` | Enchaînement du cycle complet, backlog, mode asynchrone | Coordination |
| `aidd-telemetry` | Comptage des tokens par tâche | Observation |

Chaque couche a une frontière stricte. Exemples :

- la couche *Connaissance* n'écrit jamais de code applicatif ;
- la couche *Observation* ne modifie rien.

### Le cycle (skill `aidd-orchestrator:01-sdlc`)

Trois zones mènent de la demande à une pull request en brouillon : **Frame → Deliver → Check**.

1. **Frame** — produit un **contrat** à partir de la demande ou d'un ticket.
   - Une demande est *prête à planifier* si elle nomme l'artefact à changer, décrit le résultat observable et ne laisse ouverte aucune décision qui changerait ce qui est construit.
   - Sinon, on clarifie l'intention (brainstorm) ou on formalise le contrat (spec).
2. **Deliver** — transforme le contrat en un plan **proportionné**.
   - Un agent *executor* implémente le plan, puis appelle les skills de validation (assertions, tests, parcours de bout en bout si le plan le prévoit).
   - Le travail validé est commité.
3. **Check** — un agent *checker* **neuf et indépendant** de l'implémentation relit le candidat au regard du contrat, du plan et des **preuves de validation**.
   - Il remet ensuite en question le résultat réel : est-il fiable, sert-il l'utilisateur ?
   - Les constats sont aiguillés selon leur nature : un problème de contrat repart en Frame, un problème d'implémentation repart en Deliver.

Le cycle a deux modes :

- **auto** (par défaut) : de la demande à la PR sans confirmation, sauf pour dépenser de l'argent, faire une action irréversible ou prendre une décision qui relève de l'utilisateur ;
- **interactive** : **trois pauses**, sur le contrat, sur le plan et sur le résultat. Un refus à une pause devient un constat contre l'artefact concerné.

### Autres mécanismes notables

- **Mémoire projet** (`aidd_docs/memory/`) : fichiers thématiques (architecture, tests, VCS, carte du code, décisions…), rafraîchis par un hook au démarrage de session.
- **Skill `learn`** : capture les leçons durables (décision, convention, piège, workflow réutilisable, constat de revue) et les range en mémoire, en ADR, en règle ou en skill. Rien n'est écrit sans validation de l'utilisateur.
- **Onboarding guidé** (`00-onboard`) : inspecte le projet et oriente vers la bonne étape.
- **Anatomie d'un skill** :
  - `SKILL.md` sert de routeur vers des fichiers `actions/`, `assets/` et `references/` ;
  - l'agent ne lit que l'action suivante, ce qui économise les tokens.
- **Règles transverses** récurrentes :
  - ne jamais inventer le contenu d'un fichier illisible ;
  - demander plutôt que de choisir un défaut en silence ;
  - n'écrire que ce que l'utilisateur a validé.
- **Contrats de délégation** : au plus deux niveaux de délégation, sans cycle ; un agent n'invoque jamais un orchestrateur.
- **Le projet s'applique son propre framework** : `aidd_docs/tasks/<mois>/<tâche>/` contient le `plan.md`, les `phase-N.md`, le `review.md` et parfois une rétrospective de chaque évolution.
- **Transparence** : section *Trust and safety* (les hooks s'exécutent avec les permissions de l'utilisateur) et fichier `THIRD_PARTY_LICENSES.md`.

## 2. Le manifeste AIDD

### En bref

*« A standard for building software in the age of AI — written by developers, for developers. »*

Le manifeste définit l'AIDD comme le fait de construire du logiciel avec l'IA comme partenaire délibéré, en restant l'architecte responsable. Il s'oppose au *vibe coding* : même vitesse, mais avec qualité, revue et maintenabilité. Il a été signé par environ 136 personnes, via des pull requests.

### Quatre valeurs (structure inspirée du Manifeste agile)

| Valeur | Idée |
|---|---|
| **Method over Model** | Miser sur la méthode, pas sur le modèle ; la méthode survit aux outils. |
| **Ownership over Delegation** | On est responsable de ce qu'on livre, même de ce que l'IA a écrit. |
| **Understanding over Acceptance** | Ne pas accepter ce qu'on ne comprend pas. |
| **Outcome over Output** | Mesurer le résultat utile, pas les lignes, tokens ou commits produits. |

### Douze principes (reformulés)

1. Livrer par incréments vérifiés.
2. Partir d'une spec claire.
3. Planifier avant de générer.
4. Découper jusqu'à ce que chaque tâche soit déléguable.
5. Traiter le contexte (specs, règles, mémoire) avec le même soin que le code.
6. Capitaliser les leçons pour les réutiliser.
7. L'humain approuve, merge et maintient chaque changement livré.
8. Ne pas déléguer ce qu'on ne sait pas évaluer.
9. Réparer le système avant de pousser le modèle plus fort.
10. Reconstruire le contexte quand la cohérence se dégrade.
11. Traiter un échec de l'IA comme un signal de diagnostic.
12. Rendre la pratique transmissible entre personnes et entre outils.

---

## Ce que JDLC peut en retenir

### Du framework

| # | Idée | Ce que ça donnerait dans JDLC | Lien avec l'existant |
|---|---|---|---|
| 1 | **Le dépôt est une marketplace Claude Code** | Précédent concret et éprouvé pour l'option 1 de l'ADR-0005 : `.claude-plugin/marketplace.json` à la racine, plugins dans `plugins/`, installation et mise à jour natives. | [ADR-0005](../adr/0005-mecanisme-installation.md), [ADR-0006](../adr/0006-claude-code-uniquement.md) |
| 2 | **Plugins découpés par responsabilité, en couches** | Découper JDLC selon des frontières nettes (connaissance / exécution / coordination) plutôt que par phase. Sans aller jusqu'à 9 plugins au départ. | Futur ADR de structure |
| 3 | **Notion de demande « prête à planifier »** | Critère d'entrée explicite pour passer du cadrage à la planification. | Phases Cadrage → Spécification |
| 4 | **Trois pauses optionnelles (contrat, plan, résultat) selon le mode** | Répond à la question ouverte « 2 ou 3 validations humaines ? » : trois points de validation, activables ou non selon le mode. | Principe 6, piste SFEIR |
| 5 | **Vérificateur indépendant et neuf** | La revue est faite par un agent qui n'a pas implémenté, au regard du contrat, du plan **et des preuves**. Prolonge directement la discipline de la preuve. | Principe 8, phase Revue |
| 6 | **Aiguillage des constats selon leur nature** | Un problème de spec repart au cadrage, un bug repart à l'implémentation. | Phases Revue & vérification |
| 7 | **Plan proportionné** | La lourdeur du plan s'adapte à la taille du changement : pas de cérémonie pour une petite correction. | Principe 7 (incrémental) |
| 8 | **Mémoire projet + skill `learn`** | Mémoire thématique dans le projet cible, et un mécanisme de capitalisation validé par l'humain qui range chaque leçon au bon endroit (mémoire, ADR, règle, skill). | Phase Retour d'expérience, piste SFEIR (capitalisation) |
| 9 | **Onboarding qui inspecte puis oriente** | Base de la future commande `init` (équivalent de la phase Setup de SFEIR). | ADR-0005 |
| 10 | **Skills routeurs avec actions chargées à la demande** | Gabarit de skill pour JDLC : économie de contexte, actions lisibles une par une. | Futur gabarit de skill |
| 11 | **Règles transverses** | Ne jamais inventer, demander plutôt que choisir un défaut en silence, n'écrire que ce qui est validé : candidates pour des règles communes à tous les skills JDLC. | Futur `rules` |
| 12 | **Appliquer JDLC à JDLC** | Tracer les évolutions de JDLC avec ses propres artefacts (plan, phases, revue) : c'est aussi une démonstration pédagogique. | Journal, ADR |
| 13 | **Section « Trust and safety » + `THIRD_PARTY_LICENSES.md`** | Documenter ce que JDLC exécute (hooks) et avec quelles permissions. | README, politique de licences |

### Du manifeste

| # | Idée | Ce que ça donnerait dans JDLC |
|---|---|---|
| 14 | **Ne pas déléguer ce qu'on ne sait pas évaluer** et **ne pas accepter ce qu'on ne comprend pas** | Candidat pour un principe directeur : complète « l'humain garde la main » par une exigence de compréhension. |
| 15 | **Le contexte est du code** | Les fichiers de contexte (CLAUDE.md, règles, mémoire, specs) sont versionnés, relus et maintenus comme du code. |
| 16 | **Réparer le système avant de pousser le modèle** et **l'échec de l'IA comme signal** | Même logique que la capitalisation SFEIR : un échec révèle un trou dans le contexte ou le processus, pas seulement une erreur du modèle. |
| 17 | **Reconstruire le contexte quand la cohérence se dégrade** | Hygiène de contexte : tâches découpées pour tenir dans la fenêtre, repartir d'un contexte propre plutôt que d'insister. |
| 18 | **Outcome over Output** | Mesurer JDLC à ce qu'il apporte (moins de retours, livraisons plus sûres), pas au volume produit. |
| 19 | **Croiser nos principes avec les 12 du manifeste** | Exercice pédagogique : montrer ce que JDLC partage et où il diverge. Signer le manifeste reste une option personnelle. |

## Ce qu'on écarte ou relativise

- **Le multi-outils** (CLI de traduction vers Cursor, Copilot, Codex…) : hors périmètre, JDLC cible uniquement Claude Code ([ADR-0006](../adr/0006-claude-code-uniquement.md)). Le principe 12 du manifeste (transmissible entre outils) n'est donc pas repris pour l'instant.
- **L'échelle** (9 plugins, 51 skills) et l'orientation **entreprise et équipe** (PM, three amigos, backlog, télémétrie) : JDLC démarre petit et solo.
- **Le mode auto par défaut** : pour JDLC, le mode supervisé est peut-être un meilleur défaut. Question à trancher à l'usage.

## Question stratégique soulevée

AIDD est un SDLC complet, open source (MIT), installable dans Claude Code, et proche de nos principes. Cela pose une question légitime : **pourquoi construire JDLC plutôt qu'utiliser AIDD ?** Trois pistes, non exclusives :

1. **Construire JDLC** pour apprendre et coller à ma pratique, en s'inspirant d'AIDD : c'est le choix actuel.
2. **Importer ou adapter** certains skills d'AIDD, ce que la licence MIT permet, en appliquant la procédure de [licences.md](../licences.md).
3. **S'appuyer sur AIDD comme dépendance** et faire de JDLC une surcouche.

À trancher dans un ADR quand le premier composant sera conçu.
