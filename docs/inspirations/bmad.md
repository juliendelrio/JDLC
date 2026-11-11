# BMad Method (BMAD-METHOD)

| | |
|---|---|
| **Source** | https://github.com/bmad-code-org/BMAD-METHOD (documentation : https://docs.bmad-method.org/) |
| **Auteur** | BMad Code, LLC et contributeurs |
| **Version étudiée** | branche `main`, commit `a867e1604b365d48ea03cbd109da1066ba0c84bc` (2026-11-08) : ligne **v7 en *preview alpha*** (modules déclarés en `6.13.0-next`). Ligne stable : v6.12.4 (2026-10-14), non étudiée en détail. |
| **Consulté le** | 2026-11-11 |
| **Licence** | **MIT**, « Copyright (c) 2025 BMad Code, LLC » (fichier `LICENSE` lu à ce commit). Le même fichier ajoute une **mention de marque** : *BMad*, *BMad Method*, *BMad Core* sont des marques de BMad Code, LLC, non couvertes par la licence MIT (détails dans `TRADEMARK.md`). C'est ce texte ajouté qui fait afficher `NOASSERTION` par les métadonnées GitHub. |
| **Niveau de reprise possible** | Idée, adaptation ou import (licence compatible), en conservant la mention de copyright **et** sans utiliser le nom « BMad » (ni variante) pour un produit, un skill ou un module JDLC. |
| **Niveau actuel** | Idée |

> Point d'attention : la v7 étudiée est explicitement instable (« expect breaking changes »), avec une sortie annoncée pour mi-novembre 2026. Les noms de skills et la structure des dossiers peuvent changer ; toute reprise future devra repartir d'un tag.

---

## Synthèse

### En bref

BMad se présente comme une méthode de *développement agile piloté par l'IA* : transformer une idée ou une demande de changement en logiciel qui fonctionne, *« sans renoncer à réfléchir »*. Le cœur de l'argument : les assistants de code transforment des hypothèses implicites en code ; BMad rend les décisions importantes **explicites** et les conserve comme **contexte durable** pour la suite.

Trois idées structurent la méthode :

- **le processus se dimensionne au travail** : une petite modification va directement à l'implémentation, un projet multi-epics reçoit toute la profondeur de planification ;
- **une seule unité d'implémentation** (une session *Build*), réutilisée à toutes les échelles ;
- **des perspectives spécialisées** (produit, architecture, UX, dev, test) incarnées par des agents nommés.

### Contenu et organisation du dépôt

- `skills/` : une trentaine de skills, regroupés en **modules** (`bmod-core-tools`, `bmod-method`, `bmod-toolsmith`…), chacun décrit par un `bmod.toml` (version, liste des skills, dépendances, migrations).
- Chaque skill contient un `SKILL.md` court, un `customize.toml` (points d'extension), souvent des `references/`, `assets/` (gabarits), `scripts/` Python et parfois des `evals/`.
- `docs/` : documentation organisée par usage (démarrer, planifier, construire, bases de code existantes, personnaliser, *toolsmith*).
- `tools/` : validation des skills, tests, procédure de release.

### Installation et distribution

- **Stable (v6.12)** : installeur npm (`npx bmad-method install`), qui écrit les skills dans le répertoire de l'outil (`.claude/skills/` pour Claude Code).
- **v7** : via la CLI `skills` (`npx skills add bmad-code-org/BMAD-METHOD`) ou via une **marketplace Claude Code** séparée (`/plugin marketplace add bmad-code-org/bmad-plugins`), qui propose les plugins `bmad-method` et `bmad-core-tools`.
- Après installation, le skill hub `bmad` exécute `bmad setup` (configuration du projet, nettoyage des skills renommés ou retirés) et `bmad status`.
- **Dépendance à l'exécution** : la plupart des skills lancent un script Python via `uv` (`render_skill.py`) qui *compile* le workflow à partir de gabarits et de la configuration du projet avant que l'agent ne le lise.
- Multi-outils : Claude Code, Codex, Cursor, Cline, etc.

### Le chemin de livraison

La documentation résume la méthode par une boucle : **Clarifier → Planifier → Construire et vérifier → Apprendre et ajuster**, avec trois points d'entrée selon la maturité de l'idée (idée vague, grande idée claire, petit changement).

**Critère central : l'intention est-elle bien définie ?** Une intention bien définie dit ce qui doit être vrai à la fin, ce qui ne doit pas changer et ce qui est hors périmètre, assez pour que quelqu'un d'autre construise sans deviner. Si ce n'est pas le cas, on utilise des outils indépendants (pas des étapes imposées) jusqu'à ce qu'elle le soit : brainstorming, mise à l'épreuve d'une idée, recherche documentée, brief, PRD, UX, architecture.

**La taille suit l'intention :**

| Taille | Chemin |
|---|---|
| Modification évidente et sans risque | Pas besoin de BMad |
| Tient dans une session | Une session `bmad-build` |
| Un résultat cohérent, plusieurs sessions | **Epic** : exigences, découpage en stories ordonnées, une session Build par story, rétrospective |
| Plusieurs epics (environ 20 sessions ou plus) | **Projet** : documents partagés (PRD, architecture…), initiative découpée en epics |

Le message clé : plus le travail est gros, plus on ajoute de **contexte partagé autour** de l'unité Build, mais on ne change jamais de système de livraison.

### L'unité d'implémentation : `bmad-build`

Le workflow est découpé en fichiers d'étapes chargés un par un (*just-in-time loading*) :

1. **Clarifier et router** : reprendre un plan existant ou démarrer ; vérifier que l'arbre Git est propre et la branche cohérente ; **contrôle multi-objectifs** (si la demande contient plusieurs livrables indépendants, proposer de les séparer et consigner les autres dans un fichier `deferred-work.md`).
2. **Planifier** : investiguer sans questionner l'humain, puis produire un plan selon un standard « prêt pour le développement » (tâches avec chemin de fichier, ordonnées, critères d'acceptation Given/When/Then vérifiables, aucune zone « à définir »). Les questions que l'investigation ne tranche pas deviennent une section *Open Questions*. **Point de contrôle 1** : l'humain approuve le plan (et continue ou s'arrête), ou demande une revue du plan.
3. **Implémenter** : en vérifiant que chaque ligne de la matrice des cas limites est couverte par un test qui a réellement tourné ; un test en désaccord avec la matrice ne se corrige jamais en modifiant l'attente.
4. **Revoir** : plusieurs « lentilles » en sous-agents parallèles (chasseur de cas limites, trous de vérification…), puis triage.
5. **Présenter** le résultat.

Une **route `oneshot`** existe pour les petites modifications, et `bmad-build-auto` exécute la même unité sans humain, pour un orchestrateur.

Mécanismes notables du plan :

- un bloc **figé après approbation** contient l'intention et les décisions validées : une fois le plan approuvé, seul l'humain peut le modifier ;
- un **standard de taille** pour le plan (un seul objectif utilisateur, environ 900 à 1 600 tokens), présenté comme une proposition et non comme une barrière.

### La revue et son triage

C'est l'un des mécanismes les plus travaillés :

- les sous-agents de revue ne reçoivent qu'un **chemin vers le diff**, jamais le diff dans le prompt, et pas le raisonnement de l'implémenteur ;
- le plan est traité comme un **témoignage, pas une preuve** : une lentille dédiée extrait ses affirmations et tente de les **réfuter** contre le code ;
- l'orchestrateur **ignore la sévérité** attribuée par les relecteurs et **vérifie lui-même** chaque constat (« le problème se produit-il vraiment ici ? »), avec un verdict unique : grave, tolérable, mineur, faux, peut-être faux ;
- chaque constat laisse une ligne dans un **journal de triage** dans le plan, rien n'est fusionné ou ignoré silencieusement ;
- les constats sont **aiguillés** : trou dans l'intention (retour à l'humain), mauvais plan (on re-planifie), correctif direct, ou report ;
- les boucles sont **plafonnées** : au-delà de cinq retours, escalade vers l'humain ;
- si aucun sous-agent n'est disponible, le workflow produit des prompts autonomes à faire exécuter par l'humain dans une autre session, idéalement avec un autre modèle.

### Agents (personas)

Six agents nommés, chacun avec un menu de codes : Analyste (Mary), Product Manager (John), Product Owner (Sofia), Architecte (Winston), Développeuse (Amelia), UX (Sally). Ils produisent une douzaine de types de documents (brief, PRFAQ, BRD, PRD, SRS, charte…). Un mode **party** fait débattre plusieurs personas entre eux, avec la consigne explicite de ne pas lisser les désaccords.

### Autres mécanismes notables

- **Théorie du contexte projet** (`bmad-project-context`, page *The Theory of Project Context*) : un fichier d'instructions ne doit contenir que ce qui est **coûteux à retrouver** dans le dépôt. Le comportement du code se relit dans le code ; l'**intention, la justification et ce qui a été délibérément rejeté** ne s'y trouvent pas. La page s'appuie sur des études montrant qu'un `AGENTS.md` qui répète la structure du dépôt n'améliore pas le taux de réussite et augmente le coût, alors qu'un index compact de connaissances absentes du modèle aide nettement. Le bloc de contexte est délimité par des marqueurs et porte le commit contre lequel il a été vérifié.
- **Personnalisation sans toucher aux fichiers installés** : chaque skill expose des points d'extension (faits persistants, étapes ajoutées avant ou après l'activation, gabarits) ; l'utilisateur écrit des fichiers de surcharge dans `_bmad/custom/`, fusionnés au moment du rendu. Les mises à jour n'écrasent donc pas les adaptations locales. Des **migrations** versionnées font passer un projet d'une version à l'autre.
- **Rétrospective d'epic sur preuves** : elle relit les plans, diffs et commits ; chaque constat cite une source (fichier, ligne, commit) ou est abandonné ; elle rend une **décision d'acceptation**. En mode sans humain, chaque hypothèse prise est consignée.
- **Élicitation avancée** : pousser un brouillon dans plusieurs directions (pré-mortem, red team, premiers principes, avocat de la partie adverse…) par des **lecteurs indépendants** qui ignorent le raisonnement de l'auteur, puis proposer une révision à accepter ou refuser point par point.
- **Évaluation des skills** (`bmad-eval`) : quatre modes, dont la comparaison **avec le modèle nu**. Règle affichée : un skill que le modèle nu égale doit être **retiré**, pas corrigé ; et comme les modèles progressent, la mesure est à refaire.
- **Conventions d'écriture des skills** (`AGENTS.md` du dépôt) : chaque instruction se paie à chaque exécution, donc on n'écrit pas d'instruction pour les cas exotiques, et l'on préfère **supprimer** une instruction plutôt que la nuancer.
- **Le projet s'applique sa méthode** : son propre `AGENTS.md` est géré par `bmad-project-context`.

---

## Ce que JDLC peut en retenir

### Idées qui confirment des pistes existantes

| # | Idée | Ce que ça donnerait dans JDLC | Convergence |
|---|---|---|---|
| 1 | **Critère d'« intention bien définie »** : ce qui doit être vrai, ce qui ne doit pas changer, ce qui est hors périmètre | Formulation concrète du critère d'entrée en planification, à trois rubriques. | AIDD (« prête à planifier »), Anthropic A3 (contrat avant de coder), SFEIR (porte Define) |
| 2 | **Processus dimensionné au travail**, y compris « pas de JDLC du tout » pour une modification évidente | Grille de tailles explicite (rien / une session / epic / projet). | AIDD (plan proportionné), principe 7, Anthropic A1 |
| 3 | **Approbation du plan comme porte humaine** | Confirme une porte « plan » distincte de la porte « spec ». | SFEIR (3 portes), AIDD (pause sur le plan), principe 6 |
| 4 | **Revue par des relecteurs indépendants, puis aiguillage selon la nature du constat** (intention / plan / correctif / report) | Même aiguillage qu'AIDD, avec une catégorie « report » en plus. | AIDD (checker neuf, aiguillage), Anthropic A3, principe 8 |
| 5 | **Rétrospective qui ne retient que des constats sourcés** | Règle pour la phase Retour d'expérience : pas de cause sans preuve citée. | Principe 8, SFEIR (Compound) |
| 6 | **Contexte minimal : n'écrire que ce qui est coûteux à retrouver** (intention, justification, options rejetées) | Règle de rédaction pour `CLAUDE.md` et la mémoire projet générés par JDLC. Les ADR sont précisément ce type de contenu. | Anthropic A2 et A7 (contexte injecté minimal), principe 10 |
| 7 | **Retirer un skill que le modèle nu égale** | Outille directement l'idée « JDLC doit pouvoir maigrir ». | Anthropic A3/A5 (chaque composant encode une hypothèse), A6 |
| 8 | **Chargement juste à temps des étapes** | Confirme le gabarit de skill routeur. | AIDD (anatomie des skills), Anthropic A2 |

### Idées nouvelles pour JDLC

| # | Idée | Ce que ça donnerait dans JDLC | Convergence |
|---|---|---|---|
| 9 | **Une seule unité d'implémentation à toutes les échelles** | JDLC définit d'abord *une* unité (une session : plan → code → revue → preuve) ; les échelles supérieures ne font qu'ajouter du contexte partagé et répéter l'unité. Bon candidat pour le premier composant (principe 7). | Prolonge AIDD (cycle unique) |
| 10 | **Bloc « figé après approbation » dans le plan** | Ce que l'humain a validé (intention, décisions) devient non modifiable par l'agent ; toute divergence remonte comme « trou d'intention ». Rend la porte humaine opposable. | Principe 6, Anthropic A4 (« ce que l'agent choisit seul n'est pas autorisé ») |
| 11 | **Le plan est un témoignage, pas une preuve** | Le relecteur tente de *réfuter* les affirmations du plan contre le code, après sa propre lecture du diff. Raffine la discipline de la preuve. | Principe 8, Anthropic A3 (auto-évaluation complaisante) |
| 12 | **L'orchestrateur vérifie chaque constat de revue et ignore la sévérité proposée** ; journal de triage exhaustif | Évite à la fois les faux positifs et les constats perdus ; trace auditable. | Anthropic A3 (évaluateur calibré) |
| 13 | **Plafond de boucles de correction puis escalade** | Limite explicite (par ex. N allers-retours) avant de rendre la main à l'humain. | Principe 6 |
| 14 | **Séparer les objectifs multiples et consigner le reste** dans un fichier de travail reporté | Contre la dérive de périmètre sans perdre les idées. | Anthropic A4, SFEIR (Build sans dérive) |
| 15 | **Surcharges locales séparées des fichiers installés**, fusionnées au chargement, plus des **migrations** versionnées | Réponse concrète au principe 5 (« mis à jour sans écraser les adaptations locales ») : à étudier pour l'[ADR-0005](../adr/0005-mecanisme-installation.md), en vérifiant ce que les plugins Claude Code permettent nativement. | Principe 5, ADR-0005 |
| 16 | **Élicitation par lecteurs indépendants** (pré-mortem, red team, prémisse…) | Un skill optionnel pour durcir une spec ou un ADR avant la porte humaine. | AIDD (`aidd-refine` : challenge, zones d'ombre) |
| 17 | **Repli sans sous-agent : prompt autonome à faire exécuter ailleurs** | Garantit l'indépendance de la revue même quand la délégation est impossible. | — |
| 18 | **Bloc de contexte délimité et daté par commit** | Le contexte généré dans le projet cible indique contre quelle version il a été vérifié, pour détecter qu'il est périmé. | Anthropic A7 (consolidation) |

## Ce qu'on écarte ou relativise

- **Les personas nommés et le jeu de rôle** (Mary, John, Sofia… et le mode *party*) : utile pour une équipe ou pour la pédagogie, mais coûteux en contexte et peu utile en usage solo. On garde l'idée de *perspectives* distinctes (produit, architecture, test), pas les personnages.
- **L'échelle documentaire d'organisation** (PRD, BRD, PRFAQ, SRS, charte, ticketing Jira/Linear/Trello…) : hors périmètre solo. La documentation de BMad elle-même dit qu'un PRD ne sert que si plusieurs personnes doivent s'accorder.
- **La dépendance à Python/`uv` au moment de l'exécution** et le rendu de gabarits Jinja : puissant (configuration, surcharges), mais contraire à l'objectif d'un contenu majoritairement Markdown sans dépendance. À ne reprendre que si les surcharges l'exigent vraiment.
- **Le multi-outils** (une dizaine de répertoires cibles) : hors périmètre ([ADR-0006](../adr/0006-claude-code-uniquement.md)).
- **L'instabilité de la v7** : on retient les idées, pas les noms ni la structure de fichiers, susceptibles de changer.
- **La marque** : aucun élément de JDLC ne doit porter le nom « BMad » ou une variante (voir `TRADEMARK.md` du dépôt). Créditer et décrire la filiation reste autorisé.

## Sources secondaires à étudier

- La page *The Theory of Project Context* et les études qu'elle cite (effet des fichiers `AGENTS.md` sur le taux de réussite et le coût) : à lire à la source avant de s'en servir comme argument.
- `docs/build/autonomous-development-loops.md` et le module **BMad Loop** (https://github.com/bmad-code-org/bmad-loop) : exécution d'un epic sans surveillance, utile si JDLC envisage un mode autonome.
- `docs/toolsmith/` (écrire un bon skill, motifs de sous-agents, évaluations) : guide d'écriture de skills à comparer avec celui de Superpowers et d'Anthropic.
- La ligne stable **v6.12** (branche `V6.12`), si l'on veut une référence moins mouvante.
- La marketplace https://github.com/bmad-code-org/bmad-plugins : les métadonnées GitHub n'y détectent **aucune licence** (non vérifié au niveau des fichiers) ; à contrôler avant toute reprise depuis ce dépôt.
