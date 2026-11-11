# Vision

## Qu'est-ce qu'un SDLC agentique ?

Un SDLC (*Software Development Life Cycle*) décrit les étapes par lesquelles passe un logiciel, de l'idée à la production. Un SDLC **agentique** redistribue ce travail entre humains et agents IA :

- les agents produisent des artefacts (spécifications, plans, code, tests, revues…) ;
- l'humain cadre, arbitre et valide aux points de contrôle ;
- des conventions écrites (instructions, gabarits, checklists) rendent le comportement des agents reproductible d'un projet à l'autre.

JDLC est ma version de ce cycle.

## Principes directeurs

1. **Empirique d'abord.** Chaque élément du SDLC doit venir d'un besoin rencontré sur un vrai projet, ou d'une pratique existante qu'on a testée. Pas de processus théorique « au cas où ».
2. **Traçabilité des décisions.** Toute décision structurante fait l'objet d'un ADR ([docs/adr/](adr/README.md)). Le *pourquoi* compte autant que le *quoi*.
3. **Traçabilité des inspirations.** Toute idée ou contenu venant d'ailleurs est crédité dans [CREDITS.md](../CREDITS.md), et tout import respecte la licence source ([docs/licences.md](licences.md)).
4. **Pédagogique.** La documentation doit permettre à quelqu'un d'extérieur de comprendre comment le SDLC a été conçu et de reproduire la démarche.
5. **Installable et versionné.** Le SDLC se distribue vers des projets cibles ; il doit pouvoir être mis à jour sans écraser les adaptations locales.
6. **L'humain garde la main.** Les agents proposent, l'humain décide aux étapes clés (validation de spec, de plan, de merge, de release).
7. **Incrémental.** On commence petit (une ou deux phases outillées) et on étend.
8. **Discipline de la preuve.** Un agent ne déclare jamais une étape terminée sur parole : il joint la sortie réelle qui le prouve (exécution des tests, build, lint, diff…). On fait confiance à la preuve, pas à l'affirmation. *(Inspiré de [SFEIR](inspirations/sfeir-sdlc-ai.md).)*

## Périmètre (hypothèses de départ)

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

## Hors périmètre (pour l'instant)

- Le travail en équipe multi-personnes (JDLC vise d'abord un usage solo).
- L'outillage lourd (serveur, base de données, interface web).

## Outillage cible

Point de départ : **Claude Code**, l'outil que j'utilise au quotidien. Le contenu méthodologique (processus, gabarits, checklists) sera écrit en Markdown autant que possible indépendant de l'outil, afin de pouvoir l'adapter à d'autres agents plus tard. Voir [ADR-0005](adr/0005-mecanisme-installation.md).
