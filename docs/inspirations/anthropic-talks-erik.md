# Conférences d'Anthropic : « vibe coder en production » et « des agents plus efficaces »

Deux interventions de **Erik S.**, chercheur chez Anthropic et co-auteur, avec Barry Zhang, de *Building effective agents* (voir [anthropic.md](anthropic.md), A1).

| | C1 — Conférence | C2 — Entretien |
|---|---|---|
| **Titre** | *Master Coding Agents Like a Pro: Anthropic's Ultimate Playbook* (titre du fichier transmis). Le contenu est la conférence « vibe coding in prod responsibly ». | *Building more effective AI agents* |
| **Intervenants** | Erik (Anthropic, agents de code) | Alex (relations Claude chez Anthropic) et Eric (recherche multi-agents chez Anthropic), probablement la même personne que dans C1 |
| **Date** | Non déterminée ; vers mi-2025 d'après le contenu (« les modèles Claude 4 depuis une semaine ou deux ») | Non déterminée ; après le lancement des skills (fin 2025) d'après le contenu |
| **Support étudié** | Transcription complète en anglais, fournie par l'auteur de JDLC (URL non communiquée) | Transcription complète en anglais, fournie par l'auteur de JDLC (URL non communiquée) |
| **Consulté le** | 2026-11-11 | 2026-11-11 |
| **Licence** | Vidéo, pas de licence de réutilisation connue | Idem |
| **Niveau de reprise** | **Idée uniquement** | **Idée uniquement** |

---

## C1 — Vibe coder en production, de façon responsable

### Thèse

Au sens strict (celui d'Andrej Karpathy), « vibe coder » signifie *oublier que le code existe*. Ce n'est pas la même chose que beaucoup utiliser l'IA tout en restant dans une boucle serrée avec elle.

Pourquoi s'y intéresser malgré les risques ? À cause de l'exponentielle. La durée des tâches qu'une IA sait mener double environ tous les sept mois. Quand un agent produira une journée ou une semaine de travail d'un coup, relire chaque ligne fera de l'humain le goulot d'étranglement.

La réponse : **oublier que le code existe, mais pas que le produit existe.**

### Vérifier sans lire l'implémentation

C'est un problème aussi vieux que le management. Un responsable technique, un responsable produit ou un dirigeant contrôle tous les jours le travail d'experts sans en maîtriser l'implémentation :

- il écrit des tests d'acceptation ;
- il utilise le produit ;
- il vérifie par sondage les faits qu'il comprend.

Le principe : trouver **une couche d'abstraction vérifiable** sans connaître ce qu'il y a dessous, comme on fait confiance à un compilateur sans relire l'assembleur.

### L'exception : la dette technique

Aujourd'hui, la dette technique ne se mesure pas sans lire le code. D'où la recommandation de **cantonner l'autonomie aux « feuilles » de la base de code** : les fonctionnalités terminales dont rien ne dépend. Les « troncs et branches » (l'architecture sur laquelle tout repose) doivent rester compris par l'humain, parce que c'est là qu'on construira ensuite. Cette frontière recule à mesure que les modèles progressent.

### Être le chef de produit de Claude

*« Ne demandez pas ce que Claude peut faire pour vous, mais ce que vous pouvez faire pour Claude. »*

- On donne à l'agent ce qu'on donnerait à un nouvel arrivant : visite du code, exigences, contraintes, modèles à suivre.
- Erik consacre souvent 15 à 20 minutes à préparer le travail, dans une conversation séparée avec Claude : exploration du code, fichiers concernés, motifs à suivre, construction d'un plan. Le plan devient un artefact, exécuté ensuite (souvent dans un contexte neuf). Le taux de réussite est alors très élevé.
- Il faut savoir poser les bonnes questions. Une personne totalement non technique ne peut pas jouer ce rôle sur un système de production : elle ne sait pas ce qui est dangereux.
- Il ne faut pas trop contraindre. Le niveau de détail dépend de ce qui compte pour soi : les exigences seules quand l'implémentation importe peu, les classes et exemples à suivre quand on connaît bien le code.

### Un cas réel

Une modification de 22 000 lignes a été fusionnée dans du code de production d'Anthropic. Ce qui l'a rendue responsable :

- des jours de travail humain sur les exigences et le guidage ;
- un changement concentré dans les feuilles ;
- des tests de stabilité soigneusement conçus pour les parties qui devaient rester extensibles ;
- un système **conçu pour avoir des entrées et sorties vérifiables par un humain**, c'est-à-dire des points de contrôle vérifiables sans lire le code.

### Pratiques citées en réponse aux questions

- **Tests d'abord, mais minimalistes.** Demander quelques tests de bout en bout (le cas nominal et deux cas d'erreur) plutôt que des tests collés à l'implémentation. Lire les tests en premier : si on est d'accord avec les tests et qu'ils passent, on peut avoir confiance dans le code.
- **Compacter aux pauses naturelles.** Explorer et planifier, faire écrire le plan dans un document, puis compacter ou repartir d'une session neuve. Le bon moment est là où un humain s'arrêterait pour une pause.
- **Explorer avant de coder** dans une partie inconnue : demander où se trouve telle logique, quelles fonctionnalités similaires existent, quels fichiers et classes regarder. On se construit une image mentale avant de lancer la fonctionnalité.
- **Apprendre avec l'agent.** Lui demander d'expliquer une bibliothèque ou un choix pendant la relecture. Des cycles plus courts permettent de tirer plus de leçons d'architecture dans le même temps, à condition de faire l'effort.

## C2 — Construire des agents plus efficaces

- **Les agents ont dépassé les workflows** quand la qualité prime. Les workflows restent utiles quand la latence compte.
- **Les « workflows d'agents ».** Chaque étape d'une chaîne devient une **boucle fermée** : l'agent exécute, observe le résultat, corrige jusqu'à obtenir une sortie valide, *puis* passe à l'étape suivante. Une étape qui échoue en silence ne contamine plus la suite.
- **La simplicité reste la règle.** Plus le système est complexe, plus l'observabilité est difficile. On commence par le plus simple et on n'ajoute des couches que si nécessaire.
- **Les sous-agents protègent le contexte.** Une sous-tâche coûteuse en tokens mais au résultat court est confiée à un sous-agent, qui ne renvoie que la réponse.
- **Claude fait les erreurs d'un manager débutant.** Il donne aux sous-agents des consignes incomplètes, en supposant qu'ils ont un contexte qu'ils n'ont pas. Une bonne délégation transmet le contexte d'ensemble.
- **Plusieurs agents pour une meilleure réponse.** On peut confier la même tâche à plusieurs agents, ou des approches différentes du même problème, puis comparer.
- **Danger du sur-dimensionnement.** Un système multi-agents trop gros passe son temps à se coordonner au lieu d'avancer, comme une organisation humaine qui grossit.
- **Se mettre à la place de l'agent.** Relire ce que le modèle reçoit réellement, y compris les transcriptions brutes et les appels d'outils. Le modèle ne voit que ce qu'on lui montre.
- **Un outil doit ressembler à une interface utilisateur, pas à une API.** L'agent est un utilisateur : un outil doit présenter d'un coup une information complète et lisible, plutôt que d'imposer plusieurs appels techniques.
- **Les skills sont des instructions plus des ressources.** Ils peuvent contenir des gabarits, des scripts, des images, en plus des consignes.
- **Écrire du code pour produire un artefact.** C'est souvent plus efficace que de produire l'artefact directement : un script génère un SVG répétitif, un tableur, etc.
- **Fermer la boucle de test.** L'agent vérifie lui-même son application (par exemple en l'utilisant via *computer use*), pour que l'humain ne soit plus son testeur.

---

## Ce que JDLC peut en retenir

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 1 | **Zoner la base de code : feuilles et tronc** | Dans le cadrage ou la planification, classer la zone touchée : *feuille* (autonomie large, vérification par le comportement) ou *tronc* (architecture : compréhension et revue humaines exigées). Le niveau de validation dépend de la zone, pas seulement de la taille du changement. | **Nouvelle**. Complète la proportionnalité (AIDD, BMad, profils AWS) |
| 2 | **La dette technique est ce qui échappe à la vérification par le comportement** | Les revues automatiques et humaines du *tronc* doivent cibler la dette (extensibilité, lisibilité) ; les tests ne la voient pas. | **Nouvelle**. Rejoint la simplification (vidéo YK, *compound engineering*) |
| 3 | **Concevoir pour la vérifiabilité** | Exigence de conception : prévoir des entrées et sorties observables, des tests de stabilité, des points de contrôle vérifiables sans lire le code. À intégrer aux critères de la phase Conception. | **Nouvelle** dans sa formulation. Rejoint « critère = méthode de vérification » (ADLC) |
| 4 | **Vérifier une couche d'abstraction, pas l'implémentation** | Les validations humaines portent sur les tests d'acceptation, l'usage du produit et des vérifications par sondage, pas sur le diff complet. | Confirmation : ADLC (P6), « contrôler les résultats » (vidéo), fatigue d'approbation (Anthropic A4) |
| 5 | **Être le chef de produit de l'agent** | La phase amont (explorer, rassembler le contexte, produire un plan) est un investissement qui conditionne le taux de réussite. Puis exécution dans un contexte neuf. | Confirmation : SFEIR (amont), AIDD (Frame), Superpowers, vidéo YK |
| 6 | **Tests de bout en bout minimalistes, lus en premier** | Gabarit de tests pour JDLC : cas nominal plus quelques cas d'erreur, au niveau du comportement. L'humain lit les tests avant le code. | Confirmation : ADLC (rails), YK (tests commités avant), Kiro (tests de propriétés) |
| 7 | **Compacter aux pauses naturelles, après avoir écrit le plan** | Règle d'hygiène de contexte : chaque fin de phase écrit son artefact, puis on compacte ou on repart d'un contexte neuf. | Confirmation : Anthropic A2, YK (passation) |
| 8 | **Chaque phase est une boucle fermée** (« workflows d'agents ») | Chaque phase JDLC itère jusqu'à une sortie vérifiée avant de passer la main ; aucune transition sur un résultat non contrôlé. | Confirmation, nouvelle formulation : ADLC (porte déterministe entre phases) |
| 9 | **Bien briefer les sous-agents** | Gabarit de délégation pour les skills JDLC qui lancent des sous-agents : objectif, contexte d'ensemble, ce qui a déjà été écarté, format de retour attendu. | **Nouvelle** en tant que règle explicite |
| 10 | **Le sur-dimensionnement multi-agents est un coût** | Limiter le nombre d'agents et d'échanges ; mesurer le temps passé à se coordonner. | Confirmation : simplicité (A1), « JDLC doit savoir maigrir » |
| 11 | **Relire ce que l'agent voit vraiment** | Pratique de mise au point de JDLC : relire les transcriptions brutes quand un skill se comporte mal. | Confirmation : diagnostic sur transcriptions (Superpowers) |
| 12 | **Les skills transportent des ressources** | Les skills JDLC peuvent embarquer gabarits et scripts déterministes, pas seulement des consignes. | Confirmation : AIDD (`assets/`), documentation Claude Code |
| 13 | **Fermer la boucle de test** | L'agent vérifie lui-même le comportement (lancement de l'application, parcours utilisateur) avant de présenter le résultat. | Confirmation : Anthropic A3 (évaluateur Playwright), AIDD (`aidd-qa`) |
| 14 | **Savoir poser les bonnes questions** | Conforte le principe 9 : JDLC s'adresse à quelqu'un capable d'évaluer ce qu'il délègue. | Confirmation : principe 9, manifeste AIDD |

## Ce qu'on écarte ou relativise

- **Le discours sur l'exponentielle** est un pari prospectif. On n'en retient que la conséquence pratique, déjà notée ailleurs : les composants de JDLC sont des hypothèses à réévaluer à chaque nouveau modèle.
- **L'outil plutôt que l'API pour les serveurs MCP** est hors périmètre immédiat : JDLC ne prévoit pas d'outils MCP pour l'instant. À garder si cela change.
- **Le contexte de la modification de 22 000 lignes** était particulier : un système hors ligne, sans enjeu de sécurité. Il ne se transpose pas tel quel.
