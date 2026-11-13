# 2026-11-13 — Relecture de cohérence avant de construire

## Ce qu'on a fait

- Fait relire tout le dépôt par un sous-agent sans le contexte de la conversation : les dix-neuf ADR, la vision, `CLAUDE.md`. Sa consigne était de chercher les contradictions et les phrases périmées, sans juger les choix.
- Corrigé les phrases périmées et les écarts d'alignement.
- Tranché six points, consignés dans l'[ADR-0019](../adr/0019-etats-et-gabarit-du-ticket.md) et en précisions des ADR 0008, 0012 et 0017.
- Écrit un [glossaire](../glossaire.md), pour régler les mots employés dans deux sens.
- Décidé la forme du cadre : [ADR-0020](../adr/0020-le-cadre-comme-artefact.md).
- Posé une exigence nouvelle de l'auteur, l'accompagnement de l'humain : [ADR-0021](../adr/0021-accompagner-l-humain.md), et le principe 13 de la vision.

## Ce qu'on a appris

- **Deux ADR écrits le même jour se contredisaient déjà.** L'entrée courte ne suivait pas le même chemin dans l'ADR-0017 et dans l'ADR-0019. Le départ supervisé n'avait pas d'état. Écrire vite produit des incohérences que l'auteur ne voit pas : la relecture par un contexte neuf les trouve.
- **Des règles anciennes ne tenaient plus avec des agents autonomes.** « Une permission explicite avant chaque écriture » et « un seul écrivain par ticket » dataient d'avant le cycle. Avec des agents seuls sur plusieurs tickets, la permission se donne une fois à l'initialisation, et l'écrivain unique est l'instance.
- **Une porte se définit par qui décide, pas par qui exécute.** Un humain peut demander une fusion ou la validation d'un lot à un agent. La règle devient : un agent ne franchit une porte que sur une décision tracée d'un humain.
- **Les tests d'acceptation viennent avant le code**, sur le modèle du développement piloté par les tests.
- **L'humain choisit où vit le cadre.** La recommandation était « dans le dépôt ». L'auteur en a fait un défaut, pas une obligation : un humain peut choisir un autre emplacement parmi les connecteurs proposés. Cela crée une exigence envers ces connecteurs, qui reste à concevoir.
- **« Dire ce qu'on fait, faire ce qu'on dit. »** JDLC doit être simple à utiliser, pédagogique, et renvoyer à la documentation. Un module optionnel pourra signaler à un humain qu'il sort du cadre du framework, avec un coût en tokens annoncé d'avance.

## Limites de cette session

- Six des quatorze constats du sous-agent ont été contrôlés dans le texte. Ils étaient exacts. Les autres n'ont pas été contrôlés un par un.
- Le développement piloté par les tests est cité comme idée générale. Il n'a pas été étudié comme source.

## Ce qui reste ouvert

- Quelques trous mineurs : l'abandon d'un ticket depuis « prêt » ou « en revue », le retour d'un plan « à revoir », les états du ticket parent.
