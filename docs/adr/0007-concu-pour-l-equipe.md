# ADR-0007 — Conçu pour l'équipe, utilisable seul

- **Statut** : Accepté
- **Date** : 2026-11-11
- **Inspirations** : synthèse croisée des sources ([synthese-des-sources.md](../synthese-des-sources.md)), méthodes agiles (Scrum, Kanban)

## Contexte

La vision plaçait « le travail en équipe multi-personnes » hors périmètre : JDLC visait d'abord un usage solo. La plupart des sources étudiées partagent ce biais (outillage local, démos, développeur seul). Elles en tirent des choix qui ne tiennent qu'en solo, comme un état du travail rangé dans des fichiers locaux.

Deux constats remettent ce cadrage en cause :

1. **Une même personne est déjà une équipe.** Elle travaille depuis plusieurs environnements : plusieurs machines, plusieurs clones, des sessions dans le cloud, des agents en parallèle dans des worktrees. Chacune de ces instances doit se comporter comme un développeur distinct, avec son propre état local, et partager le même état de projet.
2. **JDLC doit pouvoir servir dans une vraie équipe** de développeurs séparés.

## Options envisagées

1. **Solo d'abord, équipe plus tard.** C'est plus simple au départ, mais les choix faits pour le solo (état local, pas de notion de partage) seraient à défaire.
2. **Conçu pour l'équipe, utilisable seul.** Chaque élément de JDLC sait dès le départ s'il appartient à l'instance ou au projet. L'usage solo est le cas particulier d'une équipe d'une instance.
3. **Équipe d'abord.** C'est plus lourd, avec des rôles, des cérémonies et une gouvernance, alors que l'usage réel commence en solo.

## Décision

**JDLC est conçu pour fonctionner en équipe et reste utilisable par un développeur seul.**

Tout élément de JDLC (artefact, état, réglage, mémoire, règle) appartient à l'un des deux niveaux suivants :

| Niveau | Définition | Exemples | Où il vit |
|---|---|---|---|
| **Instance** | Propre à un environnement de travail : une machine, un clone, une session cloud, un worktree. Deux instances du même développeur sont deux développeurs pour JDLC. | état de session, préférences personnelles, notes de reprise, mémoire automatique de Claude Code | hors du dépôt partagé, ou dans des fichiers locaux ignorés par git |
| **Projet** | Commun à toutes les instances du dépôt, pour tous les membres de l'équipe. | spec principale, ADR, règles, `CLAUDE.md`, suivi du travail | le dépôt (commité) ou l'outil de tickets partagé |

## Conséquences

- La vision est mise à jour : le travail en équipe sort du « hors périmètre », et un principe « pensé pour l'équipe, utilisable seul » est ajouté.
- **Ce qui sert à plusieurs instances doit être au niveau projet.** Une leçon apprise dans une instance, comme la mémoire automatique de Claude Code (enregistrée par machine), ne profite pas aux autres tant qu'elle n'est pas promue au niveau projet. La capitalisation doit prévoir cette promotion.
- **Les niveaux de réglage de Claude Code s'alignent naturellement** : utilisateur et local pour l'instance, projet commité pour le projet. JDLC s'appuie sur cette distinction plutôt que d'en inventer une.
- **Plusieurs instances peuvent travailler en même temps.** Il faut éviter deux écrivains sur le même travail : une unité de travail est prise par une seule instance à la fois (voir [ADR-0008](0008-ticket-unite-de-travail.md)).
- Les méthodes agiles, faites pour des équipes, deviennent des sources pertinentes. Elles sont ajoutées aux sources à étudier.
- On ne reprend pas l'outillage organisationnel lourd (rôles, cérémonies imposées, tableaux de bord). Les sources qui l'écartaient « parce que solo » sont à relire avec ce nouveau cadrage.
