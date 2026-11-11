# Google : « The New SDLC With Vibe Coding »

Livre blanc publié par Google dans le cadre du cours Kaggle *5-Day AI Agents Intensive* (édition « vibe coding », juin 2026). C'est le support du jour 1 ; d'autres livres blancs de la série (contexte, sessions et skills au jour 3 ; développement piloté par la spec au jour 5) sont cités mais non étudiés ici.

| | |
|---|---|
| **Source** | Page officielle : https://www.kaggle.com/whitepaper-the-new-SDLC-with-vibe-coding (contenu non lisible sans navigateur). PDF lu : copie hébergée par Readwise, https://readwise-assets.s3.amazonaws.com/media/wisereads/articles/the-new-sdlc-with-vibe-coding/1317.pdf. Billet de présentation de l'auteur : https://addyosmani.com/blog/new-sdlc-vibe-coding/ |
| **Auteurs** | Addy Osmani, Shubham Saboo, Sokratis Kartakis (Google). Contributeurs : Elia Secchi, Julia Wiesinger, Anant Nawalgaria |
| **Version étudiée** | PDF de 51 pages daté « May 2026 » en pied de page (métadonnées du fichier : 2026-06-18). Le billet d'Osmani, daté du 2026-06-16, annonce la publication « cette semaine » |
| **Consulté le** | 2026-11-11 |
| **Licence** | **Aucune licence ni mention de copyright** dans le PDF ; aucune lisible sur la page Kaggle. Le billet d'Osmani porte « © Copyright 2026 Addy Osmani » et autorise informellement la réutilisation de ses figures (« Reuse the figures wherever you like »), ce qui ne couvre pas le texte du livre blanc |
| **Niveau de reprise possible** | **Idée uniquement** (aucune licence = tous droits réservés, [politique](../licences.md)) : synthèses reformulées, citations courtes et attribuées |
| **Niveau actuel** | Idée |

> Réserve : la copie lue n'est pas hébergée par Google. Son contenu est cohérent avec le billet de l'auteur, mais une vérification sur la page Kaggle (dans un navigateur) reste à faire avant toute citation précise.

---

## Synthèse

### Thèse

Le passage le plus profond en ingénierie logicielle n'est pas un nouveau langage, mais le passage **de l'écriture du code à l'expression de l'intention**. Le livre blanc veut fournir des modèles mentaux durables plutôt qu'un état des outils. Sa formule de conclusion : *« Generation is solved. Verification, judgment, and direction are the new craft. »*

### Un spectre, pas une opposition

Le *vibe coding* (décrire, accepter, recoller l'erreur dans le prompt) et l'*agentic engineering* sont les deux bouts d'un spectre, avec au milieu un codage assisté structuré. Ce qui distingue les positions n'est pas l'usage de l'IA, mais **la quantité de structure, de vérification et de jugement humain** autour de ce qu'elle produit. Le tableau du livre blanc compare cinq dimensions : spécification de l'intention, vérification, compréhension du code, traitement des erreurs, périmètre et risque.

- La bonne position dépend de l'enjeu : un prototype du week-end peut être « vibe codé », une API de paiement non.
- **Le différenciateur principal est la vérification.** Les tests vérifient la partie déterministe ; les *evals* vérifient la partie non déterministe (bonne trajectoire, bons outils, réponse finale au niveau attendu). Sans les deux, on reste dans le *vibe coding*, quelle que soit la qualité des prompts.

### Ingénierie du contexte

- La qualité du code généré dépend moins de l'habileté des prompts que de la **qualité du contexte**.
- Six types de contexte : instructions, connaissances, mémoire (court et long terme), exemples, outils, garde-fous.
- **Contexte statique ou dynamique.** Le statique (prompt système, `CLAUDE.md`/`AGENTS.md`, mémoire globale) est toujours chargé : fiable mais coûteux, et il dilue le signal. Le dynamique (skills, résultats d'outils, documents) n'est chargé qu'au besoin. La frontière entre les deux est une **décision d'architecture** à part entière, relue et versionnée.
- Les **skills** sont présentées comme le motif principal du contexte dynamique : métadonnées légères au démarrage, instructions complètes quand la tâche correspond, références profondes seulement si nécessaire (divulgation progressive).
- La bonne question n'est pas « comment piéger l'IA pour qu'elle code bien ? » mais « que devrait savoir un nouveau membre de l'équipe, et comment l'encoder pour l'IA ? ».

### Le cycle de vie compressé, mais inégalement

L'IA réduit l'implémentation de semaines à des heures, alors que les exigences, l'architecture et la vérification gardent un rythme humain. Le résultat n'est pas une version accélérée de l'ancien SDLC mais un autre flux, aux frontières de phases floues, où le développeur passe d'implémenteur à **concepteur du système et arbitre de la qualité**. Phase par phase :

- **Exigences** : elles deviennent une conversation qui produit en même temps la spec et une première implémentation.
- **Architecture** : la phase la plus humaine, car faite de compromis dépendant du contexte ; l'IA excelle à *appliquer* une architecture documentée.
- **Implémentation** : gains réels mais nuancés (le livre blanc cite des enquêtes à +25-39 % et une étude METR où des développeurs expérimentés étaient 19 % plus lents sur certaines tâches). Le travail se déplace de l'écriture vers la relecture et la vérification.
- **Tests** : évaluer le résultat *et* la trajectoire, car une sortie fluide qui a sauté ses vérifications est plus dangereuse qu'une erreur visible. Les tests et evals deviennent **le moyen principal de communiquer l'intention** à l'agent. Boucle d'amélioration : évaluer, regrouper les causes d'échec, corriger prompts ou outils, vérifier en régression, surveiller.
- **Revue et déploiement** : l'IA en premier relecteur, sans remplacer le jugement humain sur la conception et la maintenabilité.
- **Maintenance** : le code « trop risqué pour y toucher » redevient modifiable.

### Le modèle de l'usine

Le **produit principal du développeur n'est plus le code, mais le système qui produit le code** : spécifications et contexte, agents, tests et portes de qualité, boucles de retour qui renvoient les échecs aux agents, garde-fous. On donne aux agents des **critères de succès** plutôt que des instructions pas à pas, et on les laisse itérer. (L'idée reprend un billet antérieur d'Osmani, *The Factory Model*.)

### Agent = Modèle + Harness

- Le modèle n'est qu'un moteur ; tout ce qui l'entoure est le **harness** : fichiers d'instructions et skills, outils (MCP), sandboxes, orchestration (sous-agents, routage), **hooks** (code déterministe aux points clés du cycle : avant un appel d'outil, après une édition, avant un commit, *« pour ce que l'agent ne devrait jamais oublier mais oublie souvent »*), observabilité (logs, traces, evals, coûts).
- Ce harness est la surface de l'équipe, pas du fournisseur du modèle. Le billet d'Osmani résume la proportion par un ordre de grandeur « 10 % modèle, 90 % harness ».
- Le harness intervient dans chaque phase : il est **configuré** au cadrage, **exécuté** à l'implémentation, il porte la **boucle de retour** aux tests, il est **observé** en revue, déploiement et maintenance.
- On peut faire du *vibe coding* ou de l'*agentic engineering* avec le même agent : la différence tient à la délibération avec laquelle on configure le harness.
- **La plupart des échecs d'agent sont des échecs de configuration** (outil manquant, règle vague, garde-fou absent, contexte bruité), pas des échecs du modèle. Exemples cités : un changement de harness seul a fait passer un agent du top 30 au top 5 d'un benchmark.

### Le rôle du développeur

- Deux modes entre lesquels on alterne : **chef d'orchestre** (*conductor*, en direct, contrôle fin, pour la logique complexe ou le code inconnu) et **orchestrateur** (asynchrone, délégation à plusieurs agents, pour les tâches bien définies).
- Le mode orchestrateur demande quatre compétences : **spécifier** sans ambiguïté, **découper** en unités de bonne taille, **évaluer** vite, **concevoir** les contraintes et boucles de retour.
- **Le problème des 80 %** : l'agent produit vite 80 % d'une fonctionnalité ; les 20 % restants (cas limites, erreurs, intégration, exactitude subtile) demandent un contexte qu'il n'a pas. Les erreurs sont devenues conceptuelles (mauvaises hypothèses métier, absence de demande de clarification), donc plus difficiles à voir, car le code « a l'air juste ».

### Économie

- *Vibe coding* : faible investissement initial, coût d'exploitation élevé (tokens gaspillés en boucles de correction, dette de maintenance, failles de sécurité).
- *Agentic engineering* : investissement initial élevé (schémas, suites de tests, structuration du contexte), coût marginal par fonctionnalité bien plus bas.
- L'ingénierie du contexte est aussi un **levier financier** (meilleur taux de réussite au premier essai), tout comme le **routage de modèles** : gros modèles pour les exigences, l'architecture et l'implémentation initiale ; petits modèles pour les tâches plus déterministes.

### Par où commencer (pour un développeur seul)

1. Créer un fichier d'instructions de **dix lignes** (stack, conventions, règles strictes, flux) et **ajouter une règle chaque fois que l'agent fait quelque chose qu'il ne doit pas refaire**.
2. Installer des skills.
3. Faire d'un flux répétitif son premier agent.
4. **Écrire les tests et evals avant le code** : ils sont le contrat avec l'IA.
5. Relire chaque ligne qui sera livrée ; vérifier que les dépendances importées existent.
6. Entretenir ses propres compétences.

Côté responsables d'équipe : traiter le contexte (fichiers d'instructions, evals, skills) comme du code, avec des propriétaires nommés ; fixer la barre à l'eval, pas à la démo ; adapter la revue aux défauts propres au code généré ; **rendre explicite la frontière entre prototype et production** ; traiter le harness comme un actif partagé.

### Trois principes « durables » de la conclusion

1. *« Structure scales, vibes don't. »*
2. **L'IA amplifie la culture d'ingénierie** existante, forces comme faiblesses (même conclusion que [DORA 2025](dora-2025.md)).
3. Le rôle humain évolue sans diminuer : de l'implémentation vers le jugement.

---

## Ce que JDLC peut en retenir

### Confirmations de choix existants

| # | Idée | Ce que ça donnerait dans JDLC | Convergence |
|---|---|---|---|
| 1 | **Le produit du développeur, c'est le système qui produit le code** | Formule pédagogique pour présenter JDLC lui-même : JDLC *est* ce système (contexte, agents, portes, boucles, garde-fous), installé dans le projet cible. | Vision (définition d'un SDLC agentique), SFEIR (« standardiser le contexte et la preuve ») |
| 2 | **Agent = Modèle + Harness ; la plupart des échecs sont des échecs de configuration** | Vocabulaire pour le README et les ADR : JDLC est une couche de *harness* au-dessus de Claude Code. Renforce le réflexe « corriger le harness avant d'accuser le modèle ». | Principe 11, manifeste AIDD, Anthropic A3/A5 (le harness encode des hypothèses) |
| 3 | **La vérification fait la différence** | Confirme la discipline de la preuve ; la preuve doit porter sur le résultat **et** sur la trajectoire (l'agent a-t-il réellement lancé les tests ?). | Principe 8, AIDD (*checker* et preuves), Anthropic A3 |
| 4 | **Tests et evals écrits avant le code, comme contrat** | Rejoint le contrat avant implémentation : les critères d'acceptation, rédigés sous forme testable, sont produits avant le plan. | AIDD (Frame → contrat), Anthropic A3 (contrat de sprint), SFEIR (Define) |
| 5 | **Contexte traité comme du code, versionné, avec un propriétaire** | Déjà le principe 10. | Manifeste AIDD, Anthropic A2/A6 |
| 6 | **Skills et divulgation progressive** | Confirme le gabarit de skill routeur envisagé. | AIDD (anatomie des skills), Anthropic A2 |
| 7 | **Ajouter une règle chaque fois que l'agent se trompe** | Version minimale de la capitalisation, applicable dès l'installation. | SFEIR (une erreur vue deux fois devient une règle), AIDD (`learn`), Anthropic A2 (instructions ajoutées à partir des échecs) |
| 8 | **Le spectre selon l'enjeu** | Confirme le domaine d'application : prototyper d'abord, appliquer JDLC ensuite. | Vision (domaine d'application), SFEIR |

### Idées nouvelles ou qui précisent l'existant

| # | Idée | Ce que ça donnerait dans JDLC | Convergence |
|---|---|---|---|
| 9 | **Frontière statique / dynamique du contexte comme décision d'architecture** | Un ADR qui fixe ce que JDLC met dans le contexte toujours chargé (`CLAUDE.md` minimal, règles transverses) et ce qu'il charge à la demande (skills, références). Budget explicite pour le contexte statique. | **Nouveau** dans sa formulation ; prolonge Anthropic A2/A7 (contexte injecté minimal) |
| 10 | **Les hooks pour « ce que l'agent oublie souvent »** | Critère de choix entre règle écrite et hook : une consigne qui n'est pas suivie de façon fiable et dont la violation est détectable mécaniquement (secret commité, tests non lancés, fichier interdit modifié) devient un hook. | **Nouveau** comme critère ; rejoint SFEIR (hook pour la preuve) |
| 11 | **Le harness présent dans chaque phase, avec un rôle différent** (configurer, exécuter, boucler, observer) | Grille pédagogique pour décrire chaque phase JDLC : quel composant de harness elle mobilise. Utile à côté du vocabulaire des motifs de workflow d'Anthropic. | **Nouveau** |
| 12 | **Deux modes : chef d'orchestre ou orchestrateur** | Les modes de JDLC (supervisé / autonome) peuvent se décrire ainsi, et le choix dépend de la tâche, pas d'une préférence globale. Éclaire la question du mode par défaut soulevée par AIDD. | Proche des modes *interactive* / *auto* d'AIDD |
| 13 | **Le problème des 80 %** | Concentrer l'attention humaine et les vérifications sur les 20 % : cas limites, gestion d'erreurs, intégration, hypothèses métier. Candidat pour la checklist de revue et pour une règle « demander quand une hypothèse métier n'est pas écrite ». | **Nouveau** comme cadrage ; rejoint la règle AIDD « demander plutôt que choisir en silence » |
| 14 | **Défauts typiques du code généré** | Checklist de revue spécifique : dépendances inventées (vérifier que le paquet existe), gestion d'erreurs insuffisante, code qui « a l'air juste », sophistication gratuite. | **Nouveau** (en partie) ; rejoint la vidéo « Senior engineers » (simplification comme angle de revue) |
| 15 | **Évaluer la trajectoire, pas seulement la sortie** | Pour l'évolution de JDLC lui-même : une future suite d'évaluations de JDLC regarde aussi *comment* l'agent a travaillé (a-t-il suivi les étapes, appelé les bons outils). | **Nouveau** ; prolonge Anthropic A6 (évaluer les changements d'instructions) |
| 16 | **Routage de modèles par phase** | Les sous-agents JDLC peuvent fixer leur modèle : modèle puissant pour cadrage, conception, revue critique ; modèle léger pour les tâches mécaniques. À tester : le livre blanc place la revue côté « petit modèle », ce qui contredit l'idée d'un vérificateur exigeant. | **Nouveau** ; à arbitrer avec Anthropic A3 (évaluateur sceptique) |
| 17 | **Fichier d'instructions de dix lignes au départ** | Le `init` de JDLC génère un `CLAUDE.md` très court, enrichi ensuite par la capitalisation, plutôt qu'un gros fichier d'emblée. | Précise ADR-0005 ; rejoint Anthropic A7 |

## Ce qu'on écarte ou relativise

- **Le multi-outils et les standards ouverts** (portabilité des skills entre fournisseurs, A2A, Agents CLI) : hors périmètre, JDLC cible uniquement Claude Code ([ADR-0006](../adr/0006-claude-code-uniquement.md)).
- **Construire des agents en production** (ADK, Agent Runtime, observabilité hébergée) : c'est la partie la plus liée à l'offre Google ; JDLC outille le développement, pas le déploiement d'agents.
- **Les recommandations d'organisation** (embauche, équipes hybrides, actifs partagés entre équipes) : JDLC est solo pour l'instant. On garde l'idée de propriétaire explicite du contexte.
- **Les chiffres** (85 % de développeurs, 41 % de code généré, gains de 25-39 %, rapport de coût 3 à 10, classements de benchmarks) : sources secondaires hétérogènes (blogs, agrégateurs) et ordres de grandeur illustratifs, selon l'auteur lui-même pour le rapport de coût. Ils ne fondent aucune décision de JDLC.
- **Le ton promotionnel** (*« Generation is solved »*) : à lire comme un slogan. Le livre blanc reconnaît lui-même que les erreurs se sont déplacées plutôt qu'elles n'ont disparu.

## Sources secondaires

- Addy Osmani, [The New Software Lifecycle](https://addyosmani.com/blog/new-sdlc-vibe-coding/) (2026-06-16), billet de présentation ; republié sur [O'Reilly Radar](https://oreilly.com/radar/the-new-software-lifecycle).
- Billets antérieurs d'Osmani cités dans le livre blanc : *The Factory Model*, *Agentic Engineering*, *From Conductors to Orchestrators*, *The 80% Problem in Agentic Coding*. À consulter si l'une de ces idées est approfondie.
- Les autres livres blancs de la série Kaggle (jour 3 : *Context Engineering: Sessions, Skills & Memory* ; jour 5 : *Spec-Driven Production Grade Development in the Age of Vibe Coding*) : candidats pour une étude ultérieure.
