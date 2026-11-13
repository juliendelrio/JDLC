# ADR-0009 — Une spec à deux niveaux : la spec principale et la spec de ticket

- **Statut** : Accepté (principe). Les formats et le cycle de vie restent à concevoir.
- **Date** : 2026-11-11
- **Inspirations** : [Spec Kit](../inspirations/spec-kit.md) (constitution, spec par fonctionnalité, modèles de conservation des specs) ; [Kiro](../inspirations/kiro.md) (contexte persistant `product.md`) ; méthodes agiles (Scrum, Kanban)

## Contexte

Les sources placent la spec à des endroits différents :

- Spec Kit et Kiro mettent une spec par fonctionnalité dans le dépôt, plus un contexte de projet (constitution, `product.md`) ;
- AIDD et ADLC partent d'un ticket ;
- compound engineering utilise un seul document qui grossit.

Avec le ticket comme unité de travail ([ADR-0008](0008-ticket-unite-de-travail.md)), la question « la spec vit-elle dans le ticket ou dans le dépôt ? » se pose. Une seule spec ne peut pas porter à la fois ce qui est commun à tout le projet et ce qui est propre au travail en cours.

## Options envisagées

1. **Tout dans le ticket.** La spec reste près de la discussion, mais la vision et les éléments transverses se dispersent entre les tickets.
2. **Tout dans le dépôt.** La spec est versionnée avec le code, mais elle est séparée du suivi du travail quand l'équipe utilise un outil de tickets.
3. **Deux niveaux.** Une spec principale, transverse, au niveau projet. Une spec de ticket, pour le travail en cours. Des tickets parents pour coordonner plusieurs tickets.

## Décision

La spec est séparée en deux niveaux :

- **La spec principale** porte tout ce qui est transverse à tous les tickets : la vision du produit, les grandes fonctionnalités, les personas, les contraintes communes. Elle relève du niveau projet.
- **La spec de ticket** porte ce qui concerne le travail en cours : l'intention, les critères d'acceptation et leur méthode de vérification, et ce qui ne doit pas changer.

**Un ticket parent** peut regrouper la spec partagée par plusieurs tickets, pour coordonner leur travail (l'équivalent d'un epic).

## Conséquences

- **Une spec de ticket s'appuie sur la spec principale sans la recopier.** Elle y renvoie.
- **La spec de ticket suit le ticket** : elle vit là où le projet stocke ses tickets (ADR-0008).
- **Il reste à décider où vit la spec principale** (le dépôt semble naturel, car elle évolue lentement et se relit comme du code) et **ce qui remonte d'une spec de ticket vers la spec principale** quand le ticket se termine. Spec Kit distingue plusieurs modèles de conservation (remontée, nouvelle spec à chaque changement, spec vivante, constitution seule). Ce sera l'objet d'un ADR au moment de concevoir la phase Spécification.
- **L'oracle reste fixé par l'humain** (convergence 1.1 de la synthèse). Les critères d'acceptation d'une spec de ticket sont validés par l'humain avant la construction.
- **Les tickets parents posent la question de la coordination** entre plusieurs instances ou développeurs. Elle sera étudiée avec Kanban et Scrum.

## Précisions du 2026-11-11

Une relecture a relevé un risque : une spec de ticket rangée dans un outil de tickets peut être modifiée par d'autres, et n'est pas versionnée avec le code. Cela fragilise l'idée d'un attendu fixé par l'humain.

Décision de l'auteur : **on ne fige pas davantage la spec de ticket.** En revanche, **les changements doivent laisser une trace** : on travaille en « mode commentaire ». Une modification de la spec de ticket passe par un commentaire, ou s'accompagne d'un commentaire, qui dit ce qui change et pourquoi. L'historique reste ainsi lisible par l'humain comme par l'agent.

Conséquences :

- Un agent qui propose de changer un attendu **justifie le changement à l'humain** (voir la contradiction 2.1 de la [synthèse](../synthese-des-sources.md)).
- Avant de s'appuyer sur une spec de ticket, l'agent lit aussi les changements survenus depuis sa dernière lecture.
- La forme exacte du suivi dépend de l'outil de tickets et du stockage choisi. Elle reste à concevoir.

## Précisions du 2026-11-12

- **La validation humaine des critères d'acceptation se fait à la porte du découpage**, avant la construction. L'humain peut valider tout un lot d'un coup, ou ticket par ticket. Avant elle, un agent autre que le rédacteur relit chaque spec de ticket. Voir l'[ADR-0017](0017-cycle-complet.md).
- **Les exigences de qualité rejoignent le cadre**, à côté de la spec principale et de la conception. Voir l'[ADR-0018](0018-exigences-de-qualite.md).
- **Après la validation humaine, un agent ne change pas un critère d'acceptation.** Si un changement est nécessaire, il repasse par un humain. Le reste de la spec de ticket continue d'évoluer en « mode commentaire ». Voir l'[ADR-0019](0019-etats-et-gabarit-du-ticket.md).

## Précisions du 2026-11-13

- **L'emplacement de la spec principale est décidé** par l'[ADR-0020](0020-le-cadre-comme-artefact.md) : un humain le choisit à la configuration, et c'est le dépôt par défaut.
- **Rien ne remonte seul d'une spec de ticket vers la spec principale.** Un agent propose le changement, un humain décide, et la proposition est tracée dans le ticket.
- **Une spec de ticket renvoie au cadre par des références stables**, sans recopier.
