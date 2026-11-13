# ADR-0020 — Le cadre comme artefact : où il vit, sa forme, qui le change

- **Statut** : Accepté (hypothèse à vérifier par l'usage, voir [ADR-0011](0011-versionnement.md))
- **Date** : 2026-11-13
- **Inspirations** : [Kiro](../inspirations/kiro.md) (trois fichiers de contexte : produit, technique, structure ; renvois plutôt que copies) ; [Spec Kit](../inspirations/spec-kit.md) (constitution amendée explicitement ; modèles de conservation des specs). Idées seulement, rien n'est importé.

## Contexte

Le cadre est ce qu'un humain valide au niveau du produit et que les agents respectent ensuite : la spec principale, la conception et les exigences de qualité ([ADR-0017](0017-cycle-complet.md), [glossaire](../glossaire.md)). Aucun ADR ne disait où il vit, sous quelle forme, ni qui le change. Trois ADR laissaient ce point ouvert (0009, 0017, 0019). Le garant du cadre et le vérificateur en dépendent entièrement.

## Options envisagées

1. **Un document unique**, qui grossit avec le projet. Simple, mais lourd à charger et difficile à citer.
2. **Tout dans l'outil de tickets.** Proche du suivi, mais mal versionné et dispersé.
3. **Quelques fichiers versionnés, citables par identifiant**, dont l'emplacement est choisi par un humain à la configuration.

## Décision

**Option 3.**

### Où vit le cadre

- **Un humain choisit l'emplacement du cadre à la configuration du projet.**
- **Par défaut, le cadre vit dans le dépôt**, avec le code.
- L'humain peut choisir un autre emplacement parmi les connecteurs que JDLC propose.
- Le cadre appartient au projet, pas à l'instance.

### Sa forme

```
<dossier du cadre>
  produit.md        spec principale : vision, personas, critères de succès,
                    et une courte entrée par fonctionnalité
  conception.md     architecture, modèle de données, choix structurants,
                    et la liste des chemins qui forment le tronc
  qualite.md        exigences de qualité : tiroirs retenus, tiroirs écartés
  decisions/        les décisions structurantes du projet (ADR)
```

- **Chaque exigence porte une référence stable**, par exemple `F-03` pour une fonctionnalité ou `Q-SEC-02` pour une exigence de sécurité. Une spec de ticket renvoie à ces références, sans recopier le texte ([ADR-0009](0009-spec-a-deux-niveaux.md)).
- **Une fonctionnalité a une entrée courte dans `produit.md`** : ce qu'elle apporte, ses critères de succès. Un humain la valide avec le cadre. Le détail partagé par ses tickets est écrit par un agent dans le ticket parent, au découpage ([ADR-0019](0019-etats-et-gabarit-du-ticket.md)).
- **Le tronc est délimité dans `conception.md`, par des chemins de fichiers.** Un agent le propose pendant la conception, un humain le valide avec le reste du cadre. C'est ce qui permet à un agent de proposer la zone d'un ticket, et de déclencher la porte du plan ([ADR-0016](0016-comprendre-le-tronc.md)).
- **`qualite.md` garde aussi les tiroirs écartés**, pour que la décision de l'humain reste lisible ([ADR-0018](0018-exigences-de-qualite.md)).

### Qui le change

- **La porte du cadre est une pull request**, quand le cadre vit dans le dépôt. Un agent rédige ou modifie le cadre sur une branche, un humain décide la fusion. La validation est tracée et le cadre est versionné sans mécanisme de plus.
- **Le cadre n'évolue jamais sur l'action autonome d'un agent.** Un agent peut proposer un changement, quand un ticket est bloqué ou à la rétrospective. Un humain tranche.
- **La proposition est tracée dans le ticket** qui l'a fait naître.
- Les tickets terminés gardent la trace de ce qui a été fait. Le cadre dit ce qui est vrai maintenant.

### Qui le lit

- L'agent **garant du cadre** et l'agent **vérificateur** lisent le cadre.
- L'agent **réalisateur** lit la spec du ticket, et les seules sections du cadre qu'elle cite.
- Le cadre n'est pas chargé en entier à chaque session, pour ne pas alourdir le contexte.

## Conséquences

- Le point « où vit la spec principale », ouvert dans l'ADR-0009, est clos. Le point « ce qui remonte d'un ticket vers la spec principale » aussi : rien ne remonte seul, un agent propose et un humain décide.
- Le versionnement du cadre, laissé ouvert par l'ADR-0017, est réglé quand le cadre vit dans le dépôt. Ce que deviennent les tickets en cours quand le cadre change reste à déterminer par l'usage.
- **Quand un humain choisit un autre emplacement que le dépôt**, le connecteur doit offrir l'équivalent : une validation humaine tracée, un historique des versions et des références stables. Cette exigence envers les connecteurs reste à concevoir, et un connecteur qui ne peut pas la tenir doit le dire à l'humain.
- Restent à concevoir :
  - le nom du dossier et le format de la configuration, avec l'installation ([ADR-0005](0005-mecanisme-installation.md)) ;
  - le format des références stables, et ce qui se passe quand une exigence est retirée ;
  - le gabarit de chaque fichier, et la part lisible par une machine (la liste des chemins du tronc) ;
  - les connecteurs proposés en dehors du dépôt ;
  - la façon dont un agent cadre un projet existant, qui a déjà du code et pas de cadre.
