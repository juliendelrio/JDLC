# 2026-11-11 — Synthèse croisée des sources

## Ce qu'on a fait

- Croisé les 23 notes d'analyse dans [la synthèse des sources](../synthese-des-sources.md).
- Méthode en deux temps :
  - quatre agents ont extrait les idées de leur lot de notes, selon une grille commune de 14 thèmes, et relevé les tensions ;
  - la synthèse a ensuite été écrite à la main à partir de ces extraits.

## Ce qu'on a appris

- **Le socle est plus net qu'attendu.** Dix convergences portées par au moins trois sources indépendantes :
  - « terminé » se définit avant de construire, et c'est l'humain qui fixe l'oracle ;
  - pas de preuve, pas de fait ;
  - celui qui fait n'est pas celui qui vérifie ;
  - peu de portes humaines, mais significatives ;
  - l'état du travail vit dans des fichiers ;
  - on capitalise sous la forme la moins chère qui empêche la répétition ;
  - chaque composant est une hypothèse, que l'on retire quand elle ne sert plus ;
  - le processus se proportionne au travail ;
  - les skills s'écrivent comme des briefings et s'évaluent ;
  - les garanties priment sur les consignes pour ce qui compte.
- **Beaucoup de contradictions apparentes se résolvent par le contexte.** Elles opposent souvent un objet à un autre (valider un travail contre signaler un défaut), une époque de modèle à une autre (2025 contre fin 2026), ou un projet neuf à un projet existant. Dix-sept sont traitées dans la synthèse.
- **Sept arbitrages restent à rendre par l'auteur**, parce qu'ils touchent la vision ou des préférences :
  - le nombre de portes ;
  - le mode par défaut ;
  - le principe 9 face au zonage feuilles et tronc ;
  - qui modifie les règles et la mémoire ;
  - le critère de proportionnalité ;
  - le poids de JDLC ;
  - l'investissement dans la mesure.
- **Les sources se rejoignent sur le premier composant** : la preuve et la vérification d'abord, avant d'accélérer l'implémentation.

## Une convergence corrigée, et trois décisions

À la relecture, l'auteur a contesté la convergence « l'état du travail vit dans des fichiers ». C'était un biais des sources, presque toutes pensées pour un développeur seul en local. Dans une équipe, le suivi du travail vit souvent déjà dans un outil de tickets.

La discussion a produit trois décisions :

- **[ADR-0007](../adr/0007-concu-pour-l-equipe.md) — Conçu pour l'équipe, utilisable seul.** Une même personne qui travaille depuis plusieurs environnements forme déjà une équipe. Chaque élément de JDLC appartient à l'**instance** (un environnement) ou au **projet** (commun à toutes les instances). Le travail en équipe sort du « hors périmètre ».
- **[ADR-0008](../adr/0008-ticket-unite-de-travail.md) — Le ticket comme unité de travail.** Le gabarit de ticket est générique, inspiré de Kanban et de Scrum. Le propriétaire du projet choisit où les tickets vivent : dans le dépôt ou dans un outil de tickets. Un ensemble de skills par outil est fourni, et le projet ne charge que le sien.
- **[ADR-0009](../adr/0009-spec-a-deux-niveaux.md) — Une spec à deux niveaux.** Une spec principale, transverse (vision, fonctionnalités, personas), une spec de ticket pour le travail en cours, et des tickets parents pour coordonner.

Une quatrième décision a suivi, sur la mémoire :

- **[ADR-0010](../adr/0010-deux-memoires.md) — Deux mémoires.** La mémoire de Claude Code garde ce qui est propre à l'utilisateur. Une mémoire de projet, faite de fichiers versionnés, reçoit les apprentissages transverses.

La leçon de méthode : **une convergence entre sources qui partagent le même biais n'est pas une preuve.** Il faut se demander pour quel contexte chaque source a été écrite.

## La revue par l'auteur

L'auteur a relu les contradictions et les arbitrages dans un tableur. Le résultat est consigné dans la section 7 de la synthèse.

- Les 17 résolutions proposées sont validées, sauf le commit automatique en fin de session, écarté : une conversation locale se résume sans perte de continuité.
- Quatre arbitrages sont rendus : autonomie gagnée ([ADR-0012](../adr/0012-autonomie-gagnee.md)), validation humaine des règles et de la mémoire de projet ([ADR-0013](../adr/0013-validation-humaine-regles-memoire.md)), profils de travail ([ADR-0014](../adr/0014-profils-de-travail.md)), mesure minimale ([ADR-0015](../adr/0015-mesure-minimale.md)).
- Trois arbitrages étaient mal expliqués : les portes humaines, le principe 9 face au zonage feuilles et tronc, et le poids de JDLC. **Une question mal expliquée ne peut pas être arbitrée** : c'est un défaut de la synthèse, pas du lecteur. Une fois réexpliqués :
  - le principe 9 adopte le zonage : comprendre le tronc, accepter les feuilles sur preuve ([ADR-0016](../adr/0016-comprendre-le-tronc.md)) ;
  - le poids de JDLC sera jugé sur le terrain, avec le premier composant ;
  - les portes attendent un schéma du workflow complet. L'auteur veut voir où sont les arrêts et ce qui tourne en autonomie, pour garder de la fluidité.
- Les skills par outil de tickets seront **un plugin par outil**, avec un chemin d'accès par défaut et des chemins de secours (pour GitHub : CLI, puis MCP, puis API).
- Une idée nouvelle : JDLC doit décrire comment un agent propose de lui-même une amélioration, par une *issue* dans le dépôt GitHub.

## Une relecture de toute la démarche

Après un changement de modèle, l'auteur a demandé une relecture critique de toute la discussion. Elle a relevé :

- **un trou de conception** : une spec de ticket rangée dans un outil de tickets peut être modifiée par d'autres. Décision : ne pas figer davantage, mais travailler en « mode commentaire » pour garder la trace des changements ;
- **des ajouts de l'agent dans des ADR acceptés**, que l'auteur n'avait pas validés. Ils ont été soumis et précisés ;
- **un écart avec notre propre règle** « celui qui fait ne vérifie pas » : les notes et la synthèse n'avaient pas été contrôlées. Un sondage a confirmé huit affirmations sur neuf. La règle est maintenant écrite dans le `CLAUDE.md` du projet ;
- **un écart avec le principe 1** (empirique) : beaucoup de décisions, aucun composant. Réponse de l'auteur : la première implémentation fera la version 0.1.0, et c'est la vérification par l'usage qui permettra la 1.0 ([ADR-0011](../adr/0011-versionnement.md)).

## Suites

- Arbitrer les portes humaines sur le schéma du workflow, puis mettre à jour le principe 6 de la vision.
- Choisir le premier composant et trancher par ADR la question « construire, importer, ou surcouche ».
