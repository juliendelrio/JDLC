# ADLC : Agentic Development Lifecycle (Chris Williams)

Chris Williams publie l'ADLC sous deux formes, étudiées ensemble ici :

- une **série d'articles** de blog en 10 parties, *The Agentic Development Lifecycle*, qui expose la thèse ;
- le dépôt **`voodootikigod/adlc`**, qui contient le texte de référence `ADLC.md` (version 0.3) et une boîte à outils de portes déterministes, installable notamment comme plugin Claude Code.

> Attention à l'homonymie : plusieurs autres publications utilisent le sigle « ADLC » pour des choses différentes (cycle de vie d'agents IA en production chez EPAM, *automated development life cycle* dans `raunakkathuria/adlc`…). Cette note ne concerne que l'ADLC de Chris Williams.

| | Série d'articles | Dépôt et texte de référence |
|---|---|---|
| **Source** | https://www.voodootikigod.com/series/adlc (partie 1 : https://www.voodootikigod.com/adlc-1-models-arent-human ; partie 2 : https://www.voodootikigod.com/adlc-2-two-human-gates) | https://github.com/voodootikigod/adlc (fichier `ADLC.md`) ; site de documentation : https://www.agenticlifecycle.ai/ |
| **Auteur** | Chris Williams (*voodootikigod*) | Chris Williams |
| **Version étudiée** | Parties 1 et 2 publiées le 2026-06-12 ; parties 3 à 10 repérées mais non lues | commit `0da571cfef15ca1355122ae7d209c882545dd3cb` (2026-11-09), `ADLC.md` « Version 0.3 » |
| **Consulté le** | 2026-11-11 | 2026-11-11 |
| **Licence** | « © 2026 Chris Williams » en pied de page, aucune licence de réutilisation | **MIT**, « Copyright (c) 2026 Chris Williams », lu dans `LICENSE` au commit étudié |
| **Niveau de reprise possible** | **Idée uniquement** | Idée, adaptation ou import (licence compatible) |
| **Niveau actuel** | Idée | Idée |

Le contenu de `ADLC.md` recoupe largement celui des articles. Pour une éventuelle adaptation, partir **du dépôt** (MIT), jamais du texte des articles.

---

## Synthèse

### Thèse

Le SDLC classique est une accumulation, sur soixante ans, de défenses contre les défaillances **humaines** : oubli, ego, fatigue, peur du blâme, coût de communication. Les modèles ont un autre profil de défaillance. Copier les rituels humains fait donc deux erreurs à la fois : on importe des défenses inutiles (un agent n'a pas besoin de *stand-up*, il se coordonne par les artefacts) et on rate les défaillances propres aux modèles (inventer une API avec aplomb, supprimer un test qui échoue puis annoncer « tout est vert »).

D'où la **règle de conception** : chaque phase, porte ou boucle doit se rattacher à une défaillance précise du modèle qu'elle contre, ou à une propriété du modèle qu'elle exploite. Si elle se rattache à une défaillance humaine, on la retire.

### L'inventaire des défaillances

Le texte part d'un inventaire explicite, qui sert d'entrée à toute la conception. Parmi les principales :

| Défaillance du modèle | Réponse de conception |
|---|---|
| **Satisfaction prématurée** : fait le minimum défendable puis déclare victoire | Rendre la satisfaction vérifiable par une machine : critères d'acceptation exécutables |
| **Complaisance** envers celui qui demande | Ne jamais faire valider un travail par le contexte qui l'a produit ; inverser la mission (« réfuter ») |
| **Dégradation du contexte** (*context rot*) | Tâches atomiques, contexte neuf par tâche, transmission de conclusions plutôt que de transcriptions |
| **Hallucination assurée** | Pas de preuve, pas de fait : toute affirmation passe par une exécution déterministe |
| **Contournement de la récompense** (*reward hacking*) : supprimer un test, affaiblir une assertion | Tests écrits dans un autre contexte et gelés pendant l'implémentation |
| **Plafond de constats** : une revue s'arrête vers 10 à 20 constats | Relancer la revue jusqu'à des passes « sèches » |
| **Verbosité, duplication** | Phase de simplification après fusion |

Et des propriétés à exploiter : la diversité d'échantillonnage (un ensemble gratuit), la conformité zélée à une mission (un critique chargé de réfuter est impitoyable), l'absence d'ego (on peut jeter et recommencer), la fraîcheur d'un contexte neuf, le faible coût de l'exploration.

### Principes (reformulés)

1. **Une porte déterministe entre chaque phase** : compilateur, tests, validateur de schéma ou humain. Une chaîne de LLM n'est pas plus solide que ses maillons non LLM.
2. **Pas de preuve, pas de fait**, y compris pour les critiques : un constat de revue est une affirmation tant qu'il n'est pas reproduit.
3. **Ne jamais faire juger un travail par le contexte qui l'a produit.**
4. **Rendre la satisfaction vérifiable par une machine** : le minimum que l'agent fera doit *être* la cible.
5. **Protéger les rails du constructeur** : tests, contrats et types écrits avant, ailleurs, et en lecture seule pendant le build.
6. **Un seul écrivain par partition ; paralléliser seulement la recherche** (revue, alternatives), pas la construction.
7. **Le niveau de modèle dépend du coût de *détection* d'une erreur**, pas du prestige de la tâche : le meilleur modèle pour la spec et les contrats, un modèle moyen pour le code, très vérifié par ailleurs.
8. **La divergence d'une boucle est une information** : budget d'itérations, sortie sur passes sèches, escalade humaine. Une boucle qui ne converge pas signale une spec contradictoire ou un mauvais découpage.
9. **Dépense en haltère** : beaucoup en spec et en vérification, peu au milieu.
10. **Tout artefact lu par l'agent suivant est un cache, et un cache s'invalide** : skills, specs, mémoire, `CLAUDE.md` vieillissent puis désinforment avec autorité.

### Huit phases, deux portes humaines

| Phase | Rôle | Porte de sortie |
|---|---|---|
| **P0 Triage** | Router selon **risque × rayon d'impact**, pas selon la taille : trivial, borné (on saute en P3), substantiel, architectural | Routage |
| **P1 Interrogate** | Interroger l'humain jusqu'à épuisement des questions, **en vérifiant le code avant chaque question**. Chaque critère d'acceptation nomme sa méthode de vérification. Alternatives de conception pour l'architectural | **Humain : approbation de la spec** |
| **P2 Decompose** | Découper en tickets exécutables par un agent neuf à partir du seul ticket ; contrats explicites aux frontières | *Cold-start test* : un modèle bon marché et neuf liste ce qui manque au ticket |
| **P3 Rail** | Écrire tests, types et contrats depuis la spec, dans un contexte qui ne verra jamais l'implémentation ; puis les **geler** | Suite rouge « pour les bonnes raisons » ; audit des rails |
| **P4 Build** | Un agent neuf par ticket ; règle des **deux échecs** : on ne coache pas un agent qui tourne en rond, on améliore le ticket et on recommence | Rails verts, build, lint, rails intacts |
| **P5 Prosecute** | Pas une « revue » mais une **accusation** : plusieurs critiques en parallèle, un angle chacun, mission de réfuter ; chaque constat passe par un **vérificateur** qui le reproduit ou l'écarte ; on boucle jusqu'à **deux passes sèches** | Zéro constat vérifié ouvert |
| **P6 Integrate** | L'humain lit le résumé de conformité à la spec et le diff des tests, **lance le logiciel**, inspecte 2 ou 3 points chauds, plutôt que de relire tout le diff | **Humain : acceptation du comportement** |
| **P7 Distill** | Simplifier sous rails verts ; transformer les constats récurrents en lints ou skills, les questions récurrentes en gabarits de spec ; rafraîchir les skills périmés | Alimente P1 à P4 du cycle suivant |

Les deux portes humaines posent la même question sous deux formes : « est-ce ce que je voulais ? » sur la spec, puis « est-ce ce que je voulais, *en fonctionnement* ? ». L'article 2 le résume ainsi : l'intention est vérifiée *« exactly twice: once as words, once as behavior »*. Ailleurs, l'humain n'intervient que sur escalade.

### Autres mécanismes notables

- **Normes rejetées**, avec leurs raisons : relecture humaine de diffs complets (du « théâtre » au-delà de quelques centaines de lignes), planification légère façon agile, **personas** (« un costume ajoute des tokens, pas des capacités »), construction collaborative à plusieurs agents, DRY au moment d'écrire, couverture de code comme porte de qualité.
- **Annexe Goodhart** : chaque porte vient avec sa défense contre le contournement. Exemples : rails gelés et diff des rails vide ; grep des marqueurs `skip` et des suppressions de lint ; question d'audit « un test échoue-t-il si on supprime la fonctionnalité ? » ; diff limité aux fichiers déclarés par le ticket.
- **Économie** : l'unité de compte est le **coût par changement fusionné et vérifié**. Une dépense concentrée dans le build trahit des skills manquants ou des tickets trop gros ; une revue qui coûte de plus en plus cher trahit une capitalisation qui ne fonctionne pas.
- **Adoption progressive** : une équipe n'adopte pas un cycle idéal, elle adopte un soulagement. Ordre proposé : d'abord l'accusation des PR existantes, puis les rails, puis l'interrogation, enfin le cycle complet.
- **Doctrine « sans modèle de pointe »** : obtenir la qualité visée avec des modèles moyens en remplaçant le jugement en une passe par de la recherche (N tentatives), de la vérification, de la décomposition, de la capitalisation et de la mesure. La revue est calibrée par des **bugs plantés** : *« Measure the stack, never the model »*.
- **Boîte à outils** : une trentaine de petites CLI Node, une par porte (`spec-lint`, `coldstart`, `hollow-test`, `rails-guard`, `prosecute`, `lesson-foundry`, `skill-rot`…), avec une preuve de passage enregistrée dans `.adlc/`.

---

## Ce que JDLC peut en retenir

| # | Idée | Ce que ça donnerait dans JDLC | Convergence |
|---|---|---|---|
| 1 | **Chaque composant se justifie par une défaillance du modèle qu'il contre** | Tenir un inventaire des défaillances comme entrée de conception, et exiger de chaque skill, hook ou phase JDLC qu'il dise laquelle il traite. Ce qui ne se rattache à rien est retiré. *Confirme et durcit* une idée existante. | Anthropic A3/A5 (chaque composant encode une hypothèse), principe 1 (empirique) |
| 2 | **Deux portes humaines : l'intention en mots, puis en comportement** | Réponse argumentée à la question « 2 ou 3 portes ? ». À la seconde porte, l'humain lit un résumé de conformité et le diff des tests, et **lance le logiciel**, au lieu de relire tout le diff. *Nouveau* pour le contenu de la porte d'acceptation. | SFEIR (3 portes), AIDD (3 pauses), Anthropic A4 (fatigue d'approbation), principe 6 |
| 3 | **Rails écrits avant, ailleurs, puis gelés mécaniquement** | Les tests d'acceptation sont écrits par un sous-agent qui ne voit pas l'implémentation ; un hook Claude Code interdit leur modification pendant l'implémentation. *Nouveau*, et très concret. | Principe 8 (preuve), Anthropic A3 (contrat de sprint), AWS AI-DLC (commande de vérification fixée par l'humain) |
| 4 | **Les constats de revue sont des affirmations : vérifier, puis boucler jusqu'à deux passes sèches** | Après la revue, un vérificateur reproduit ou écarte chaque constat avant qu'il atteigne l'implémentation ; budget d'itérations et escalade si ça ne converge pas. *Nouveau* : prolonge la revue indépendante. | AIDD (*checker* neuf), SFEIR (revues parallèles), Anthropic A3 (évaluateur sceptique) |
| 5 | **Chaque critère d'acceptation nomme sa méthode de vérification** | Règle de la spec JDLC : un critère sans test, commande ou démonstration associée est un souhait, pas un critère. *Nouveau* comme règle explicite. | Principe 8, AIDD (demande « prête à planifier »), AWS AI-DLC (identifiants de critères) |
| 6 | **Interroger, mais vérifier le code avant chaque question** | Consigne pour le skill de cadrage : ne poser que les questions auxquelles le dépôt ne répond pas. *Nouveau*. | AWS AI-DLC (questions plutôt que suppositions), SFEIR (explorations amont) |
| 7 | **Règle des deux échecs : on ne coache pas un agent qui tourne en rond** | Après deux échecs, on arrête, on corrige la tâche ou le contexte, et on repart d'un contexte neuf. Si ça échoue encore, c'est le découpage qui est faux. *Rend opératoires* les principes 11 et l'hygiène de contexte. | Manifeste AIDD (reconstruire le contexte), principe 11 |
| 8 | **Test « à froid » des tâches** | Avant l'implémentation, un sous-agent neuf et bon marché lit la tâche seule et liste ce qui lui manque. *Nouveau*, peu coûteux. | Anthropic A2 (sous-agents à contexte propre) |
| 9 | **Triage par risque × rayon d'impact** | Critère pour choisir le profil ou la lourdeur du cycle : pas la taille du changement, mais ce qu'il peut casser. *Confirmation*, avec un meilleur critère. | AIDD (plan proportionné), AWS AI-DLC (profils), principe 7 |
| 10 | **Une leçon récurrente devient de préférence une vérification déterministe** | Dans la capitalisation, préférer un lint, un test ou un hook à une règle en prose quand c'est possible. *Précise* la capitalisation. | SFEIR (un bug vu deux fois…), AIDD (`learn`), Anthropic A7 |
| 11 | **Les artefacts de contexte sont des caches à invalider** | Dater les skills et la mémoire ; revue périodique de fraîcheur. *Confirmation*. | Anthropic A7 (consolidation, « rêve »), principe 10 |
| 12 | **Chaque porte a sa défense anti-contournement** | Pour chaque contrôle JDLC, écrire comment un agent pourrait le satisfaire sans faire le travail, et la parade. Pas de pourcentage de couverture comme porte. *Nouveau*. | Principe 8 |
| 13 | **Mesurer le dispositif, pas le modèle** | Évaluer la revue JDLC sur des bugs plantés pour savoir ce qu'elle attrape réellement. *Nouveau* ; donne corps à l'idée d'une suite d'évaluations de JDLC. | Anthropic A6 (évaluations, ablations), manifeste AIDD (*Outcome over Output*) |
| 14 | **Niveau de modèle selon le coût de détection d'une erreur** | Choisir le modèle de chaque sous-agent JDLC : le plus fort pour la spec et le découpage, un modèle moyen pour l'implémentation encadrée par les rails. *Nouveau*. | — |
| 15 | **Adopter par soulagement, pas par cycle complet** | Pour le premier composant JDLC : commencer par ce qui soulage le plus. Ordre ADLC : revue → rails → interrogation. À confronter à l'ordre SFEIR (porte de spec + preuve). | Principe 7, piste SFEIR « par où commencer » |

## Ce qu'on écarte ou relativise

- **Le quorum multi-familles de modèles** (au moins deux fournisseurs différents pour les surfaces sensibles) : impossible dans le cadre Claude Code uniquement ([ADR-0006](../adr/0006-claude-code-uniquement.md)). On retient l'argument (un relecteur de la même famille partage les angles morts) comme limite connue, pas comme mécanisme.
- **Le parallélisme à grande échelle** (flotte de *worktrees*, file de fusion, gestion des quotas d'abonnement, routage entre canaux de facturation) : disproportionné pour un usage solo.
- **La boîte à outils Node** (une trentaine de paquets, manifeste de portes signé) : JDLC reste du Markdown et des fonctions natives de Claude Code. On retient *ce que vérifie* chaque porte, pas l'outillage. À noter : le dépôt indique que Windows n'est pas encore pris en charge.
- **Le rejet catégorique de la relecture humaine du diff** : pour un usage solo et pédagogique, lire le code reste parfois le but. On retient l'idée de *diriger* l'attention (spec, tests, comportement, points chauds), pas l'interdiction.
- **La doctrine « sans modèle de pointe »** : pertinente en entreprise sous contrainte de modèles autorisés ; secondaire pour JDLC. On garde la logique « vérifier plutôt que faire confiance ».
- **Le ton et les affirmations chiffrées** (taux de rappel, réductions de coût) : illustratifs, issus du contexte de l'auteur, non vérifiés.

## Sources secondaires

- Parties 3 à 10 de la série (rails, accusation, parallélisme, économie, boîte à outils, comparaison avec un SDLC d'entreprise, accusation des portes, choix du modèle) : repérées, non lues. Leur contenu semble repris dans `ADLC.md` et ses annexes, qui ont servi de base à cette note.
- `docs/models-by-phase.md` et `docs/interrogation-protocol.md` du dépôt (MIT) : à lire au moment de concevoir le cadrage et le choix des modèles.
- Le site https://www.agenticlifecycle.ai/ affiche « MIT » en pied de page ; le dépôt déclare https://adlc.fyi comme page d'accueil. La licence retenue pour toute reprise reste celle lue dans `LICENSE` au commit étudié.
