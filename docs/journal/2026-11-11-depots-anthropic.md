# 2026-11-11 — Les dépôts publics d'Anthropic

## Ce qu'on a fait

- Analysé le *single agent loop kit*, puis passé en revue le compte [github.com/anthropics](https://github.com/anthropics) (une centaine de dépôts publics).
- Retenu 27 dépôts liés au développement avec Claude Code et écarté le reste : SDK, forks de projets tiers, recherche, plugins métier.
- Réparti les 27 dépôts en six familles, chacune analysée par un sous-agent en parallèle. Chaque sous-agent a écrit une seule note et vérifié chaque licence au commit étudié.
- Ajouté sept notes, une vingtaine de lignes dans `CREDITS.md` et une mise à jour de l'[ADR-0005](../adr/0005-mecanisme-installation.md).

## Ce qu'on a appris

- **Anthropic publie des skills Claude Code longs, à étapes et à validations humaines.** C'est la forme que prendront les composants de JDLC. Le *single agent loop kit*, `code-modernization` et le *code migration kit* sont des modèles directs.
- **Les mêmes idées reviennent d'un dépôt à l'autre** :
  - des points d'arrêt humains fixes, au format court, avec une seule question ;
  - un fichier d'état dans le dépôt pour reprendre le travail ;
  - un vérificateur à contexte neuf, qui cite ses preuves et n'écrit rien ;
  - « échec par défaut » : rien n'est validé sans preuve, parfois imposé par un hook ;
  - tester chaque skill avec et sans lui, et le supprimer s'il n'apporte rien.
- **Pour l'installation**, le motif « moteur dans le plugin, règles dans le projet » répond à une question ouverte de l'ADR-0005.
- **La licence d'un dépôt ne suffit pas.** Elle varie par dossier :
  - `doc-coauthoring` n'a aucune licence ;
  - le README de `autonomous-coding` dit « Internal Anthropic use » malgré la licence MIT à la racine ;
  - `claude-security` est propriétaire au milieu d'un dépôt Apache-2.0.

  La règle « vérifier au commit exact » s'étend donc au **dossier exact**.

## Suites

- La synthèse croisée des sources devient urgente : il y a maintenant 23 notes, avec beaucoup de recoupements.
- Deux articles d'Anthropic sont à lire : *Effective Harnesses for Long-Running Agents* et le récit du compilateur C.
- Plusieurs dépôts sous Apache-2.0 sont de vrais candidats à une adaptation, à décider au moment de concevoir les composants.
