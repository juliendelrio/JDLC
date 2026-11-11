# Agent Skills et kits à base de skills (Anthropic)

Trois dépôts Anthropic étudiés ensemble : le dépôt public des Agent Skills, un skill qui mène de l'idée à un agent en ligne par un entretien guidé, et un dépôt de skills pour enseignants dont on ne regarde que les grilles d'évaluation.

| | `skills` | `launch-your-agent` | `k12-teacher-skills` |
|---|---|---|---|
| **Source** | [anthropics/skills](https://github.com/anthropics/skills) | [anthropics/launch-your-agent](https://github.com/anthropics/launch-your-agent) | [anthropics/k12-teacher-skills](https://github.com/anthropics/k12-teacher-skills) |
| **Auteur** | Anthropic PBC | Anthropic PBC | Anthropic PBC et Learning Commons |
| **Version étudiée** | Commit `2c5958e` (2026-11-06) | Commit `d5d0ffb` (2026-09-23) | Commit `31fd6db` (2026-10-13) |
| **Consulté le** | 2026-11-11 | 2026-11-11 | 2026-11-11 |
| **Licence (vérifiée au commit)** | **Par dossier.** Pas de licence à la racine. Chaque skill a son `LICENSE.txt` Apache-2.0, **sauf `doc-coauthoring`, sans aucune licence**. Détail en fin de note. | **Apache-2.0** (`LICENSE`), en-têtes `Copyright 2026 Anthropic PBC` et SPDX dans les fichiers. Pas de `NOTICE`. | **Apache-2.0** (`LICENSE`) **avec un fichier `NOTICE`** (Anthropic et Learning Commons). Les références pédagogiques citent des standards sous une autre licence. |
| **Niveau de reprise possible** | Idée, adaptation ou import pour les dossiers Apache-2.0 ; idée seulement pour `doc-coauthoring` | Idée, adaptation ou import | Idée, adaptation ou import des grilles (`evals/`), en reprenant le `NOTICE` |
| **Niveau actuel** | **Idée** | **Idée** | **Idée** |
| **Statut** | Exemples « à but de démonstration et pédagogique » | Implémentation de référence, ni maintenue ni ouverte aux contributions | Dépôt public avec CLA |

## De quoi il s'agit

### `anthropics/skills`

Le dépôt d'exemples de skills d'Anthropic, distribué comme marketplace de plugins Claude Code. Il contient une vingtaine de skills, un gabarit minimal (`template/SKILL.md`) et un renvoi vers la spécification, qui vit désormais sur agentskills.io (non lue ici).

Pour JDLC, quatre skills comptent :

- **`skill-creator`** : le skill qui aide à écrire, tester et améliorer un skill. C'est le cœur de cette note. Il décrit une boucle complète : entretien, brouillon, exécutions de test avec et sans le skill, notation, revue humaine, réécriture. Il fournit trois sous-agents (`grader`, `comparator`, `analyzer`), des scripts d'agrégation, un visualiseur HTML et une boucle d'optimisation de la description.
- **`doc-coauthoring`** : un processus guidé en trois étapes pour coécrire un document (spec, proposition, document de décision). Directement transposable au cadrage et à la spécification de JDLC. **Attention : pas de licence.**
- **`webapp-testing`** et **`mcp-builder`** : deux skills d'ingénierie. Le premier montre comment utiliser des scripts en boîte noire ; le second contient une méthode de construction d'évaluations.

Le skill `claude-api` contient aussi une liste de contrôle pour auditer une évaluation (`shared/evals/eval-audit.md`), utile pour la phase de vérification.

Les autres skills (art génératif, charte graphique, documents Office, GIF Slack, thèmes…) n'apportent rien à JDLC.

### `anthropics/launch-your-agent`

Deux skills Claude Code. `launch-your-agent` accompagne un fondateur technique de l'idée à un agent hébergé sur Claude Managed Agents, en quatre phases : entretien et plan, préparation et lancement, notation et itération, exécution planifiée. `wrap-up` est un skill compagnon de clôture.

Le domaine (les agents hébergés) est hors périmètre. Mais la **phase 1 est un entretien de cadrage complet** : comment ouvrir, quand poser quelle question, comment transformer les réponses en configuration, comment traiter ce qui ne rentre pas dans la première version. C'est exactement la question des phases Cadrage et Spécification de JDLC.

### `anthropics/k12-teacher-skills`

Des skills pour enseignants, accompagnés de **grilles d'évaluation** publiées à part, dans `evals/`. Conformément à la consigne, seule la façon dont ces grilles sont associées aux skills a été étudiée ; le contenu pédagogique est ignoré.

## Ce que JDLC peut en retenir

### Écrire un skill

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 1 | **Anatomie et trois niveaux de chargement.** Un `SKILL.md` obligatoire, puis `scripts/`, `references/` et `assets/`. Le nom et la description sont toujours en contexte. Le corps est chargé au déclenchement (moins de 500 lignes). Les ressources ne sont lues qu'au besoin. Une référence de plus de 300 lignes porte une table des matières. Quand un skill couvre plusieurs variantes, une référence par variante. | Gabarit de skill JDLC, avec ces seuils comme règles de relecture. | Confirmation : divulgation progressive (Anthropic A2, [google-new-sdlc.md](google-new-sdlc.md) idée 6), [single-agent-loop-kit.md](single-agent-loop-kit.md) idée 1. Les seuils chiffrés sont nouveaux. |
| 2 | **Tout le « quand l'utiliser » va dans la description**, pas dans le corps : le corps n'est lu qu'une fois le skill déclenché. Selon Anthropic, le modèle a tendance à **sous-déclencher** les skills ; il recommande des descriptions un peu insistantes. | Règle de rédaction des descriptions JDLC. L'insistance reste à doser (voir « Ce qu'on écarte »). | Confirmation pour la première partie ([single-agent-loop-kit.md](single-agent-loop-kit.md) idée 20). **Nouvelle** pour le sous-déclenchement. |
| 3 | **Des frontières explicites entre skills voisins dans la description** : « pas pour X, utiliser Y », « ne pas invoquer aussi Z ». Les skills k12 le font systématiquement. | Chaque skill JDLC dont le domaine touche un autre skill JDLC nomme ce voisin dans sa description (par exemple spécification et conception). | **Nouvelle** dans sa forme. Prolonge la revue de recouvrement ([single-agent-loop-kit.md](single-agent-loop-kit.md) idée 20). |
| 4 | **Expliquer le pourquoi plutôt qu'empiler des « TOUJOURS » et « JAMAIS ».** Des majuscules ou une structure rigide sont un signal d'alerte : mieux vaut donner la raison. | Guide de style des skills JDLC. | Confirmation : [single-agent-loop-kit.md](single-agent-loop-kit.md) idée 21, Anthropic A2. |
| 5 | **Les scripts embarqués s'utilisent en boîte noire.** On lance `--help` d'abord ; on ne lit la source que si c'est indispensable, pour ne pas remplir le contexte. | Consigne type dans les skills JDLC qui embarquent des scripts. | **Nouvelle** dans sa formulation. Complète « les skills transportent des ressources » ([anthropic-talks-erik.md](anthropic-talks-erik.md) idée 12). |
| 6 | **Repérer le travail répété dans les transcriptions de test.** Si chaque exécution de test réécrit le même script d'aide, ce script doit être embarqué dans le skill. | Critère pour décider qu'un comportement d'un skill JDLC devient un script. | **Nouvelle**. Rejoint « le déterministe en script » ([anthropic-talks-erik.md](anthropic-talks-erik.md), C2). |
| 7 | **Partir de la conversation en cours.** Quand l'utilisateur dit « fais-en un skill », on extrait d'abord de l'historique les outils utilisés, les étapes, les corrections faites par l'humain ; l'humain comble les trous et valide. | Mécanisme pour la phase Retour d'expérience : transformer une session réussie en skill ou en règle. | **Nouvelle** comme procédure. Rejoint la capitalisation ([claude-code-tips-yk.md](claude-code-tips-yk.md) idée 2). |
| 8 | **Généraliser à partir des retours, sans surajuster.** On itère sur quelques exemples par commodité, mais le skill servira des milliers de fois. Face à un problème tenace, on change d'angle plutôt que d'ajouter une règle étroite. | Garde-fou pour l'évolution des skills JDLC. | **Nouvelle** formulation. Rejoint le principe 11. |

### Évaluer un skill

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 9 | **Une boucle de test courte.** Deux ou trois demandes réalistes. Pour chacune, deux sous-agents lancés **au même moment** : un avec le skill, un sans. Quand on améliore un skill existant, la référence est **une copie de l'ancienne version**, faite avant de modifier. Les résultats sont rangés par itération. | Procédure concrète pour faire évoluer un skill JDLC. | Confirmation : test avec et sans ([single-agent-loop-kit.md](single-agent-loop-kit.md) idée 8, [claude-code-docs.md](claude-code-docs.md) idée 15). La copie de l'ancienne version comme référence est nouvelle. |
| 10 | **Des assertions écrites pendant que les tests tournent**, vérifiables objectivement, avec un nom lisible. Ce qui relève du goût (style, design) n'a pas d'assertion : c'est l'humain qui juge. | Pour les évals JDLC : assertion quand c'est mesurable, revue humaine sinon. Ne pas forcer des chiffres sur du jugement. | **Nouvelle** |
| 11 | **L'humain voit les sorties avant que l'agent corrige.** Le skill insiste : montrer les résultats à l'humain d'abord, puis lire ses retours cas par cas. Un retour vide veut dire « bien ». | Règle des évals JDLC : la revue humaine précède la correction automatique. | **Nouvelle** dans sa forme. Rejoint le principe 9 et « l'humain fixe l'oracle » ([single-agent-loop-kit.md](single-agent-loop-kit.md) idée 9). |
| 12 | **Un correcteur qui note et critique l'éval.** Il cite une preuve pour chaque verdict. Pas de note partielle. En cas de doute, c'est un échec. Il regarde les fichiers produits, pas ce que la transcription prétend. Il extrait les affirmations implicites de la sortie et les vérifie. Enfin il signale une assertion trop facile à satisfaire, ou un résultat important qu'aucune assertion ne couvre. | Gabarit de l'agent vérificateur JDLC : verdict prouvé, charge de la preuve sur le travail, et retour sur la qualité des critères eux-mêmes. | Confirmation pour la preuve (principe 8). **Nouvelle** pour le double rôle « noter et critiquer l'éval ». |
| 13 | **Chercher les assertions qui ne discriminent pas.** Une assertion qui passe toujours, avec ou sans skill, ne mesure rien. Une assertion qui varie beaucoup est instable. On compare aussi le coût en temps et en tokens au gain. | Lecture critique des résultats d'éval JDLC avant d'en tirer une conclusion. | **Nouvelle** |
| 14 | **Comparaison à l'aveugle**, puis analyse du pourquoi. Un agent compare deux sorties A et B sans savoir laquelle vient de quelle version ; un second explique ce qui a fait gagner l'une. Optionnel. | Outil ponctuel pour trancher « la nouvelle version est-elle vraiment meilleure ? ». | Confirmation : [superpowers.md](superpowers.md) idée 7. |
| 15 | **Évaluer le déclenchement, pas seulement le résultat.** Vingt requêtes réalistes et détaillées : la moitié doit déclencher le skill, l'autre moitié sont des **quasi-voisines** qui ne doivent pas. Chaque requête est jouée trois fois. Séparation 60/40 entre ajustement et test ; on retient la description qui gagne sur le test. Précision utile : le modèle ne consulte un skill que pour une tâche qu'il ne règle pas facilement seul ; une requête triviale ne teste donc rien. | Pour les skills JDLC auto-invocables : une petite suite de déclenchement, surtout pour les skills voisins (idée 3). | **Nouvelle** |
| 16 | **Auditer une éval avant de lui faire confiance** (`claude-api`, `eval-audit.md`). D'où vient la vérité attendue (humain, ou sorties d'un modèle) ? La réponse fuit-elle dans le contexte ? Une réponse triviale obtient-elle un bon score ? Reste-t-il de la marge ? Constat d'Anthropic : un résultat surprenant est plus souvent un bug de l'éval qu'un fait sur le modèle. | Liste de contrôle pour les évals de JDLC et pour les tests d'acceptation produits en phase Spécification. | **Nouvelle**. Complète « mesurer avant de changer » ([single-agent-loop-kit.md](single-agent-loop-kit.md) idée 7). |
| 17 | **Des questions d'éval bien formées** (`mcp-builder`) : indépendantes, en lecture seule, réalistes, demandant plusieurs étapes, avec **une réponse unique vérifiable** et stable dans le temps. L'agent résout lui-même chaque question pour vérifier la réponse attendue. | Critères de qualité des cas d'éval JDLC. | **Nouvelle** |

### Associer un skill à une grille d'évaluation (k12)

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 18 | **La grille vit à côté du skill, pas dedans.** Le dossier `evals/` est hors du plugin : il n'est pas installé chez l'utilisateur. Une grille par skill, au format CSV : identifiant, catégorie, critère, **ce qu'exige un succès**, notes, condition d'application. | Dans le dépôt JDLC : un dossier d'évals par skill, non distribué avec le plugin. | **Nouvelle** dans son format. Rejoint l'évaluateur calibré (Anthropic A3, [anthropic.md](anthropic.md) idée 10). |
| 19 | **Critères binaires et indépendants.** Chaque critère passe ou échoue (0 ou 1). Un critère conditionnel est **ignoré, pas échoué**, si sa condition n'est pas remplie. Une grille commune, plus des couches spécifiques. On suit le taux de réussite **par critère**, car un agrégat masque les trous. | Format des grilles JDLC : un socle commun à tous les livrables, plus une couche par phase. | **Nouvelle** |
| 20 | **Le comportement conversationnel a sa propre catégorie.** Les critères « M » jugent la réponse du modèle (a-t-il demandé l'information manquante avant de produire ? a-t-il proposé des suites ?). Les autres jugent les documents. Le prompt de juge précise : le contenu doit être **présent dans les documents**, pas seulement annoncé dans la réponse. | Pour les skills de cadrage JDLC : un critère « a posé la question requise avant d'écrire », et un critère « ce qui est annoncé est réellement dans le fichier ». | **Nouvelle**. Le second point prolonge la discipline de la preuve (principe 8). |
| 21 | **Des critères de cohérence entre artefacts** : pas de contradiction de chiffres ou de consignes entre deux documents, les mêmes tâches décrites partout. Et un critère « spécifique, pas générique » : un contenu qu'on pourrait transplanter tel quel dans un autre sujet échoue. | Pour la revue JDLC : la spec, le plan et les tests racontent la même chose. Un critère anti-remplissage pour les specs. | **Nouvelle** |
| 22 | **Calibrer le juge** en ajustant la formulation des critères avec des praticiens. | Les grilles JDLC se calibrent sur des cas réels jugés par l'humain. | Confirmation : Anthropic A3 ([anthropic.md](anthropic.md) idée 10). |

### Mener un processus guidé par entretien

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 23 | **Coécrire un document en trois étapes** (`doc-coauthoring`) : collecter le contexte, construire section par section, puis **tester le document avec un lecteur neuf**. Le workflow est proposé, pas imposé : si l'humain refuse, on travaille librement. | Structure possible du skill de spécification JDLC. | **Nouvelle** comme structure. |
| 24 | **Collecter le contexte en vrac, puis questionner.** D'abord quelques questions de cadrage (type de document, public, effet attendu, gabarit). Puis l'humain « vide son sac » sans s'organiser. Puis 5 à 10 questions numérotées, auxquelles il répond en abrégé (« 1 : oui, 2 : voir ticket, 3 : non »). **Critère de sortie** : on peut poser des questions sur les cas limites et les compromis sans avoir à réexpliquer les bases. | Phase Cadrage : un critère de sortie explicite pour la collecte de contexte. | **Nouvelle**. Autre style que « une question à la fois » ([superpowers.md](superpowers.md) idée 15) : à arbitrer par l'usage. |
| 25 | **Section par section, en commençant par la plus incertaine.** Pour chaque section : questions, liste numérotée d'options, l'humain garde, retire ou fusionne (avec ses raisons), brouillon, puis retouches ciblées. L'humain dit quoi changer plutôt que d'éditer lui-même, pour que l'agent apprenne son style. Après trois itérations sans changement notable, on demande ce qui peut être retiré. | Méthode de rédaction des specs et ADR assistés dans JDLC. | **Nouvelle** |
| 26 | **Le test du lecteur neuf.** On prédit les questions que se posera un lecteur. Un sous-agent sans contexte reçoit seulement le document et ces questions. Un second cherche ambiguïtés, hypothèses implicites et contradictions. On boucle jusqu'à ce que le lecteur réponde juste. Enfin l'humain relit lui-même : il reste responsable du document. | Porte de sortie de la phase Spécification : la spec doit suffire à un agent neuf. | Confirmation : test « à froid » des tâches ([adlc.md](adlc.md) idée 8), session neuve après la spec ([claude-code-docs.md](claude-code-docs.md) idée 3). Appliqué ici au document lui-même. |
| 27 | **Ouvrir léger, laisser expliquer avant de proposer** (`launch-your-agent`). L'ouverture tient en deux phrases, deux ou trois exemples et une question ouverte. Après la première réponse : une seule relance ouverte, avant tout menu ou toute reformulation. Ensuite, des grappes de questions choisies selon ce qui reste ouvert, jamais un formulaire. Choix fermés dès que les réponses sont énumérables, au plus une question ouverte par tour. | Règles d'interaction du skill de cadrage JDLC. | **Nouvelle** dans sa précision. Rejoint « lire avant de demander » ([superpowers.md](superpowers.md) idée 15). |
| 28 | **L'entretien est un exercice de correspondance.** Chaque grappe de questions décide d'un champ précis de la configuration. Une table relie question, décision, fichier produit et endroit où le résultat est visible. Une personne qui raconte son besoin répond souvent à plusieurs grappes d'un coup : on ne pose que ce qui reste ouvert. | Le skill de cadrage JDLC relie chaque question à une section du document de cadrage. Une question qui ne nourrit aucune section ne se pose pas. | **Nouvelle** |
| 29 | **Une fiche unique, source de vérité.** Les réponses alimentent un seul fichier structuré ; les autres fichiers en sont des projections. Avant de produire quoi que ce soit, on relit la fiche à l'humain sous forme d'un résumé tabulaire, et on attend son accord. | Le cadrage JDLC produit un seul document de référence ; spec et plan en découlent. La validation du résumé est une porte humaine nommée. | Confirmation partielle : plan versionné comme état ([single-agent-loop-kit.md](single-agent-loop-kit.md) idée 2). La relation source / projections est nouvelle. |
| 30 | **Ses mots, pas les nôtres.** Partout où le besoin est écrit, on reprend ce que l'humain a dit (citation ou paraphrase proche). On n'invente jamais un détail qu'il n'a pas donné (durées, tailles d'équipe, intensité de la douleur). | Règle de rédaction des documents de cadrage et de spec JDLC. | **Nouvelle** |
| 31 | **« Pas maintenant » veut dire « planifié ».** Tout ce qui sort de la première version est écrit **sur le moment** dans un fichier de suites, rangé en v1, v2…, avec quoi, pourquoi et comment. Trois raisons de report sont toujours distinguées : impossible, il manque un prérequis, hors périmètre de cette itération. | Le cadrage JDLC tient une liste « hors périmètre » vivante, classée par raison. Alimente aussi la phase Dépréciation à l'envers : ce qu'on n'a pas fait reste tracé. | **Nouvelle** |
| 32 | **Le critère de « terminé » vient une fois le besoin compris**, dans une étape nommée. On cherche de **vrais cas passés** avec la bonne réponse connue : un sert à construire, les autres sont gardés de côté pour vérifier ensuite. Sans cas réel, la première sortie vérifiée devient le cas de référence. | Phase Spécification : critères d'acceptation après le besoin, et un jeu de cas réels dont une partie n'est pas montrée pendant la construction. | Confirmation pour « terminé défini avant de coder » ([claude-code-docs.md](claude-code-docs.md)). **Nouvelle** pour les cas gardés de côté. |
| 33 | **Ne jamais faire attendre, prévenir tôt.** L'agent prépare tout ce qui ne demande pas l'humain. Dès qu'un élément humain devient inévitable (une clé, un accès), il dit tout de suite quoi et où le trouver ; la remise se fait le plus tard possible. Un secret ne passe jamais par la conversation. | Organisation des moments humains dans les skills JDLC : annoncer tôt, demander tard, une seule fois. | **Nouvelle** |
| 34 | **Un skill compagnon de clôture, idempotent** (`wrap-up`). Il relit l'état, récapitule ce qui existe, propose une ou deux suites, fait l'hygiène en silence et ne signale que ce qui exige une action humaine. Il ne rediscute pas les décisions. | Un skill de fin de phase JDLC, relançable à tout moment, qui sert aussi de point d'état. | **Nouvelle** |
| 35 | **Une échelle de repli.** Après deux échecs sur une étape, on descend d'un barreau (vérifier la doc, passer par une autre voie, revenir à une configuration connue), et on le dit en une phrase. | Règle d'échec commune aux skills JDLC. | Confirmation : après deux corrections ratées, repartir ([claude-code-docs.md](claude-code-docs.md) idée 8). |
| 36 | **Le dépôt du skill documente sa méthode de test.** Le `CLAUDE.md` de `launch-your-agent` consigne les décisions de conception, et la façon de tester : dans un dossier jetable ignoré par git, en notant chaque friction, en corrigeant la copie du dépôt. | Le dépôt JDLC décrit comment on teste un skill JDLC, et où l'on note les frictions. | Confirmation : principe 10, écrire un skill comme du TDD ([superpowers.md](superpowers.md) idée 9). |

## Ce qu'on écarte ou relativise

- **Les descriptions « insistantes ».** Elles augmentent le déclenchement, mais aussi le coût permanent et les faux déclenchements ([claude-code-docs.md](claude-code-docs.md) idée 16). JDLC tranche par une éval de déclenchement (idée 15), pas par réflexe.
- **L'outillage complet de `skill-creator`** (visualiseur HTML, scripts Python, boucle `claude -p`, mesures de temps et de tokens) est lourd pour un développeur seul. On garde la méthode. On vérifiera si `claude plugin eval` couvre le besoin avant de construire quoi que ce soit.
- **Le ton de `skill-creator`** est familier et contient lui-même des majuscules qu'il déconseille. On retient le fond, pas la forme.
- **Le style de `launch-your-agent`** (émojis, ton célébratoire, page HTML de synthèse) et tout ce qui touche à Claude Managed Agents : hors périmètre.
- **Deux styles de questions coexistent** : un lot de questions numérotées (`doc-coauthoring`) ou une question ouverte à la fois (`launch-your-agent`, Superpowers). Les deux ont leur place ; l'usage dira lequel convient au cadrage JDLC.
- **Le remue-méninges de 5 à 20 options par section** est coûteux. À réserver aux sections incertaines.
- **Le contenu des skills k12** et leur dépendance à un graphe de connaissances externe : sans objet pour JDLC.
- **La spécification Agent Skills** a quitté le dépôt pour agentskills.io. Elle n'a pas été lue ici. Le gabarit du dépôt est trivial (nom, description, une ligne).

## Dépôts sans intérêt pour JDLC

Aucun dépôt entier. Dans `anthropics/skills`, les skills créatifs, de marque et de documents Office n'ont pas d'intérêt pour JDLC.

## Licence et réutilisation

- **`anthropics/skills`**
  - Il n'y a **pas de licence à la racine**. Chaque dossier de skill a son propre `LICENSE.txt`, à vérifier un par un.
  - `skill-creator`, `webapp-testing`, `mcp-builder` et `claude-api` sont sous **Apache-2.0**, avec la mention `Copyright 2026 Anthropic, PBC.` Contrairement à ce qu'on pouvait craindre, les skills de documents (`docx`, `pdf`, `pptx`, `xlsx`) sont aussi sous Apache-2.0 à ce commit (texte avec la mention de copyright non remplie).
  - **`doc-coauthoring` n'a aucun fichier de licence** à ce commit, alors qu'il est distribué dans la marketplace. Selon [licences.md](../licences.md), c'est **idée uniquement** : réécriture complète, sans le texte source sous les yeux. C'est pourtant le skill le plus proche du besoin de spécification de JDLC.
  - `THIRD_PARTY_NOTICES.md`, à la racine, concerne des dépendances média (dont FFmpeg sous GPL-3.0). Rien à reprendre.
  - Candidats à une adaptation : `skill-creator/agents/grader.md` (le correcteur), la partie « Analyzing Benchmark Results » d'`analyzer.md`, la méthode d'éval de déclenchement, `eval-audit.md`.
- **`anthropics/launch-your-agent`** : Apache-2.0, chaque fichier porte `Copyright 2026 Anthropic PBC` et `SPDX-License-Identifier: Apache-2.0`. Pas de `NOTICE`. Candidats : les règles d'entretien de `SKILL.md` et la structure de `references/interview.md`.
- **`anthropics/k12-teacher-skills`** : Apache-2.0 avec un **`NOTICE`** (copyright Anthropic et Learning Commons) à reproduire en cas d'import. Les fichiers `references/` des skills contiennent des standards scolaires sous une licence tierce : ne pas y toucher. Seuls le format des grilles et le prompt de juge de `evals/README.md` sont candidats à une adaptation.
- Tout import Apache-2.0 garde la mention de copyright, copie le texte de licence dans `licenses/`, reprend le `NOTICE` s'il existe et signale les modifications. Licences à revérifier au commit exact au moment de l'import.
