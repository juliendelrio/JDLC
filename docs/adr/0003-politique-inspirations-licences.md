# ADR-0003 — Politique d'inspiration et de licences

- **Statut** : Accepté
- **Date** : 2026-11-11

## Contexte

JDLC va s'inspirer de SDLC agentiques existants. Par respect pour leurs auteurs, chaque inspiration doit être créditée. Et comme le projet est public, tout contenu repris doit l'être dans le respect de la licence source.

Il faut distinguer deux choses très différentes :

- **reprendre une idée** (un concept, une méthode, une structure de processus) — les idées ne sont en général pas protégées par le droit d'auteur, mais les créditer est une question d'honnêteté intellectuelle ;
- **reprendre un contenu** (texte, prompt, gabarit, code) — c'est là que la licence s'applique.

## Options envisagées

1. **Ne rien importer, tout réécrire** — zéro risque juridique, mais on se prive de contenus de qualité sous licence permissive.
2. **Importer librement en citant la source** — insuffisant : certaines licences imposent bien plus qu'une citation (copyleft, interdiction de modification…).
3. **Politique explicite** : niveaux de reprise, matrice de compatibilité, procédure d'import et registre des crédits.

## Décision

Option 3, détaillée dans [docs/licences.md](../licences.md) :

- trois niveaux de reprise (**idée**, **adaptation**, **import**) avec des obligations croissantes ;
- toute reprise, quel que soit son niveau, est inscrite dans [CREDITS.md](../../CREDITS.md) ;
- un import n'est possible que si la licence source est compatible avec celle de JDLC ([ADR-0004](0004-licence-du-projet.md)) ;
- la licence d'une source est vérifiée **au moment de l'import**, sur la version exacte reprise (commit ou tag), pas de mémoire.

## Conséquences

- Un peu de rigueur administrative à chaque reprise.
- Le registre des crédits devient lui-même un contenu pédagogique : il montre de quoi JDLC est fait.
- Cette politique n'est pas un avis juridique ; en cas de doute sur une licence, on s'abstient d'importer et on se limite à l'idée.
