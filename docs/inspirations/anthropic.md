# Anthropic : publications sur le développement agentique

Anthropic publie sur son blog *Engineering* / *Research* une base de connaissances riche sur la conception d'agents. Comme JDLC cible Claude Code ([ADR-0006](../adr/0006-claude-code-uniquement.md)), ces publications sont doublement pertinentes : elles décrivent les principes **et** l'outil sur lequel JDLC repose.

| | |
|---|---|
| **Éditeur** | Anthropic PBC |
| **Consulté le** | 2026-11-11 |
| **Licence** | © 2026 Anthropic PBC ; aucune licence de réutilisation indiquée sur les pages |
| **Niveau de reprise autorisé** | **Idée uniquement** : synthèses reformulées, citations courtes et attribuées ([politique](../licences.md)) |

## Articles étudiés

| # | Article | Auteur(s) | Date | Fraîcheur |
|---|---|---|---|---|
| A1 | [Building effective agents](https://www.anthropic.com/research/building-effective-agents) | Erik S., Barry Zhang | 2024-12-19 | Daté (une note de la page le signale) |
| A2 | [Effective context engineering for AI agents](https://www.anthropic.com/research/effective-context-engineering-for-ai-agents) | P. Rajasekaran, E. Dixon, C. Ryan, J. Hadfield | 2025-09-29 | Daté |
| A3 | [Harness design for long-running application development](https://www.anthropic.com/research/harness-design-long-running-apps) | Prithvi Rajasekaran | 2026-03-24 | Récent |
| A4 | [How we built Claude Code auto mode](https://www.anthropic.com/research/claude-code-auto-mode) | John Hughes | 2026-03-25 | Récent |
| A5 | [Scaling Managed Agents: Decoupling the brain from the hands](https://www.anthropic.com/research/managed-agents) | L. Martin, G. Cemaj, M. Cohen | 2026-04-08 | Récent |
| A6 | [An update on recent Claude Code quality reports](https://www.anthropic.com/research/april-23-postmortem) (post-mortem) | Équipe Engineering | 2026-04-23 | Récent |
| A7 | [The components of agent memory](https://www.anthropic.com/research/components-of-agent-memory) | Lance Martin | 2026-11-06 | Très récent |

---

## Synthèses

### A1 — Building effective agents (2024)

- **Workflows ou agents.** Un *workflow* orchestre le modèle et les outils selon des chemins de code prédéfinis. Un *agent* dirige lui-même son processus et ses outils, en boucle, guidé par le retour de son environnement.
- **Commencer simple.** Les meilleures réalisations observées utilisent des briques simples et composables plutôt que des frameworks lourds. On n'ajoute de la complexité que si elle améliore *de façon démontrée* le résultat.
- **Cinq motifs de workflow :**
  - **chaînage de prompts**, avec des points de contrôle entre étapes ;
  - **routage** vers des traitements spécialisés ;
  - **parallélisation**, par découpage en sous-tâches ou par vote (plusieurs passes de revue) ;
  - **orchestrateur-travailleurs**, où l'orchestrateur décide des sous-tâches ;
  - **évaluateur-optimiseur**, une boucle où un modèle produit et un autre évalue.
- **Trois principes pour les agents :**
  - la simplicité ;
  - la transparence (rendre la planification visible) ;
  - le soin de l'interface agent-machine (*ACI*), aussi grand que celui d'une interface homme-machine.
- **Le code se prête bien aux agents**, car il est vérifiable par des tests. La revue humaine reste cruciale pour l'alignement avec le reste du système.
- **Concevoir les outils comme pour un humain.** Les décrire comme on écrirait une docstring pour un développeur junior, les tester, et les rendre résistants aux erreurs (*poka-yoke*). Exemple : imposer des chemins absolus a supprimé une classe d'erreurs.

### A2 — Effective context engineering (2025)

- **Du prompt au contexte.** La question devient : *quelle configuration de contexte* produit le comportement voulu ? Le contexte englobe le prompt système, les outils, les données et l'historique.
- **Le contexte est une ressource finie.** Le modèle dispose d'un « budget d'attention » : plus le contexte grossit, plus le rappel se dégrade (*context rot*). Cette dégradation est progressive, pas brutale.
- **Principe directeur :** viser le plus petit ensemble de tokens à fort signal.
- **Instructions à la « bonne altitude ».** Ni logique codée en dur et fragile, ni consignes vagues. On part d'un prompt minimal et on ajoute des instructions *à partir des échecs observés*. Minimal ne veut pas dire court.
- **Outils et exemples.** Les outils doivent être peu nombreux, sans recouvrement et économes en tokens. Les exemples doivent être canoniques et variés, plutôt qu'une liste de cas limites.
- **Contexte « juste à temps ».** L'agent garde des références légères (chemins, requêtes) et charge le contenu à la demande, par divulgation progressive. Claude Code suit une approche hybride : `CLAUDE.md` chargé d'emblée, exploration par glob et grep ensuite.
- **Trois techniques pour les tâches longues :**

  | Technique | Principe | Adaptée à |
  |---|---|---|
  | Compaction | Résumer la conversation, puis repartir de ce résumé | Garder un fil de conversation |
  | Prise de notes structurée | Notes persistantes hors de la fenêtre : todo, `NOTES.md` | Avancer par jalons successifs |
  | Sous-agents | Travail dans un contexte propre, résumé condensé de 1 000 à 2 000 tokens rendu à l'agent principal | Recherches menées en parallèle |

### A3 — Harness design for long-running apps (2026)

- **Séparer la génération de l'évaluation.** Un agent tend à louer son propre travail, même médiocre. Il est plus facile de rendre sceptique un évaluateur distinct que de rendre un générateur autocritique.
- **Rendre la qualité évaluable.** On donne au générateur et à l'évaluateur des critères explicites et pondérés, et on calibre l'évaluateur avec des exemples notés. Le vocabulaire des critères oriente fortement le résultat.
- **Trois agents pour les applications complètes :**
  - un **planificateur** qui transforme une demande courte en spec produit, en restant à haut niveau pour qu'une erreur précoce ne se propage pas ;
  - un **générateur** qui travaille par sprints, une fonctionnalité à la fois, avec git ;
  - un **évaluateur** qui teste l'application en marche comme un utilisateur (Playwright), avec des seuils éliminatoires.
- **Contrats de sprint.** Avant de coder, le générateur et l'évaluateur s'accordent sur ce que « terminé » veut dire et sur la façon de le tester. Ils communiquent par fichiers.
- **Chaque composant encode une hypothèse** sur ce que le modèle ne sait pas faire seul. Ces hypothèses se périment quand les modèles progressent : on les réexamine à chaque nouveau modèle et on retire, *un par un*, les composants devenus inutiles.
- **L'évaluateur n'est rentable qu'aux limites des capacités du modèle.** Il est inutile sur ce que le modèle réussit déjà seul.
- **Les ordres de grandeur sont parlants.** Pour un même prototype de jeu, un agent seul a mis 20 min pour 9 $ et a livré une application qui ne réagissait pas aux commandes. Le harness complet a mis 6 h pour 200 $, avec un résultat bien plus abouti.

### A4 — Claude Code auto mode (2026)

- **Fatigue d'approbation.** Les utilisateurs acceptent 93 % des demandes de permission : à force de répétition, ils ne lisent plus. Les deux alternatives existantes sont imparfaites : le sandbox demande de la configuration, et le contournement total des permissions n'offre aucune protection.
- **Modèle de menace** : quatre façons pour un agent de faire une action dangereuse.
  1. Il est **trop zélé** et dépasse ce qui a été autorisé.
  2. Il fait une **erreur de bonne foi** et sous-estime l'étendue des dégâts possibles.
  3. Il subit une **injection de prompt**.
  4. Le modèle est **mal aligné** (non observé en pratique, mais évalué).
- **Permissions par paliers :**
  - lecture et navigation : libres ;
  - modifications de fichiers dans le projet : libres, car relisibles via git ;
  - tout le reste : soumis à un classifieur qui ne voit que les messages de l'utilisateur et les appels d'outils, pas les justifications de l'agent, pour qu'il ne se laisse pas convaincre.
- **Principe d'autorisation.** Ce que l'agent choisit de lui-même n'est pas autorisé tant que l'utilisateur ne l'a pas dit. Par défaut, seul le dépôt courant est de confiance.
- **Refuser sans arrêter.** Une action bloquée revient à l'agent avec une consigne pour trouver une voie plus sûre. Après 3 refus consécutifs ou 20 au total, la main repasse à l'humain.
- **Transparence sur les limites.** L'article annonce 17 % de faux négatifs sur les actions trop zélées réelles et le présente comme « le chiffre honnête ». Le mode auto ne remplace pas une revue humaine attentive sur une infrastructure sensible.

### A5 — Managed Agents (2026)

- **Des interfaces stables, des implémentations jetables.** Les *harness* (les boucles qui pilotent le modèle et ses outils) encodent des hypothèses sur les limites du modèle, et ces hypothèses se périment. On s'engage donc sur la forme des interfaces, pas sur ce qui tourne derrière.
- **Trois composants découplés :**
  - la **session**, journal en ajout seul de tout ce qui s'est passé ;
  - le **harness**, la boucle qui appelle le modèle et ses outils ;
  - le **sandbox**, l'environnement d'exécution.
- **Ne pas adopter d'« animal de compagnie ».** Un conteneur unique dont la perte fait perdre la session est fragile. Mieux vaut des environnements jetables et une session durable, rangée à l'extérieur.
- **La session n'est pas la fenêtre de contexte.** La compaction et le tri du contexte font des choix irréversibles. Garder le journal complet hors de la fenêtre permet d'y revenir quand un besoin imprévu apparaît.
- **Sécurité.** Les identifiants ne sont jamais accessibles depuis le sandbox où s'exécute le code généré.

### A6 — Post-mortem d'avril 2026 sur la qualité de Claude Code

- **Trois changements distincts** ont dégradé la qualité perçue de Claude Code pendant plusieurs semaines :
  - l'effort de raisonnement par défaut a été abaissé ;
  - un bug d'optimisation de cache effaçait le raisonnement antérieur à chaque tour ;
  - une instruction de concision ajoutée au prompt système a fait perdre 3 % sur une évaluation.
- **Ces changements étaient invisibles pour les garde-fous.** Le bug a passé revues humaines et automatiques, tests unitaires et de bout en bout, et usage interne. Leur cumul, sur des populations différentes, ressemblait à une dégradation diffuse.
- **Les leçons :**
  - un changement d'instructions est un changement de comportement ; il mérite une suite d'évaluations et des ablations, c'est-à-dire mesurer l'effet de chaque ligne ;
  - tout ce qui est spécifique à un modèle doit être limité à ce modèle ;
  - pour un changement qui peut coûter en intelligence : période d'observation et déploiement progressif ;
  - utiliser soi-même la version exacte publiée, pas une version interne ;
  - une revue de code avec le contexte complet du dépôt aurait trouvé le bug ;
  - les retours utilisateurs sont un signal à prendre au sérieux, même quand les évaluations ne voient rien.

### A7 — The components of agent memory (2026)

- **Hiérarchie de la mémoire**, du bas vers le haut :
  1. les **transcriptions**, journal consultable ;
  2. les **notes** écrites par l'agent ;
  3. les **représentations**, par exemple un wiki ;
  4. le **contexte injecté** à chaque session.
- **Écrire une note, c'est prédire.** On parie qu'un motif reviendra et sera utile.
- **Plus de mémoire n'est pas mieux.** Dans l'expérience Pokémon, Sonnet 3.7 écrit 413 fichiers de mémoire, périmés et redondants, et gagne 1 badge. Fable 5 en écrit une dizaine, orientés stratégie, et gagne 4 badges.
- **Expliquer à quoi sert la mémoire** fonctionne mieux que des règles rigides. Une règle « ne noter que les échecs » a *dégradé* les résultats des modèles récents.
- **Le « rêve » (*dreaming*).** C'est une consolidation hors tâche : relire les transcriptions pour corriger les notes fausses, combler les trous et construire des représentations. Une note erronée corrigée ainsi a débloqué une progression.
- **Le contexte injecté doit rester petit.** Une note fausse s'y propage partout, et des fichiers d'instructions trop longs sont moins bien suivis. Claude Code ne charge que les 200 premières lignes ou les 25 Ko de `MEMORY.md`.
- **Les écritures concurrentes s'écrasent.** Une page par sujet évite la perte de données quand plusieurs agents écrivent en parallèle.
- **Questions ouvertes :**
  - quand écrire, sachant qu'écrire pendant la tâche distrait de la tâche ;
  - comment garder la mémoire à jour ;
  - quel compromis entre représentations entretenues et simple recherche dans les transcriptions.

---

## Ce que JDLC peut en retenir

### Idées qui renforcent des choix existants

| Idée Anthropic | Renforce | Sources convergentes |
|---|---|---|
| Séparer génération et évaluation ; l'auto-évaluation est complaisante (A3) | Principe 8 (preuve), vérificateur indépendant | AIDD (*checker* neuf), SFEIR (revues parallèles) |
| Contrat de sprint : définir « terminé » et le test *avant* de coder (A3) | Notion de contrat / demande prête à planifier | AIDD (Frame → contrat), SFEIR (Define) |
| Partir minimal, ajouter des instructions à partir des échecs observés (A2) | Principe 11 (réparer le système) | Manifeste AIDD (l'échec comme signal) |
| Instructions, mémoire et outils sont des artefacts qui changent le comportement (A2, A6) | Principe 10 (le contexte est du code) | Manifeste AIDD |
| Commencer simple, complexifier seulement si c'est démontré utile (A1, A3) | Principe 7 (incrémental) | AIDD (plan proportionné) |
| Divulgation progressive, contexte juste à temps (A2) | Skills qui chargent leurs actions à la demande | AIDD (anatomie des skills) |
| Une fonctionnalité à la fois (A3) | Implémentation par tranches | SFEIR (Build sans dérive) |

### Idées nouvelles pour JDLC

| # | Idée | Ce que ça donnerait dans JDLC | Source |
|---|---|---|---|
| 1 | **Chaque composant encode une hypothèse sur les limites du modèle** | Documenter pour chaque composant JDLC (skill, agent, hook, validation) *l'hypothèse qu'il compense*. À chaque nouveau modèle, faire une **revue de JDLC** : retirer un par un les composants devenus inutiles. JDLC doit pouvoir *maigrir*. | A3, A5 |
| 2 | **Fatigue d'approbation** | Argument fort pour **peu de validations humaines, mais significatives** : trop de validations tuent la validation. Éclaire le choix 2 ou 3 portes. | A4 |
| 3 | **Permissions par paliers et modèle de menace** | Fournir dans JDLC une configuration de permissions recommandée pour les projets cibles (lecture libre, édition du projet libre car relisible, le reste encadré), en raisonnant sur les quatre risques : zèle, erreur de bonne foi, injection, désalignement. | A4 |
| 4 | **« Ce que l'agent choisit seul n'est pas autorisé »** | Règle transverse de JDLC contre la dérive de périmètre : tout ce qui dépasse la demande est proposé, pas fait. | A4 |
| 5 | **Changer JDLC est un changement de comportement** | Faire évoluer les skills et instructions JDLC avec prudence : mesurer l'effet d'un changement, limiter au modèle concerné ce qui lui est spécifique, utiliser soi-même la version publiée de JDLC (*dogfooding*). À terme, une petite suite d'évaluations de JDLC. | A6 |
| 6 | **Hiérarchie de mémoire et contexte injecté minimal** | Organiser la mémoire projet en couches : journal, notes, synthèses, contexte injecté. Garder `CLAUDE.md` et la mémoire injectée **courts** ; expliquer à l'agent *à quoi sert* la mémoire plutôt que lui imposer des règles d'écriture. | A7, A2 |
| 7 | **Consolidation périodique de la mémoire (« rêve »)** | Un rituel ou skill de consolidation : relire les leçons accumulées, corriger les notes fausses, fusionner les doublons, promouvoir ce qui est stable vers les règles. C'est la version outillée de notre phase « Retour d'expérience ». | A7 |
| 8 | **Notes structurées et sous-agents pour les tâches longues** | Pour les tâches qui dépassent une session : fichier de progression dans le projet (plan, état, décisions) et sous-agents qui rendent des résumés condensés. | A2 |
| 9 | **Le journal durable hors de la fenêtre de contexte** | Les artefacts de chaque tâche (contrat, plan, preuves, revue) sont écrits dans des fichiers : on peut y revenir après compaction ou changement de session. | A5 |
| 10 | **Évaluateur calibré** | Donner à l'agent de revue des critères explicites, pondérés et calibrés par des exemples, avec des seuils éliminatoires. Le régler pour le scepticisme. | A3 |
| 11 | **Évaluateur proportionné** | Ne déclencher la vérification lourde que lorsque la tâche est à la limite de ce que le modèle réussit seul. Les petites tâches reçoivent une vérification légère. | A3 |
| 12 | **Concevoir les skills comme des interfaces** | Descriptions claires, cas d'usage et contre-indications, entrées résistantes aux erreurs (*poka-yoke*), testés sur plusieurs demandes. | A1, A2 |
| 13 | **Les cinq motifs de workflow comme vocabulaire** | Décrire chaque phase JDLC avec ce vocabulaire : chaînage avec points de contrôle, routage, parallélisation, orchestrateur-travailleurs, évaluateur-optimiseur. Pédagogique, et aide à choisir le motif le plus simple. | A1 |
| 14 | **Les retours d'usage sont un signal** | Garder une trace des moments où JDLC a mal fonctionné : ils alimentent la revue de JDLC (idée 1) et le journal. | A6 |

## Ce qu'on écarte ou relativise

- **L'infrastructure** (Managed Agents, sessions hébergées, sandboxes distants) : hors périmètre pour un usage solo dans Claude Code. On garde les principes (interfaces stables, journal durable, identifiants hors de portée) sans l'architecture.
- **Les chiffres** (scores de benchmarks, coûts, taux d'erreur) : ils illustrent des ordres de grandeur, dans un contexte précis, et datent vite.
- **A1 et A2 sont datés** : les motifs restent valables, mais les recommandations d'outillage ont évolué. A3, A5 et A7 montrent d'ailleurs que les composants d'un harness se périment avec les modèles.

## Pour aller plus loin

Le reste de la base de connaissances Anthropic (documentation Claude Code, bonnes pratiques, skills, plugins, *cookbooks* sur la mémoire et le *dreaming*) est à explorer au moment de concevoir les composants concrets. Cette documentation sera consultée dans sa version à jour plutôt que de mémoire.
