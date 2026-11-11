# Synthèse croisée des sources

> **Statut** : base de travail pour le tri, datée du 2026-11-11. Elle ne décide rien. Les décisions viendront par ADR, ou par modification de la [vision](vision.md).

## Objet et méthode

JDLC a étudié 23 sources, analysées chacune dans une [note](inspirations/README.md). Cette synthèse les croise pour répondre à trois questions :

1. Sur quoi les sources **convergent-elles** ?
2. Où se **contredisent-elles**, et la contradiction se résout-elle ?
3. Que reste-t-il **à arbitrer** par l'auteur de JDLC, ou **à apprendre par l'usage** ?

Méthode :

- Les notes ont été réparties en quatre lots. Pour chaque lot, un agent a extrait les idées selon une grille commune de 14 thèmes (phases, spécification, validation humaine, vérification, contexte, capitalisation, architecture agentique, skills, proportionnalité, installation, mesure, sécurité, rôle de l'humain, autre). Il a aussi relevé les tensions.
- Les quatre extraits ont ensuite été croisés à la main.
- Chaque affirmation renvoie aux notes. Les références du type `adlc #2` désignent l'idée n° 2 du tableau de la note [adlc.md](inspirations/adlc.md).

Abréviations des notes les plus citées :

| Abréviation | Note |
|---|---|
| A1 à A7 | [anthropic.md](inspirations/anthropic.md), les sept articles d'Anthropic |
| ccdocs | [claude-code-docs.md](inspirations/claude-code-docs.md) |
| SALK | [single-agent-loop-kit.md](inspirations/single-agent-loop-kit.md) |
| AP, AS | [anthropic-plugins.md](inspirations/anthropic-plugins.md), [anthropic-skills.md](inspirations/anthropic-skills.md) |
| LR, EK | [anthropic-long-running.md](inspirations/anthropic-long-running.md), [anthropic-engineering-kits.md](inspirations/anthropic-engineering-kits.md) |
| TO, CD | [anthropic-tooling.md](inspirations/anthropic-tooling.md), [anthropic-cookbooks-demos.md](inspirations/anthropic-cookbooks-demos.md) |
| CE | [compound-engineering.md](inspirations/compound-engineering.md) |
| Erik, YK | [anthropic-talks-erik.md](inspirations/anthropic-talks-erik.md), [claude-code-tips-yk.md](inspirations/claude-code-tips-yk.md) |

Les autres notes sont citées par leur nom (sfeir, aidd, spec-kit, kiro, bmad, superpowers, aws, adlc, google, dora).

---

## 1. Convergences

Ce sur quoi au moins trois sources indépendantes s'accordent. C'est le socle le plus solide pour JDLC.

### 1.1 « Terminé » se définit avant de construire, et l'humain fixe l'oracle

- Le critère de fin est écrit **avant** le code : contrat de sprint (A3), demande « prête à planifier » (aidd), tests commités avant le code (YK #4), critères d'acceptation testables (google #4), liste de fonctionnalités figée (LR #9).
- **Chaque critère nomme sa méthode de vérification**, sinon c'est un souhait (adlc #5). La grille dit ce qui compte comme preuve (CD #1).
- **L'agent ne fixe jamais seul l'attendu** : sinon l'évaluation dérive vers ce que fait déjà le système, bugs compris (SALK #9). Même idée chez :
  - aws #11 : la commande de preuve est choisie une fois, par l'humain ;
  - spec-kit #9 : seul l'humain coche les listes de contrôle de qualité ;
  - bmad #10 : ce qui a été approuvé est figé ;
  - LR #38 : l'humain écrit l'oracle, l'agent le satisfait.
- **Un test en désaccord ne se corrige pas en changeant l'attente** (bmad, adlc #3, spec-kit #11). La voie médiane de spec-kit #12 lève l'apparente exception de Kiro (voir 2.1).

### 1.2 Pas de preuve, pas de fait

- C'est le principe 8 de la vision, confirmé partout : sfeir #1, ccdocs #1, superpowers #1 (« un rapport de sous-agent n'est pas une preuve, un diff l'est »), adlc (« pas de preuve, pas de fait »), google #3.
- La preuve peut être **imposée par la machine** plutôt que demandée :
  - un hook `Stop` qui empêche de finir tant que la vérification échoue (ccdocs #11) ;
  - un hook qui refuse d'écrire « réussi » tant qu'aucune preuve n'a été lue (LR #10) ;
  - un verdict calculé par un script, jamais recopié par le modèle (AP #24).
- **L'absence de signal n'est pas un feu vert** : une mesure indisponible n'améliore jamais un état (EK #34) et une session sans verdict compte comme « non relue » (CD #7).

### 1.3 Celui qui fait n'est pas celui qui vérifie

- C'est la convergence la plus large. L'auto-évaluation est complaisante (A3). Le vérificateur travaille dans un **contexte neuf**. Il ne reçoit ni le raisonnement ni le journal de l'implémenteur (superpowers #4, bmad #4, aws #7, adlc, AP #25, LR #12), et seulement des outils de lecture (ccdocs #2).
- **Les constats d'une revue sont eux-mêmes des affirmations à vérifier.** Chaque constat est reproduit ou écarté par un second regard (bmad #12, adlc #4, AP #17, EK #26). La gravité proposée par le chercheur n'est pas reprise telle quelle (bmad #12, CD #6).
- **La découverte et la vérification sont deux temps distincts** : on cherche large, puis on trie strictement (EK #25, CD #6, TO #2).
- Le vérificateur doit être **cadré**, sinon il trouve toujours quelque chose : on lui dit quoi ignorer (ccdocs #13, CD #2) et on tient une liste versionnée de faux positifs (EK #27).

### 1.4 Peu de validations humaines, mais significatives

- **La fatigue d'approbation est mesurée** : 93 % des demandes de permission sont acceptées (A4). Une porte après chaque étape est écartée (aws). La part d'approbations « tampon » est le premier signal d'un humain qui ne contre-examine plus (EK #22).
- **Toutes les sources posent des portes peu nombreuses, nommées et prévisibles** (sfeir #2, adlc #2, SALK #3, AP #23). Leur nombre exact diverge : voir l'arbitrage A1.
- **Une porte se présente bien** :
  - un format court, avec une seule question et des réponses écrites (SALK #4) ;
  - des auto-contrôles mécaniques en tête, et une ligne « Conflits : aucun » obligatoire (EK #4) ;
  - un briefing d'ouverture de phase en quatre lignes (EK #3).
- **Une approbation exige un vrai tour humain** (aws #3), porte sur une version précise (TO #9 : un nouveau commit la périme) et ne couvre qu'un seul arrêt (SALK #5).
- **L'humain vérifie le comportement plus que le diff** : il lance le logiciel et sonde 2 ou 3 points chauds (adlc #2, Erik #4, vidéo #3). La limite de cette idée est l'arbitrage A3.

### 1.5 L'état du travail vit hors de la conversation, sur un support durable

> **Révisé le 2026-11-11.** Une première version disait « l'état du travail vit dans des fichiers ». C'était un biais des sources, presque toutes pensées pour un développeur seul en local. Celles qui supposent un outil de tickets (aidd, adlc, AP #12) en font la source de vérité. La convergence réelle porte sur le **support durable, hors de la conversation**, pas sur le fichier. Voir les ADR [0007](adr/0007-concu-pour-l-equipe.md), [0008](adr/0008-ticket-unite-de-travail.md) et [0009](adr/0009-spec-a-deux-niveaux.md).

- **Trois sortes d'état, sur deux niveaux.** Le niveau *instance* désigne un environnement de travail, le niveau *projet* ce qui est commun à toutes les instances et à toute l'équipe.

  | Sorte d'état | Niveau | Support |
  |---|---|---|
  | Suivi du travail (tickets, statut, reports) | Projet | L'outil de tickets du projet s'il existe, des fichiers du dépôt sinon. Jamais les deux. |
  | Décisions et règles durables (spec principale, ADR, règles, `CLAUDE.md`) | Projet | Le dépôt |
  | État de session (progression fine, notes de reprise) | Instance | Local, et un résumé est déposé dans le ticket aux portes |

- Le support doit permettre la reprise. Il y a un fichier d'état avec une reprise à la première case non cochée (SALK #2). La phase se déduit de l'état du support (EK #2, LR #27) et une commande `status` le relit pour signaler ce qui est périmé (AP #22). Ces mécanismes valent pour un ticket aussi bien que pour un fichier.
- **Chaque fin de phase écrit son artefact**, ce qui permet de repartir d'un contexte neuf. Plusieurs sources le disent : Erik #7, YK #1 (passation), A3, LR #6 (`PROGRESS.md`) et superpowers #5 (plan écrit pour un exécutant sans contexte).
- **Le contexte toujours chargé reste petit** : moins de 200 lignes, seulement ce que Claude ne peut pas trouver lui-même (ccdocs #5, YK #3, google #17, TO #25). On n'écrit que ce qui est coûteux à retrouver (bmad #6).
- **Les artefacts de contexte sont des caches à invalider** : on les date et on vérifie leur fraîcheur (adlc #11, bmad #18, dora #10, LR #37 : « la doc écrite par l'agent ment »).

### 1.6 Capitaliser sous la forme la moins chère qui empêche la répétition

- Un échec répété est un trou dans le système, pas une fatalité. C'est le principe 11 de la vision, confirmé par sfeir #3, aidd #16, adlc #10 et EK #11.
- **Ordre de préférence** : supprimer la cause, puis un test, un lint ou un hook, puis une règle écrite, puis une fiche de leçon (CE #2). La même préférence pour le déterministe se retrouve chez adlc #10, EK #23, AP #32 et ccdocs #10 (« une consigne soulignée qu'il rate encore devient un hook »).
- **Les leçons sont filtrées et entretenues** :
  - critère d'entrée (CE #3) et format en plusieurs volets (CE #4, EK #16) ;
  - promotion en règle après répétition (EK #17, A7 « rêve ») ;
  - nettoyage régulier : mettre à jour, fusionner, supprimer (CE #6, A7) ;
  - provenance notée : « vu 2 fois, non vérifié » (EK #19).
- **Plus de mémoire n'est pas mieux** : 413 fichiers ont donné 1 badge, une dizaine en ont donné 4 (A7). Expliquer à quoi sert la mémoire marche mieux que des règles rigides (A7).

### 1.7 Chaque composant est une hypothèse : JDLC doit savoir maigrir

- Chaque composant compense une limite du modèle. On documente laquelle, et on retire le composant quand la limite disparaît (A1, adlc #1, ccdocs #7, bmad #7).
- **Le test avec et sans** : à score égal, on supprime le composant. Ce test se rejoue à chaque nouveau modèle (SALK #8, AS #9, bmad #7, superpowers #9).
- **Supprimer par défaut** : un ajout se justifie par un échec observé (SALK #23, bmad, A2). Chaque instruction se paie à chaque exécution, et chaque description de skill à chaque tour (ccdocs #16).
- **Le harnais est absorbé par l'outil** : on part de ce que Claude Code fait nativement (plan mode, `/goal`, hooks, sous-agents, `/code-review`) et on ne code que l'écart (LR #24, ccdocs #18, SALK #25).

### 1.8 Proportionner le processus au travail

- Toutes les sources récentes ont plusieurs pistes, y compris « pas de processus du tout » pour l'évident : bmad #2, kiro #2, superpowers, aws #2 (profils nommés), aidd #7.
- **Faisabilité d'abord pour les gros chantiers**, avec « ne pas le faire » comme verdict valide (EK #5, spec-kit #14 `kill`).
- **La revue se dose selon le diff** : l'angle correction est toujours relu, les autres seulement si la surface le justifie (CE #8, AP #20).

### 1.9 Écrire les skills comme des briefings, et les évaluer

- **La description porte tout le « quand »** : une ou deux phrases, dans les mots de l'utilisateur, distincte des autres skills (SALK #20, AS #2, ccdocs #16).
- **Le corps explique le pourquoi**, sans majuscules ni menaces, et supprime les consignes écrites pour d'anciens modèles (AS #4, SALK #21, A2).
- **Structure en trois niveaux** : un `SKILL.md` court qui renvoie à un fichier par étape, plus des ressources et des scripts (aidd #10, SALK #1, AS #1, Erik #12).
- **Un skill s'évalue** : test avec et sans, test de déclenchement (AS #15) et TDD des skills (superpowers #9).

### 1.10 Les garanties plutôt que les consignes, pour ce qui compte

- **Une consigne** (`CLAUDE.md`, skill) peut être ignorée. **Une garantie** (hook de type `command`, permission `deny`, sandbox) ne le peut pas (ccdocs #10, adlc #3, aws #3, TO #12).
- **Tout ce qui dépasse la demande est proposé, pas fait** (A4).
- **L'agent ne réécrit pas ses propres règles ni ses garde-fous** :
  - ce sont des chemins toujours interdits en écriture (TO #15) ;
  - les garde-fous sont posés par l'humain, et le skill vérifie qu'ils sont là (EK #6) ;
  - un garde-fou se prouve par un appel direct (SALK #14).
- **Le contenu lu est une donnée, jamais une instruction** (EK #32, TO #18).

---

## 2. Contradictions qui se résolvent

Des sources semblent se contredire, mais l'écart s'explique par le contexte. Pour chacune, je propose une lecture. Ces propositions restent à valider avec le reste du tri.

### 2.1 Peut-on réviser l'exigence quand un test échoue ?

- **Positions.** Kiro (#10) : un test de propriété qui échoue peut remettre en cause l'exigence. adlc, bmad et spec-kit : l'oracle est gelé.
- **Résolution.** Les deux protègent le même principe : l'agent ne change jamais l'oracle pour passer. Kiro remonte la question à l'humain ; il ne la tranche pas. La règle commune est celle de spec-kit #12 : l'agent **signale** un attendu qu'il croit faux. Si l'humain constate un **changement d'intention**, il met d'abord la spec à jour, puis on relance.

### 2.2 Dans le doute : échouer ou se taire ?

- **Positions.** Échouer : le correcteur d'AS #12, LR #17, CD #12. Se taire : un constat de revue non confirmé ou sous 80/100 disparaît (AP #17, #19).
- **Résolution.** Elles ne portent pas sur le même objet :
  - pour **valider un travail**, le doute vaut échec ;
  - pour **signaler un défaut**, le doute vaut silence, ou une mention « non vérifié ».

  Les deux s'appuient sur l'asymétrie des coûts : une fausse validation coûte cher, un faux signalement fait perdre du temps et entretient la fatigue.

### 2.3 Que faut-il contre-vérifier ?

- **Positions.** Les verdicts positifs seulement (TO #3), chaque verdict (CD #12) ou chaque constat par plusieurs votes (EK #26).
- **Résolution.** C'est la même logique que 2.2 : on contre-vérifie toujours une **approbation**, et chaque **constat** passe un filtre de vérification. Les votes multiples relèvent du volume (audit de sécurité) et ne sont pas retenus pour l'instant.

### 2.4 Confiance déclarée ou verdict calculé ?

- **Positions.** SALK #12 et AP #19 acceptent une confiance notée par le modèle. AP #24 : « un nombre tapé par le modèle ne compte pas ».
- **Résolution.** Un **verdict de passage** (réussi ou échoué) est calculé ou vérifié mécaniquement. Une **confiance déclarée** sert seulement à trier et à présenter des constats.

### 2.5 Noter le résultat ou le chemin ?

- **Positions.** SALK #10 : noter le résultat. google #15 : évaluer aussi la trajectoire.
- **Résolution.** Les **tests du produit** notent le comportement, jamais l'implémentation. **L'évaluation de JDLC** regarde aussi la trajectoire de l'agent : a-t-il lancé les tests, a-t-il ouvert la preuve ?

### 2.6 Gérer activement la fenêtre de contexte ?

- **Positions.** Pour : YK #1, Erik #7, aidd #17, A2. À arrêter : ccdocs #7.
- **Résolution.** L'écart vient de l'époque : les premières sources datent de 2025, la documentation de novembre 2026. On suit la plus récente : pas de gestion acrobatique de la fenêtre. Écrire l'artefact en fin de phase reste utile, mais pour la **traçabilité et la reprise** (1.5), plus pour économiser des tokens.

### 2.7 Décrire l'architecture dans le contexte chargé ?

- **Positions.** Pour : aidd #8, kiro #3. Contre : ccdocs, bmad #6.
- **Résolution.** Même raison d'époque, plus des études citées par bmad. L'architecture que le code montre ne va pas dans `CLAUDE.md`. Les **décisions et leurs raisons**, invisibles dans le code, vont dans les ADR.

### 2.8 Une seule boucle, ou une douzaine de relecteurs ?

- **Positions.** SALK #16 et #17 : une boucle par défaut, une persona ne justifie pas un sous-agent. CE et AP : plusieurs relecteurs en parallèle.
- **Résolution.** Le critère de SALK #16 tranche. Un sous-agent est légitime quand il faut un **contexte indépendant**, et la vérification en est le cas type. Les « angles » de revue sont justifiés par l'indépendance, pas par une persona. Leur nombre suit la proportionnalité (1.8). Les personas nommées sont écartées (bmad, aws, adlc).

### 2.9 Paralléliser la construction ?

- **Positions.** Par vagues : kiro #7, spec-kit `[P]`. Un seul écrivain : adlc.
- **Résolution.** **Un seul écrivain par ticket** (adlc, [ADR-0008](adr/0008-ticket-unite-de-travail.md)). Plusieurs instances ou développeurs peuvent travailler en parallèle, chacun sur son ticket ; les tickets parents coordonnent ([ADR-0009](adr/0009-spec-a-deux-niveaux.md)). Au sein d'un ticket, le parallélisme sert la recherche et la vérification (adlc, Erik C2), pas la construction.

### 2.10 Entretien d'abord ou code d'abord au cadrage ?

- **Positions.** L'entretien d'abord : LR #32, AS. Le code d'abord : EK #28, CD #10, superpowers #15.
- **Résolution.** Projet neuf : l'entretien d'abord. Projet existant : on lit le code, puis on ne demande que ce que le code ne dit pas.

### 2.11 Une question à la fois, ou un lot ?

- **Positions.** Un lot : AS #24 (5 à 10 questions numérotées), spec-kit #8 (au plus 5). Une à la fois : superpowers #15, AS #27. Une seule par point d'étape : SALK #4.
- **Résolution.** Il y a deux usages :
  - une **porte** pose une seule décision ;
  - une **clarification** pose un lot court et numéroté, avec des réponses abrégées.

  L'usage dira si le lot doit descendre à une question.

### 2.12 Ton impératif et descriptions insistantes

- **Positions.** superpowers, AP #13, LR : ton impératif, majuscules. AS #4, SALK #21, A2 : pas de majuscules. Anthropic se contredit d'un dépôt à l'autre.
- **Résolution.** Par défaut, pas de majuscules ni de menaces. On ne durcit le ton qu'**après un test qui montre l'échec** (principe 1). Même logique pour l'insistance des descriptions (AS #2) : on tranche par un test de déclenchement (AS #15).

### 2.13 Élaguer les tests ou ajouter un canari ?

- **Positions.** CE #7 supprime les tests qui passeraient sur du code cassé. AP #24 ajoute un canari, une ligne cassée exprès qui doit faire échouer les tests.
- **Résolution.** Les deux sont compatibles : le canari **détecte** le test sans valeur, la simplification le **répare ou le supprime**. Supprimer un test touche à la preuve : l'humain doit le voir dans la revue.

### 2.14 Capitaliser à chaud ou en fin de session ?

- **Positions.** À chaud, à la clôture de tâche : CE #5. En fin de session : AP #33.
- **Résolution.** On **capture** à chaud, pendant que le contexte est là, et on **promeut** en règle plus tard, après revue (EK #17, A7).

### 2.15 Phases prédéfinies ou agents libres ?

- **Positions.** A1 : workflows. Erik C2 : « les agents ont dépassé les workflows quand la qualité prime ».
- **Résolution.** Erik #8 concilie les deux. La structure des phases est un workflow ; chaque phase est une **boucle fermée** où l'agent itère jusqu'à une sortie vérifiée.

### 2.16 Commit automatique en fin de session ?

- **Positions.** LR #7 propose un hook qui commite.
- **Résolution.** Écarté : la préférence de l'auteur est de ne commiter que sur demande. On retient seulement l'idée : finir dans un état propre (LR #8).

### 2.17 État local ou état commité ?

- **Positions.** AP #8 : un `.claude/<plugin>.local.md` hors de git. SALK #2 : un plan commité avec le code.
- **Résolution.** AP #8 le dit lui-même : ce qui doit être tracé reste commité. Ce qui sert la **traçabilité et la reprise** est commité (principes 2 et 10). Le fichier local ne garde que des **préférences personnelles**.

---

## 3. Arbitrages pour toi

Ces contradictions touchent la vision, tes préférences de travail, ou un compromis que les sources ne tranchent pas. Pour chacune : les positions, les options et ce qui pèse. Je donne un penchant quand j'en ai un, sans le présenter comme une décision.

### A1. Combien de portes humaines, et où ?

**Le conflit.** La vision (principe 6) en nomme quatre : spec, plan, merge, release.

- 2 portes, l'intention en mots puis en comportement, sans porte de plan (adlc #2) ;
- 3 portes (sfeir #2) ;
- une porte de plan distincte (bmad #3) ;
- 4 portes (superpowers) ;
- 6 décisions humaines (AP #23) ;
- un arrêt après chaque changement (SALK #3).

La fatigue d'approbation (A4) pousse vers le moins de portes possible.

**Options.**

1. **Garder les 4 portes de la vision.** C'est sûr et lisible, mais il y a un risque de tampon sur le plan et la release, surtout pour un développeur seul. En équipe, la porte de merge prend un autre sens : c'est aussi la revue d'un collègue.
2. **2 portes à la ADLC** : approuver l'intention (la spec, avec ses critères et leur méthode de vérification), puis accepter le comportement. Le plan devient un artefact relu seulement sur escalade ou pour un travail à risque.
3. **Des portes selon le profil de travail** : 2 pour un travail borné, 3 ou 4 pour un travail architectural (voir A5).

**Ce qui pèse.** L'option 2 est la mieux argumentée (défaillances du modèle, fatigue d'approbation). Mais elle suppose que la porte « comportement » est vraiment faite : lancer le logiciel, sonder. **Penchant : option 3**, avec 2 portes comme défaut.

### A2. Mode par défaut : supervisé ou autonome ?

**Le conflit.**

- AIDD est en mode auto par défaut, et le mode auto est désormais le défaut de Claude Code (ccdocs).
- La note aidd, YK et CE (`/lfg` écarté) penchent pour le supervisé.
- SALK #6 accorde une autonomie « gagnée » après un premier point d'étape complet, et la retire au premier écart.
- google #12 refuse un défaut global et choisit selon la tâche.
- LR fait tourner l'agent seul pendant des heures, mais ne dit pas ce qu'est une exécution longue « bornée ».

**Options.**

1. **Supervisé par défaut**, l'autonomie s'accordant sur demande explicite.
2. **Autonomie gagnée (SALK #6)** : supervisé jusqu'au premier point d'étape propre, puis « continue tant que c'est propre », avec retour aux arrêts à la première anomalie.
3. **Selon le profil de travail** : autonome pour une feuille bornée, supervisé pour le tronc.

**Ce qui pèse.**

- Le principe 6.
- Le principe 9 : pour pouvoir évaluer, il faut avoir suivi.
- Ton usage : seul pour l'instant, mais pensé pour l'équipe ([ADR-0007](adr/0007-concu-pour-l-equipe.md)). Le mode par défaut pourrait donc être un réglage d'instance, laissé à chaque développeur, avec des limites fixées au niveau projet.
- La visée pédagogique.
- Une contrainte pratique : le sandbox est en alpha sous Windows (TO), alors que l'autonomie longue s'appuie dessus.

**Penchant : option 2.** Il faudra définir la borne d'une exécution autonome : budget, nombre de tâches, conditions de sortie (LR #23).

### A3. Faut-il comprendre tout le code ? Le principe 9 face au zonage feuilles et tronc

**Le conflit.**

- **Le principe 9 dit : n'accepte pas un résultat que tu ne comprends pas.**
- Erik C1 dit : « oublie que le code existe, pas que le produit existe », mais seulement dans les **feuilles**. Les feuilles sont les fonctionnalités terminales dont rien ne dépend. Le **tronc**, l'architecture, doit rester compris.
- adlc #2 : relire tout le diff est du « théâtre » au-delà de quelques centaines de lignes.
- YK #5 : relire la preuve (`TESTING.md`) plutôt que le code.
- La note adlc rappelle qu'en solo pédagogique, lire le code reste parfois le but.

**Options.**

1. **Garder le principe 9 tel quel** : tout ce qui est fusionné est compris.
2. **Préciser le principe 9 par le zonage.**
   - Le **tronc** est compris et relu.
   - Une **feuille** est acceptée sur preuve de comportement : tests lus, logiciel lancé, sondage. On pourra toujours poser des questions sur son code.
3. **Laisser le choix par travail**, à la porte de cadrage.

**Ce qui pèse.** C'est une question de valeurs : apprendre et maîtriser, ou déléguer pour aller vite. Le principe 9 en tient compte avec « ne pas déléguer ce qu'on ne sait pas évaluer », mais il ne fait pas la distinction feuille/tronc. Je ne tranche pas.

### A4. Qui modifie les règles et la mémoire, et quand ?

**Le conflit.**

- **Le principe 11 dit : « on corrige durablement le système ».**
- EK #12 et TO #15 : pendant le travail, les règles, skills et hooks sont **en lecture seule** pour l'agent. Les amendements partent dans une file, et l'humain les applique entre deux phases.
- CD #15 : la mémoire est en lecture seule hors d'un rituel de consolidation.
- EK #18 : l'agent écrit librement dans un journal de leçons, mais ne touche à la politique que par PR.
- aidd et YK : rien n'est écrit sans validation humaine.
- La mémoire automatique de Claude Code et le « rêve » (A7) sont écrits par l'agent.
- **Ta propre consigne globale** : « quand je corrige une erreur, mets à jour la mémoire automatique ». C'est une écriture au fil de l'eau, par l'agent.
- **Le niveau change la donne** ([ADR-0007](adr/0007-concu-pour-l-equipe.md)). La mémoire automatique de Claude Code est au niveau **instance**, et les règles au niveau **projet**. Une leçon écrite librement dans une instance ne profite pas aux autres tant qu'elle n'est pas promue au niveau projet.

**Options.**

1. **Deux vitesses (EK #18).** Le journal de leçons et la mémoire de session s'écrivent librement. Les règles, skills, hooks et `CLAUDE.md` changent seulement sur proposition validée, hors du travail en cours.
2. **Tout sur validation humaine**, y compris la mémoire.
3. **Tout libre**, avec une consolidation périodique qui nettoie.

**Ce qui pèse.**

- La sécurité : une partie notée ne doit pas pouvoir relâcher son propre correcteur.
- La friction : chaque leçon validée à la main coûte de l'attention.
- Ta consigne actuelle, qui correspond à l'option 1 si la mémoire automatique est rangée dans la « vitesse libre ».

**Tranché en partie** par l'[ADR-0010](adr/0010-deux-memoires.md) :

- la mémoire de Claude Code garde ce qui est propre à l'utilisateur ;
- les apprentissages transverses vont dans une mémoire de projet, faite de fichiers versionnés.

**Reste à arbitrer : qui écrit dans la mémoire de projet et dans les règles, et quand.**

**Penchant : option 1, alignée sur les niveaux.** L'instance s'écrit librement : mémoire automatique, journal de leçons local. Le projet ne change que sur proposition validée : règles, skills, hooks, `CLAUDE.md`. La promotion d'une leçon de l'instance vers le projet passe donc par toi. Il faudrait alors préciser le principe 11 : « réparer » veut dire « proposer une réparation ».

### A5. Quel critère pour dimensionner le processus ?

**Le conflit.**

- La taille ou la durée du travail : bmad #2, kiro #2.
- Le risque multiplié par le rayon d'impact, « pas la taille » : adlc #9.
- Le type de travail nommé (`bugfix`, `poc`, `feature`…) : aws #2.
- La zone, feuille ou tronc : Erik #1.

**Options.**

1. **Des profils nommés** (aws), choisis selon le **risque et la zone** (adlc et Erik), proposés par l'agent et confirmés par toi.
2. **La taille seule**, plus simple mais critiquée : un petit changement dans le tronc peut être grave.

**Ce qui pèse.** L'option 1 combine trois sources et rend la proportionnalité visible. Elle ajoute une décision à chaque début de travail, mais une décision rapide. C'est presque une convergence, mais le choix structure tout JDLC, d'où sa place ici. **Penchant : option 1.**

### A6. Quel poids pour JDLC : un cycle à phases, ou une boucle légère ?

**Le conflit.**

- La vision liste 9 phases, sfeir 11, aws 33 étapes (écartées).
- ccdocs constate qu'avec les modèles actuels, **un prompt précis dans une session neuve fait souvent le bon travail**. La configuration ne sert alors que pour ce que Claude ne peut pas voir.
- CE #10 et AS #29 n'utilisent qu'**un seul document qui grossit** : une fiche source, plutôt qu'un artefact par phase.
- dora pousse vers des petits lots et des boucles courtes, CE vers 80 % d'effort en amont.

**Options.**

1. **Garder les 9 phases comme carte**, mais n'outiller que ce qui manque. Une phase n'a pas forcément de skill ni d'artefact propres.
2. **Recentrer JDLC sur une unité de travail** : cadrer, construire, prouver, capitaliser, avec un document unique par changement. Les autres phases deviennent des options.
3. **Décider après le premier composant**, sur l'usage réel (principe 1).

**Ce qui pèse.** Le principe 7 (incrémental) et le principe 1 (empirique) favorisent l'option 3. La valeur pédagogique favorise une carte complète (option 1). **Penchant : 1 et 3 ensemble.** On garde la carte des phases dans la vision, et on n'outille qu'une unité, mesurée, avant d'étendre.

### A7. Combien investir dans la mesure de JDLC ?

**Le conflit.**

- SALK et AS demandent une méthode lourde : baseline, répétitions, test avec et sans, test de déclenchement sur 20 requêtes jouées 3 fois.
- Ces mêmes notes écartent l'outillage complet comme trop lourd en solo, et la vision exclut l'outillage lourd.
- Le principe 1 (empirique) exige pourtant de savoir si un composant aide.

**Options.**

1. **Minimal** : un test avec et sans par skill, avec `claude plugin eval` sur quelques cas, rejoué à chaque nouveau modèle. Plus 2 ou 3 indicateurs tirés de git, comme le taux de reprise ou la taille des changements (dora).
2. **Méthode complète** pour chaque composant : répétitions, test de déclenchement, dépôts d'essai piégés avec corrigé (CD #13).
3. **Pas de mesure formelle** au début : on juge à l'usage, et le journal sert de trace.

**Ce qui pèse.** Le temps disponible face à la crédibilité d'un projet public qui prône la preuve. **Penchant : option 1.**

---

## 4. Questions ouvertes, à trancher par l'usage ou par ADR

Ce ne sont pas des contradictions, mais des choix que les sources laissent ouverts.

| Question | Sources | Quand trancher |
|---|---|---|
| Construire JDLC, importer des skills existants, ou en faire une surcouche ? Les dépôts sous licence libre se sont multipliés (AIDD, SALK, plugins Anthropic). | aidd, SALK, AP | ADR au premier composant |
| Par quel composant commencer ? Plusieurs ordres sont proposés (voir ci-dessous). | sfeir, adlc #15, dora #6, bmad #9 | Prochaine étape |
| Où vit la spec principale, et que remonte une spec de ticket vers elle à la fin du ticket (remontée, nouvelle spec à chaque changement, spec vivante, constitution seule) ? | spec-kit #13, [ADR-0009](adr/0009-spec-a-deux-niveaux.md) | ADR avec la phase Spécification |
| Gabarit de ticket, états et cycle de vie, premier outil de tickets pris en charge, configuration du projet. | [ADR-0008](adr/0008-ticket-unite-de-travail.md), Scrum, Kanban (à étudier) | Avant le premier composant |
| Comment plusieurs instances se coordonnent : prise d'un ticket, ticket parent, travail en cours limité. | [ADR-0007](adr/0007-concu-pour-l-equipe.md), [ADR-0009](adr/0009-spec-a-deux-niveaux.md), Kanban | Avec le gabarit de ticket |
| Installation : hook livré par le plugin ou posé par l'init ; portée utilisateur ou projet ; versionnement ; marqueur de version. | ccdocs, AP #6 à #10 | [ADR-0005](adr/0005-mecanisme-installation.md) |
| Quel modèle pour quelle phase, et qui le choisit ? Le modèle de la revue est discuté : petit (google #16) ou Opus (AP #21). | adlc #14, EK #7 | À l'usage |
| Les hooks et le sandbox fonctionnent-ils sous Windows ? Les hooks de LR sont en `bash`+`python3` ; `srt` est en alpha. | LR, TO | Avant le premier hook |
| Les *dynamic workflows* sont-ils distribuables par plugin, et pour quel coût ? | CD #11 | Si le besoin apparaît |
| Seuils : 2 ou 3 échecs avant de changer de stratégie, plafonds d'itérations, seuil de confiance. Les sources disent elles-mêmes que ce ne sont pas des constantes. | ccdocs #8, adlc #7, EK #11, bmad #13 | À calibrer à l'usage |
| Notation EARS : référence à vérifier avant de la créditer. | kiro #8 | Si JDLC l'adopte |
| Sources signalées non lues : *Effective Harnesses for Long-Running Agents*, article sur le compilateur C, article associé à SALK, livres blancs Google des jours 3 et 5, etc. | voir [CREDITS](../CREDITS.md) | Au besoin |

**Sur le premier composant**, les sources proposent des ordres différents, mais elles se rejoignent :

- sfeir : « poser une vraie porte de spec et la discipline de la preuve, acheter les outils après » ;
- dora #6 : outiller la revue et la vérification **avant** d'accélérer l'implémentation ;
- adlc #15 : commencer par la revue « accusatoire », puis les rails ;
- bmad #9 : l'unité Build.

Le point commun : **la preuve et la vérification d'abord**. Un premier composant plausible serait donc une unité de travail courte (cadrer, construire, prouver), avec :

- un critère « terminé » écrit avant le code ;
- une preuve imposée mécaniquement ;
- une vérification dans un contexte neuf.

Le reste viendrait ensuite, à mesure que l'usage le demande.

## 5. Idées singulières à ne pas perdre

Elles n'apparaissent que dans une source, mais elles sont fortes. On les garde pour la conception.

- **La carte des ruptures** : pour chaque changement, les façons dont il peut casser et le test qui le verrait. Une case vide est un risque assumé, écrit noir sur blanc (SALK #11).
- **Le canari** : une ligne cassée exprès doit faire échouer les tests. **Zéro test vaut échec** (AP #24).
- **Valider l'oracle par mutation** : il doit échouer à chaque mutation volontaire (EK #24).
- **`BLOCKED` n'est pas `FAIL`** : quatre verdicts distincts (LR #17).
- **Vérifier les acquis avant d'ajouter** : on rejoue en début de session une ou deux fonctionnalités déjà validées (LR #4).
- **La configuration qui juge vient de la branche cible** : une modification ne peut pas réécrire les règles qui la jugent (TO #6).
- **Le test du lecteur neuf** : un sous-agent sans contexte répond aux questions qu'un lecteur se posera sur la spec (AS #23).
- **Les reports planifiés** : « pas maintenant » s'écrit en v1, v2…, avec sa raison (AS #31). Cela nourrit la phase Dépréciation.
- **Le bug à trois volets** : comportement actuel, attendu, et **inchangé** (kiro #9).
- **Un test « à froid » des tâches** : un sous-agent neuf lit la tâche seule et liste ce qui manque (adlc #8).
- **Le registre des décisions de l'agent**, avec une liste fermée des motifs d'arrêt (superpowers #11).
- **Montrer l'impact avant une commande risquée**, avec « Annuler » par défaut (TO #16).
- **Le journal des écarts au processus** : une étape sautée produit exactement la classe de défaut qu'elle devait attraper (EK #9).
- **L'orientation utilisateur au cadrage** : sans elle, l'IA a un effet négatif sur la performance d'équipe (dora #8).
- **Préserver l'apprentissage** : ne pas déléguer ce qu'on veut apprendre, et faire expliquer ses choix à l'agent aux portes (dora #11, Erik, mode « guide-moi » de LR #28). C'est cohérent avec les principes 4 et 9.

## 6. Ce que cette synthèse change pour la vision

Rien n'est modifié tant que les arbitrages ne sont pas rendus. Voici ce qui pourrait changer selon les réponses :

- **Principe 6** : le nombre et la place des portes (A1), et le mode par défaut (A2).
- **Principe 9** : préciser le zonage feuilles et tronc (A3).
- **Principe 11** : préciser que « réparer » veut dire « proposer une réparation » (A4).
- **Principes possibles**, issus des convergences les plus fortes :
  - « celui qui fait n'est pas celui qui vérifie » (1.3) ;
  - « l'humain fixe l'oracle » (1.1) ;
  - « chaque composant est une hypothèse, testée avec et sans » (1.7) ;
  - « une garantie plutôt qu'une consigne pour ce qui compte » (1.10).
