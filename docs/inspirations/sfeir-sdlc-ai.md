# SFEIR — « Un SDLC piloté par l'IA : le cycle SFEIR à 11 phases »

| | |
|---|---|
| **Source** | https://www.sfeir.com/articles/sdlc-ai/ |
| **Auteur** | SFEIR AI (SFEIR) |
| **Date** | Publié le 2026-06-16, mis à jour le 2026-09-21 |
| **Consulté le** | 2026-11-11 |
| **Licence** | © 2026 SFEIR, tous droits réservés |
| **Niveau de reprise autorisé** | **Idée uniquement** : aucun texte, schéma ni tableau n'est copié ([politique](../licences.md)) |

## Synthèse

### Thèse

En 2025, beaucoup d'entreprises ont multiplié les preuves de concept IA sans changer leur cycle de production : les outils ont changé, pas le modèle opératoire. Pour l'article, l'IA dans l'ingénierie logicielle est d'abord un problème de **modèle opératoire**, pas d'outillage.

Les approximations que le rythme humain absorbait deviennent des défauts industriels à la vitesse de l'IA :

- specs floues ;
- revues bâclées ;
- documentation repoussée.

Greffer l'IA sur un cycle pensé pour le rythme humain ne fait que produire des erreurs plus vite. Le facteur limitant n'est plus d'écrire le code, mais de **spécifier et vérifier**.

### Le cycle : 11 phases, 3 portes humaines, 2 moments de capitalisation

| # | Phase | Rôle |
|---|---|---|
| 0 | **Setup** | Détection de la stack, initialisation de la mémoire projet |
| 1 | **Define** 🚪 | Spécification |
| 2 | **Plan** 🚪 | Plan d'architecture |
| 3 | **Build** | Implémentation par tranches, une à la fois, sans dérive de périmètre |
| 4 | **Verify** | Tests avec mesure de couverture |
| 5 | **Review** | Plusieurs revues en parallèle (code, sécurité, tests, performance) consolidées en un verdict |
| 6 | **Compound-1** | Leçons « statiques », trouvées avant la mise en production |
| 7 | **Ship** 🚪 | Checklist de production, plan de retour arrière, déploiement progressif |
| 8 | **Ops** | Observation de 7 à 14 jours après la livraison |
| 9 | **Compound-2** | Leçons « runtime », apprises sous charge réelle |
| 10 | **Deprecation** | Retrait du code annoncé et capitalisé (« le code est une dette ») |

🚪 = porte humaine. Tout le reste est automatisé, mais la responsabilité reste humaine. Les deux phases *Compound* réinjectent les leçons comme règles automatiques dans la phase Plan du cycle suivant. Maxime : *un bug vu deux fois est un trou dans le système*.

Les phases se regroupent en quatre temps :

- **Amont** (Setup, Define, Plan) : c'est là que l'humain pèse le plus. Avant d'arbitrer, on mène trois explorations en parallèle : le code existant, les pratiques et les frameworks.
- **Cœur** (Build, Verify, Review).
- **Capitalisation** (Compound-1 et Compound-2).
- **Aval** (Ship, Ops, Deprecation).

### Trois convictions

1. **L'IA exécute** au lieu d'assister : les agents produisent les artefacts (code, tests, documentation).
2. **L'humain pilote** : l'intention produit, les arbitrages d'architecture et l'acceptation des changements ne se délèguent pas.
3. **Le projet apprend** : chaque cycle ajoute des leçons, des règles et des standards versionnés.

S'y ajoute une **discipline de la preuve** : on capture la sortie réelle de chaque étape, par exemple le résultat d'exécution des tests, jamais l'affirmation de l'agent. L'article la présente comme un principe opératoire, pas comme une métrique.

### Résultats annoncés

**Chiffres propres à SFEIR** (non vérifiables) :

- refonte d'un site corporate passée de 6 mois à 1 jour, cas présenté comme non généralisable ;
- 30 % d'itérations de correction en moins après dix cycles, attribués à la capitalisation et non au modèle ;
- coût « augmenté » d'environ 10 €/h.

**Chiffres d'autres acteurs**, cités à part par l'article et non agrégés avec les siens : Salesforce, AWS, Google et DORA 2025 (« l'IA est un amplificateur », des forces comme des dysfonctionnements).

### Convergence avec d'autres approches

L'article situe son cycle par rapport à ADLC (Chris Williams), « The New SDLC With Vibe Coding » (Google), Spec Kit, Kiro, BMAD, Superpowers, AI-DLC et le *compound engineering*. Il y voit une convergence de l'industrie.

Il relève une divergence de surface avec ADLC, qui a deux portes humaines (spec et acceptation) contre trois chez SFEIR, qui ajoute le plan d'architecture. Les deux approches s'accordent sur le fond : l'humain fixe l'intention, la machine vérifie le reste.

### Domaine d'application

| | |
|---|---|
| **Adapté** | Contexte métier explicable (back-offices, API, portails, modernisation), travail répétitif et traçable (tests, migrations, documentation, conformité), résultats prouvables automatiquement |
| **Pas encore adapté** | Travail inédit sans spec possible, systèmes critiques certifiables (DO-178C, EN 50128), données non gouvernées |

### Conclusion de l'article

- Le code est un sous-produit ; le vrai actif est la connaissance accumulée par le projet.
- Pour démarrer : poser une vraie **porte de spécification** et une **discipline de la preuve**, et acheter les outils seulement après.
- Règle de fond : *on standardise le contexte et la preuve, pas l'outil*.

## Ce que JDLC peut en retenir

| # | Idée | Ce que ça donnerait dans JDLC | Lien avec l'existant |
|---|---|---|---|
| 1 | **Discipline de la preuve** | Nouveau principe directeur : un agent ne déclare jamais « c'est fait » sans joindre la sortie réelle (tests, build, lint). Candidat fort pour un hook ou une checklist. | À ajouter à [vision.md](../vision.md) |
| 2 | **Portes humaines explicites et peu nombreuses** | Rendre précis notre principe « l'humain garde la main » : quelles portes, quel livrable validé à chacune. Question ouverte : 2 portes (style ADLC) ou 3 (style SFEIR) pour un usage solo ? | Principe 6 de la vision |
| 3 | **Capitalisation en deux temps** | Scinder notre phase « Retour d'expérience » en leçons *avant livraison* et *après livraison*. Les leçons deviennent des règles versionnées réinjectées dans la planification. Règle : un problème vu deux fois devient une règle. | Phase « Retour d'expérience » ; c'est aussi ce qui fait évoluer JDLC lui-même |
| 4 | **Phase 0 « Setup »** | Détection de la stack et initialisation de la mémoire projet à l'installation : correspond exactement à la commande `init` envisagée. | [ADR-0005](../adr/0005-mecanisme-installation.md) |
| 5 | **Standardiser le contexte et la preuve, pas l'outil** | Conforte le choix d'un cœur méthodologique indépendant de l'outil. | ADR-0005, vision (outillage cible) |
| 6 | **Build par tranches, sans dérive de périmètre** | Une tranche = un incrément vérifiable ; l'agent refuse d'élargir le périmètre en cours de route. | Phases Planification et Implémentation |
| 7 | **Revue multi-angles en parallèle → verdict consolidé** | Plusieurs sous-agents de revue (correction, sécurité, tests, performance) dont les avis sont fusionnés. | Phase « Revue & vérification » |
| 8 | **Phase de dépréciation** | Absente de nos phases candidates ; le retrait de code mérite d'être une étape à part entière. | Périmètre de la vision |
| 9 | **Explorations amont en parallèle** | Avant de spécifier ou de concevoir, explorer le code existant, les pratiques et les frameworks. | Phases Cadrage et Conception |
| 10 | **Domaine d'application explicite** | Documenter quand JDLC convient et quand il ne convient pas. | Nouvelle section de la vision |
| 11 | **Par où commencer** | La priorité de l'article (porte de spec + preuve) est une bonne candidate pour la première phase à outiller. | Prochaines étapes du journal |

## Ce qu'on écarte ou relativise

- **Les rôles d'équipe** (PO, architecte, QA, SRE…) : JDLC vise d'abord un usage solo. On garde les *responsabilités* sans les *rôles*.
- **La phase Ops sur 7 à 14 jours** et le déploiement progressif : utiles en contexte industriel, souvent disproportionnés pour des projets personnels. On les garde comme options.
- **Les chiffres de performance** : ce sont des affirmations de l'éditeur ou d'acteurs tiers, non vérifiables. On ne s'en sert pas comme arguments.
- **Le contexte commercial** : l'article est aussi une vitrine pour une entreprise de services numériques. Les idées valent pour elles-mêmes, pas par les résultats annoncés.

## Sources secondaires à étudier

L'article cite plusieurs sources ajoutées à la liste « à étudier » de [CREDITS.md](../../CREDITS.md) : ADLC (Chris Williams), « The New SDLC With Vibe Coding » (Google), le rapport DORA 2025, Kiro et le *compound engineering*.
