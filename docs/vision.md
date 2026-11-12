# Vision

## Qu'est-ce qu'un SDLC agentique ?

Un SDLC (*Software Development Life Cycle*) décrit les étapes par lesquelles passe un logiciel, de l'idée à la production. Un SDLC **agentique** redistribue ce travail entre humains et agents IA :

- les agents produisent des artefacts (spécifications, plans, code, tests, revues…) ;
- l'humain cadre, arbitre et valide aux points de contrôle ;
- des conventions écrites (instructions, gabarits, checklists) rendent le comportement des agents reproductible d'un projet à l'autre.

JDLC est ma version de ce cycle.

## Principes directeurs

1. **Empirique d'abord.** Chaque élément du SDLC doit venir d'un besoin rencontré sur un vrai projet, ou d'une pratique existante qu'on a testée. Pas de processus théorique « au cas où ». La version 0.1.0 sera la première implémentation ; tant que JDLC n'est pas en version 1.0, ses décisions sont des hypothèses à vérifier par l'usage ([ADR-0011](adr/0011-versionnement.md)).
2. **Traçabilité des décisions.** Toute décision structurante fait l'objet d'un ADR ([docs/adr/](adr/README.md)). Le *pourquoi* compte autant que le *quoi*.
3. **Traçabilité des inspirations.** Toute idée ou contenu venant d'ailleurs est crédité dans [CREDITS.md](../CREDITS.md), et tout import respecte la licence source ([docs/licences.md](licences.md)).
4. **Pédagogique.** La documentation doit permettre à quelqu'un d'extérieur de comprendre comment le SDLC a été conçu et de reproduire la démarche.
5. **Installable et versionné.** Le SDLC se distribue vers des projets cibles ; il doit pouvoir être mis à jour sans écraser les adaptations locales.
6. **L'humain garde la main.** Les agents proposent, un humain décide à des portes peu nombreuses et connues d'avance : le cadre, les priorités, le découpage de chaque lot, le plan des tickets du tronc, la pull request, la publication. Entre ces portes, les agents travaillent seuls. Au moment du cadrage et de l'architecture, JDLC guide et conseille l'humain sans le bloquer. Une fois le cadre validé, il est garant de ses règles dans les étapes automatisées et d'implémentation, et là il peut bloquer ([ADR-0017](adr/0017-cycle-complet.md), [ADR-0018](adr/0018-exigences-de-qualite.md)).
7. **Incrémental.** On commence petit (une ou deux phases outillées) et on étend.
8. **Discipline de la preuve.** Un agent ne déclare jamais une étape terminée sur parole : il joint la sortie réelle qui le prouve (exécution des tests, build, lint, diff…). On fait confiance à la preuve, pas à l'affirmation. *(Inspiré de [SFEIR](inspirations/sfeir-sdlc-ai.md).)*
9. **Ne pas déléguer ce qu'on ne sait pas évaluer.** L'humain ne confie à un agent que ce qu'il est capable de juger, et n'accepte pas un résultat qu'il ne comprend pas. Ce qu'il doit comprendre dépend de la zone : le code du **tronc**, dont le reste dépend, se comprend avant d'être accepté ; une **feuille**, dont rien ne dépend, peut être acceptée sur preuve de son comportement ([ADR-0016](adr/0016-comprendre-le-tronc.md)). *(Inspiré du [manifeste AIDD](inspirations/aidd.md) et des [conférences d'Erik S.](inspirations/anthropic-talks-erik.md))*
10. **Le contexte se traite comme du code.** Instructions (`CLAUDE.md`), règles, mémoire projet et specs sont versionnés, relus et maintenus avec le même soin que le code. *(Inspiré du [manifeste AIDD](inspirations/aidd.md).)*
11. **Réparer le système avant de pousser le modèle.** Un échec de l'agent est un signal de diagnostic : avant d'insister ou de reformuler, on cherche ce qui manque dans le contexte ou le processus, et on le corrige durablement. L'agent **propose** la correction ; les règles et la mémoire de projet ne changent qu'avec la validation de l'humain ([ADR-0013](adr/0013-validation-humaine-regles-memoire.md)). *(Inspiré du [manifeste AIDD](inspirations/aidd.md) ; rejoint la capitalisation de [SFEIR](inspirations/sfeir-sdlc-ai.md).)*
12. **Pensé pour l'équipe, utilisable seul.** Un développeur seul est le cas particulier d'une équipe ; et une même personne qui travaille depuis plusieurs environnements forme déjà une équipe. Tout élément de JDLC sait s'il appartient à l'**instance** (un environnement de travail) ou au **projet** (commun à toutes les instances). *([ADR-0007](adr/0007-concu-pour-l-equipe.md).)*

## Périmètre (hypothèses de départ)

JDLC couvre tout le cycle de production d'un logiciel, pas seulement le ticket. La section « Le cycle » dit comment ces phases s'enchaînent.

Phases candidates, **à confirmer par l'usage** :

| Phase | Question à laquelle elle répond |
|---|---|
| Cadrage | Quel problème, pour qui, quels critères de succès ? |
| Spécification | Que doit faire le logiciel, précisément ? |
| Conception | Comment va-t-on le construire ? |
| Planification | En quelles tâches découpe-t-on le travail ? |
| Implémentation | Le code et ses tests |
| Revue & vérification | Est-ce correct, sûr, conforme à la spec ? |
| Livraison | Comment on publie et on déploie |
| Retour d'expérience | Qu'est-ce qu'on améliore dans le SDLC lui-même ? |
| Dépréciation | Quel code, quelle fonctionnalité retirer, et comment le faire proprement ? |

Le retour d'expérience est essentiel : c'est lui qui fait évoluer JDLC à partir de l'expérience.

La dépréciation est une phase à part entière, car le code est une dette : retirer ce qui ne sert plus se planifie et se trace comme le reste. *(Inspiré de [SFEIR](inspirations/sfeir-sdlc-ai.md).)*

## Domaine d'application

Un SDLC agentique ne convient pas à tout. Hypothèses de départ, à confirmer par l'usage. *(Grille inspirée de [SFEIR](inspirations/sfeir-sdlc-ai.md).)*

**Adapté :**

- les projets dont le contexte métier peut être explicité par écrit (applications web, API, outils, back-offices, modernisation de code existant) ;
- le travail répétitif et traçable (tests, migrations, documentation, mises à jour de dépendances) ;
- le travail dont le résultat peut être prouvé automatiquement (tests, build, analyse statique).

**Pas (encore) adapté :**

- l'exploration pure, quand le besoin est trop flou pour être spécifié : on prototype d'abord, on applique JDLC ensuite ;
- les systèmes critiques soumis à certification (avionique, ferroviaire, médical…) ;
- les projets manipulant des données sensibles sans règles claires sur ce que les agents peuvent voir.

## Le cycle

Le cycle a trois niveaux : le **produit**, le **lot** (une version ou un jalon) et le **ticket**. *([ADR-0017](adr/0017-cycle-complet.md), qui contient le schéma complet.)*

1. **Cadrer et concevoir.** Un humain et un agent construisent en discussion la spec principale, la conception et les exigences de qualité. Un humain valide ce cadre.
2. **Prioriser.** Un humain décide ce qui est implémenté, reporté ou écarté, et dans quel ordre. Cet ordre fixe les lots.
3. **Découper le lot en cours.** Un agent écrit les tickets, leurs critères d'acceptation et leur profil. Un autre agent relit chaque spec de ticket. Un humain valide le découpage, une fois par lot.
4. **Réaliser les tickets.** Des agents travaillent seuls, sur plusieurs tickets à la fois, dans trois rôles : garant du cadre, réalisateur, vérificateur. Un humain valide le plan des seuls tickets du tronc, puis le résultat de chaque ticket à la pull request.
5. **Préparer la version.** Un agent vérifie le lot entier contre la spec principale. Un humain décide de publier.
6. **Rétrospective du lot.** Un humain et un agent examinent le produit, le cadre et le processus. Les propositions faites au fil de l'eau par les agents l'alimentent.

Un défaut, une urgence ou une petite demande entre directement comme ticket, par un chemin court.

Les exigences de qualité (sécurité, vie privée, accessibilité, éco-conception, conformité…) se choisissent dans un guide à tiroirs : JDLC les propose, l'humain décide. *([ADR-0018](adr/0018-exigences-de-qualite.md).)*

## Unité de travail

L'unité de travail de JDLC est un **ticket** générique, inspiré de Kanban et de Scrum. Le propriétaire du projet choisit où vivent les tickets : dans le dépôt ou dans un outil de tickets (issues GitHub ou GitLab, Jira…). JDLC accède à chaque outil par un ensemble de skills dédié, et un projet ne charge que celui qui lui correspond. *([ADR-0008](adr/0008-ticket-unite-de-travail.md).)*

La spec a deux niveaux. La **spec principale** est transverse : vision, grandes fonctionnalités, personas. La **spec de ticket** porte le travail en cours. Un **ticket parent** peut regrouper la spec partagée de plusieurs tickets. *([ADR-0009](adr/0009-spec-a-deux-niveaux.md).)*

## Hors périmètre (pour l'instant)

- L'outillage organisationnel lourd : rôles, cérémonies imposées, tableaux de bord. Le travail en équipe est, lui, dans le périmètre ([ADR-0007](adr/0007-concu-pour-l-equipe.md)).
- L'outillage lourd (serveur, base de données, interface web).
- La compatibilité avec d'autres outils agentiques que Claude Code.

## Outillage cible

**Claude Code uniquement, pour l'instant** ([ADR-0006](adr/0006-claude-code-uniquement.md)). JDLC exploite librement ses fonctions natives : plugins et marketplace, skills, sous-agents, commandes, hooks, mémoire. Le contenu restant majoritairement du Markdown, une ouverture à d'autres outils reste possible plus tard, mais elle n'est pas un objectif. Installation : voir [ADR-0005](adr/0005-mecanisme-installation.md).
