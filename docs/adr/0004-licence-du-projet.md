# ADR-0004 — Licence du projet JDLC

- **Statut** : Accepté
- **Date** : 2026-11-11

## Contexte

Le dépôt est public mais n'a pas encore de licence. Sans licence, le contenu est par défaut « tous droits réservés » : personne ne peut légalement le réutiliser, ce qui contredit l'objectif pédagogique.

Le choix de la licence conditionne aussi ce qu'on pourra importer (voir [ADR-0003](0003-politique-inspirations-licences.md)) : une licence permissive peut accueillir du contenu MIT, BSD ou Apache-2.0 ; elle ne peut pas accueillir du contenu GPL.

## Options envisagées

1. **MIT** — courte, permissive, très répandue dans l'écosystème des outils agentiques. Compatible avec l'import de contenus MIT/BSD/ISC/Apache-2.0 (en conservant leurs mentions).
2. **Apache-2.0** — permissive, avec une clause de brevets explicite et l'obligation de signaler les modifications ; plus lourde à appliquer.
3. **MIT pour le code + CC BY 4.0 pour la documentation** — licence adaptée à chaque type de contenu, mais deux régimes à gérer, et la frontière code/doc est floue pour un SDLC (un prompt est-il du code ou du texte ?).
4. **GPL / copyleft** — garantit que les dérivés restent libres, mais freine l'installation dans des projets tiers et interdit d'importer certains contenus permissifs sans conditions.

## Recommandation

**MIT (option 1)** : simplicité, compatibilité maximale avec les sources probables, aucune friction pour installer JDLC dans des projets quelle que soit leur licence.

## Décision

JDLC est publié sous **licence MIT** (fichier [`LICENSE`](../../LICENSE) à la racine), validé par l'auteur le 2026-11-11.

## Conséquences

- Les contenus sous licences permissives compatibles peuvent désormais être importés, selon la procédure de [docs/licences.md](../licences.md).
- Les contenus importés gardent leur propre licence et leurs mentions de copyright ; la licence MIT de JDLC ne s'y substitue pas.
- Les projets qui installent JDLC peuvent avoir n'importe quelle licence, y compris propriétaire.
