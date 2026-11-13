# Glossaire

Ce glossaire fixe le sens des mots de JDLC. Il a été écrit après une relecture de cohérence qui a trouvé plusieurs mots employés dans deux sens ([journal du 2026-11-13](journal/2026-11-13-relecture-de-coherence.md)). Quand un ADR ancien emploie un mot autrement, c'est le sens donné ici qui vaut.

## Qui agit

| Mot | Sens |
|---|---|
| **Humain** | Une personne. Chaque rôle de JDLC dit s'il est tenu par un humain ou par un agent. L'humain d'une porte n'est pas forcément celui d'une autre. |
| **Agent** | Une IA qui agit dans Claude Code. |
| **Rôle d'agent** | Une fonction tenue par un agent pendant la réalisation. Trois rôles sont nommés : garant du cadre, réalisateur, vérificateur ([ADR-0017](adr/0017-cycle-complet.md)). D'autres tâches d'agent ne sont pas encore attribuées à un rôle : relire les specs de tickets, écrire les tests d'acceptation, écrire le bilan, préparer la version. |
| **Garant du cadre** | Le rôle d'agent qui découpe le lot, écrit les specs de tickets et répond au réalisateur à partir du cadre. Le mot « garant » est réservé à ce rôle. Pour JDLC lui-même, on écrit qu'il « fait respecter » les règles. |
| **Réalisateur** | Le rôle d'agent qui planifie, code et teste un ticket. |
| **Vérificateur** | Le rôle d'agent qui contrôle le résultat, sans le contexte du réalisateur. |
| **Profil humain type** | Un archétype d'humain proposé pour tenir une porte : responsable de produit, designer, développeur… Leur liste reste à concevoir. À ne pas confondre avec le profil de travail. |

## Où vit une chose

| Mot | Sens |
|---|---|
| **Instance** | Un environnement de travail : une machine, un poste, une session. L'utilisateur et l'instance sont une seule appartenance ([ADR-0007](adr/0007-concu-pour-l-equipe.md)). |
| **Projet** | Ce qui est commun à toutes les instances : le dépôt, l'outil de tickets. |
| **Appartenance** | Le fait qu'un artefact, un état ou un réglage relève de l'instance ou du projet. Les ADR anciens disent « niveau instance » et « niveau projet » : on préfère désormais « appartenance », pour garder « niveau » au cycle. |
| **Preneur** | L'instance qui tient un ticket. Une seule à la fois. Tous ses agents écrivent sous ce verrou. |

## Le cycle

| Mot | Sens |
|---|---|
| **Cycle** | L'enchaînement complet décrit par l'[ADR-0017](adr/0017-cycle-complet.md), de l'idée à la rétrospective. |
| **Étape** | Une des six étapes du cycle : cadrer et concevoir, prioriser, découper, réaliser, préparer la version, rétrospective. « Étape » désigne aussi, dans un ticket en cours, le moment du travail : tests d'acceptation, réalisation, vérification, contrôles. Dans ce second sens on écrit « étape du ticket ». |
| **Phase** | Le mot des premières hypothèses de la [vision](vision.md) (cadrage, spécification, conception…). Les phases disent quelles questions le cycle traite. Ce sont les étapes qui disent comment il se déroule. Pour décrire le déroulement, on emploie « étape ». |
| **Niveau** | Un des trois niveaux du cycle : produit, lot, ticket. La fonctionnalité n'est pas un niveau : c'est un regroupement de tickets à l'intérieur d'un lot. |
| **Cadre** | Ce qu'un humain valide au niveau du produit et que les agents respectent ensuite : la spec principale, la conception et les exigences de qualité. Un humain choisit où il vit, par défaut dans le dépôt. Il ne change que sur décision d'un humain ([ADR-0020](adr/0020-le-cadre-comme-artefact.md)). |
| **Spec principale** | La partie du cadre qui dit ce qu'on construit : vision, personas, grandes fonctionnalités, critères de succès ([ADR-0009](adr/0009-spec-a-deux-niveaux.md)). |
| **Conception** | La partie du cadre qui dit sur quoi on construit : architecture, modèle de données, choix structurants. Elle délimite le tronc par des chemins de fichiers ([ADR-0020](adr/0020-le-cadre-comme-artefact.md)). |
| **Référence stable** | L'identifiant d'une exigence du cadre, par exemple `F-03`. Une spec de ticket y renvoie sans recopier le texte. |
| **Exigences de qualité** | La partie du cadre choisie dans le guide à tiroirs : sécurité, vie privée, accessibilité… ([ADR-0018](adr/0018-exigences-de-qualite.md)). |
| **Fonctionnalité** | Un élément de la spec principale. Dans l'outil de tickets, c'est un ticket parent. |
| **Lot** | Les fonctionnalités retenues pour une version ou un jalon. L'ordre des priorités forme les lots. |
| **Reporté, écarté** | Deux décisions d'un humain sur une fonctionnalité, à la porte des priorités. Un ticket n'est jamais « reporté » : il change de lot. |
| **Entrée courte** | Le chemin d'un défaut, d'une urgence ou d'une petite demande : un ticket créé sans repasser par le cadre ni par les priorités, mais validé par un humain. |
| **Rétrospective** | L'examen du produit, du cadre et du processus par un humain et un agent, une fois par lot. |

## Les décisions humaines

| Mot | Sens |
|---|---|
| **Porte** | Un moment prévu où un humain décide. Il y en a six : le cadre, les priorités, le découpage du lot, le plan d'un ticket du tronc, la pull request, la publication. Un agent peut exécuter le geste d'une porte, jamais le décider. |
| **Bloqué** | L'état d'un ticket qui attend la réponse d'un humain à une question d'un agent. Ce n'est pas une porte : l'arrêt n'est pas prévu d'avance, sauf en départ supervisé. La même règle s'applique : un agent n'en sort que sur une décision d'un humain. |
| **Renvoi** | Le retour d'un ticket vers le réalisateur, par le vérificateur ou par les contrôles automatiques. Un ticket qui ne respecte pas une règle du cadre est renvoyé. Il ne passe à « bloqué » qu'après trois renvois de suite. Quand un ADR dit que JDLC « peut bloquer », il faut lire : le ticket n'avance pas tant que la règle n'est pas respectée. |
| **Départ supervisé** | Le premier ticket d'un projet ou d'un lot, où l'agent réalisateur s'arrête à chaque étape du ticket devant un humain ([ADR-0012](adr/0012-autonomie-gagnee.md)). |

## Le ticket

| Mot | Sens |
|---|---|
| **Ticket** | L'unité de travail d'un agent réalisateur. Ses neuf états et son gabarit sont dans l'[ADR-0019](adr/0019-etats-et-gabarit-du-ticket.md). |
| **Ticket parent** | Un ticket qui regroupe la spec partagée de plusieurs tickets. Il représente une fonctionnalité. |
| **Spec de ticket** | Ce que le ticket doit faire : intention, critères d'acceptation, ce qui ne doit pas changer. |
| **Critère d'acceptation** | Une condition vérifiable que le résultat doit remplir. Un humain la valide avant la construction. Un agent ne la change pas ensuite. |
| **Test d'acceptation** | Un test écrit à partir d'un critère d'acceptation, avant la réalisation, par un autre agent que le réalisateur. |
| **Preuve** | La sortie réelle qui montre qu'une tâche est faite : résultat des tests, du build, du lint. |
| **Bilan** | La note qu'un agent ajoute au ticket après la fusion : ce qui a coincé, ce qu'un humain a fait corriger, les propositions d'amélioration. |
| **Zone** | L'endroit du code qu'un ticket touche : le **tronc**, dont le reste dépend, ou une **feuille**, dont rien ne dépend ([ADR-0016](adr/0016-comprendre-le-tronc.md)). |
| **Profil de travail** | Le réglage du processus pour un ticket, choisi selon sa zone et son risque ([ADR-0014](adr/0014-profils-de-travail.md)). La liste des profils reste à concevoir. « Feuille » et « tronc » sont des zones, pas des profils. |

## Les décisions de conception

| Mot | Sens |
|---|---|
| **ADR** | Une fiche de décision. Ses statuts sont décrits dans le [registre](adr/README.md). |
| **Précisions** | Une section datée ajoutée à un ADR accepté, quand l'auteur le clarifie ou le complète sans le renverser. |
| **Hypothèse** | Ce qu'est un ADR accepté tant que JDLC n'est pas en version 1.0 : une décision à vérifier par l'usage ([ADR-0011](adr/0011-versionnement.md)). |
