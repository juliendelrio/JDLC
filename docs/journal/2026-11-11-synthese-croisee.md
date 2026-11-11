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

## Suites

- Rendre les arbitrages, puis mettre à jour la vision (principes 6, 9 et 11, et peut-être de nouveaux principes).
- Choisir le premier composant et trancher par ADR la question « construire, importer, ou surcouche ».
