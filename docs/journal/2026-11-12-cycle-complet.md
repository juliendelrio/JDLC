# 2026-11-12 — Le cycle complet, décidé sur un schéma

## Ce qu'on a fait

- Tranché l'arbitrage A1 de la [synthèse](../synthese-des-sources.md) (combien de portes humaines, et où) : [ADR-0017](../adr/0017-cycle-complet.md).
- Décidé la place de la qualité et de la sécurité : [ADR-0018](../adr/0018-exigences-de-qualite.md).
- Mis à jour la [vision](../vision.md) : principe 6, périmètre, nouvelle section « Le cycle ».
- Décidé les états, les portes et le gabarit du ticket : [ADR-0019](../adr/0019-etats-et-gabarit-du-ticket.md).

## Comment on y est arrivé

- **Par un schéma, pas par une liste d'options.** La question « combien de portes ? » n'était pas compréhensible telle qu'elle était écrite dans la synthèse. Un diagramme d'états, corrigé dix fois, a permis de décider.
- **Premier essai rejeté : un cycle par ticket.** JDLC couvre tout le cycle de production d'un logiciel. Le ticket n'en est qu'un niveau, sous le produit et le lot.
- **Une priorisation ajoutée après le cadre.** Valider le cadre ne dit pas quoi construire maintenant.
- **Deux relectures par des sous-agents, sans le contexte de la conversation :**
  - la première cherchait les contradictions avec les ADR existants ;
  - la seconde confrontait le cycle aux pratiques du génie logiciel.

## Ce qu'on a appris

- **Le schéma contredisait trois ADR acceptés la veille** (0009, 0012, 0014). En retirant les portes du milieu, on avait retiré la validation humaine des critères d'acceptation, le départ supervisé et les profils de travail. Un relecteur sans contexte l'a vu tout de suite.
- **Cinq constats sont revenus dans les deux relectures**, qui n'avaient ni le même contexte ni la même consigne :
  - l'attendu du ticket n'était validé par aucun humain ;
  - le garant du cadre jugeait son propre travail ;
  - la pull request devient un goulot dès qu'on parallélise ;
  - la boucle d'échec n'avait ni plafond ni état « bloqué » ;
  - il n'y avait pas d'entrée pour un défaut ou une urgence.
- **Dire « un humain » et « un agent », pas « toi ».** Le schéma s'adressait à l'auteur. Nommer qui est humain et qui est agent à chaque rôle a rendu les questions plus nettes, et c'est cohérent avec un JDLC conçu pour l'équipe.
- **Sur le goulot de la pull request, l'auteur n'a pas suivi la recommandation.** Pas de limite d'en-cours : l'objectif est de produire en masse. La réponse retenue est un skill qui aide l'humain à trier les pull requests.
- **JDLC conseille au cadrage, et garantit ensuite.** Pour la qualité et la sécurité, le choix est un guide à tiroirs dont l'humain reste décisionnaire. Une fois le cadre validé, JDLC est garant de ses règles pendant la réalisation, et peut bloquer. Une première rédaction disait « JDLC ne bloque jamais » : l'auteur l'a corrigée.
- **L'état d'un ticket dit qui doit agir.** Neuf états suffisent, dont quatre où un humain doit agir. Une porte devient ainsi une règle simple à garantir : un agent n'en sort jamais seul. « Reporté » n'est pas un état : c'est un changement de planification.
- **Une relecture de cohérence de tout le dépôt**, par un sous-agent sans contexte, a trouvé quatorze constats après l'écriture de l'ADR-0019. Les plus sérieux sont entre les ADR 0017 et 0019, écrits le même jour : l'entrée courte n'y suit pas le même chemin, et le départ supervisé n'a pas d'état. Six constats ont été contrôlés dans le texte et sont exacts.

## Limites de cette session

- Les références de génie logiciel de la seconde relecture sont citées de mémoire par le sous-agent. Elles n'ont pas été vérifiées et ne sont donc pas reprises dans les ADR.
- Trois citations d'ADR de la première relecture ont été contrôlées et sont exactes. Le reste de ses constats n'a pas été contrôlé ligne à ligne.
- Le dialogue entre deux agents en cours de travail n'a pas été vérifié dans la documentation de Claude Code.
- Kanban et Scrum n'ont pas été étudiés comme sources avant de décider les états du ticket.

## Ce qui reste ouvert

- La liste est dans les conséquences de l'[ADR-0017](../adr/0017-cycle-complet.md) : la conception comme artefact, les profils humains types, le skill de tri des pull requests.
- Le changement du cadre en cours de lot est laissé à l'usage.
