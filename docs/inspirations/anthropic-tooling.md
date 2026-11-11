# Outillage Anthropic autour de Claude Code : CI, environnement, mesure

Six dépôts publics d'Anthropic qui entourent Claude Code : exécution en CI, mesure de l'usage, environnements reproductibles, bac à sable (*sandbox*). Ils sont étudiés ensemble parce qu'ils répondent aux mêmes questions pour JDLC : comment faire tourner les phases Revue et Livraison en CI, comment rendre l'autonomie sûre, et comment mesurer l'effet de JDLC.

| | [claude-code-action](https://github.com/anthropics/claude-code-action) | [claude-code-monitoring-guide](https://github.com/anthropics/claude-code-monitoring-guide) | [devcontainer-features](https://github.com/anthropics/devcontainer-features) |
|---|---|---|---|
| **Auteur** | Anthropic PBC | Anthropic (guide signé Kashyap Coimbatore Murali) | Anthropic PBC |
| **Version étudiée** | Commit `d5f7970` (2026-11-10) | Commit `0277744` (2025-07-28) | Commit `4c6fe6a` (2025-06-24) |
| **Consulté le** | 2026-11-11 | 2026-11-11 | 2026-11-11 |
| **Licence vérifiée** | **MIT**, « Copyright (c) 2025 Anthropic, PBC » (racine et `base-action/`) | **Aucune licence** : pas de fichier `LICENSE` au commit étudié | **MIT**, « Copyright (c) 2025 Anthropic PBC » |
| **Reprise possible** | Idée, adaptation, import | Idée uniquement | Idée, adaptation, import |
| **Niveau actuel** | Idée | Idée | Idée |

| | [sandbox-runtime](https://github.com/anthropics/sandbox-runtime) | [claude-code-playground](https://github.com/anthropics/claude-code-playground) | [axt-verify](https://github.com/anthropics/axt-verify) |
|---|---|---|---|
| **Auteur** | Anthropic PBC | Anthropic PBC (équipe DevRel) | Anthropic PBC |
| **Version étudiée** | Commit `f7a0f19` (2026-11-09) | Commit `569c528` (2026-10-01) | Commit `da91570` (2026-10-23) |
| **Consulté le** | 2026-11-11 | 2026-11-11 | 2026-11-11 |
| **Licence vérifiée** | **Apache-2.0**, « Copyright 2025 Anthropic ». Pas de `NOTICE` à la racine (seulement dans un jeu de test tiers). | **Apache-2.0** ; chaque fichier de code porte `Copyright 2026 Anthropic PBC` et un en-tête SPDX. Pas de `NOTICE`. | **Apache-2.0** + `NOTICE` (« Copyright 2026 Anthropic PBC ») + licences tierces des modules Go |
| **Reprise possible** | Idée, adaptation, import | Idée, adaptation, import | Sans objet |
| **Niveau actuel** | Idée | Idée | — |

`claude-code-base-action` n'a pas été étudié séparément : c'est un miroir automatique du dossier `base-action/` de claude-code-action.

## De quoi il s'agit

**claude-code-action.** Une GitHub Action qui lance Claude Code sur un dépôt GitHub. Elle a deux modes, choisis automatiquement. Le mode **interactif** répond à une mention `@claude` dans une issue ou une PR. Le mode **automatisation** s'active dès qu'on fournit un `prompt` : revue de PR, tri d'issues, tâches planifiées. Le dépôt fournit aussi des exemples de workflows (revue, correction de CI rouge, tri), une documentation de sécurité détaillée et une action séparée, `agent-approval-check`, qui exige des approbations humaines sur les PR écrites par un agent. Le dépôt utilise lui-même Claude Code : son dossier `.claude/` contient une commande de revue, cinq sous-agents relecteurs et un workflow de tri des PR. C'est la partie la plus riche pour JDLC.

**claude-code-monitoring-guide.** Un guide pour mesurer le coût et l'usage de Claude Code avec sa télémétrie OpenTelemetry (un standard ouvert d'export de métriques), Prometheus et Grafana. Il ajoute un prompt pour générer un rapport de productivité. Le guide date de juillet 2025 et prévient que ses exemples viennent de données limitées ou hypothétiques. Il est orienté « retour sur investissement » pour une organisation.

**devcontainer-features.** Une *feature* de dev container (un module d'installation pour les environnements de développement conteneurisés) qui installe la CLI Claude Code via npm. Un seul script. Dernier commit en juin 2025.

**sandbox-runtime (`srt`).** L'outil d'isolation utilisé par Claude Code. Il enferme un processus et tous ses enfants avec les mécanismes du système (`sandbox-exec` sur macOS, `bubblewrap` sur Linux, un compte utilisateur dédié sur Windows, en alpha). Il filtre le système de fichiers (lecture et écriture) et le réseau (liste de domaines autorisés via un proxy). Le README documente aussi précisément ses limites.

**claude-code-playground.** Un dépôt de l'équipe DevRel d'Anthropic : configurations réelles, skills, démos. Au commit étudié, presque tous les dossiers sont des annonces (« coming soon »). Seuls existent trois *mods* (des plugins qui modifient l'interface de Claude Code), un gabarit d'entrée et une liste de contrôle avant publication. Le dépôt prévient : « ce n'est pas un canon de bonnes pratiques ».

**axt-verify.** Un outil en Go qui vérifie le journal de transparence d'Anthropic (*Access Transparency* : la trace des accès du personnel d'Anthropic aux données d'une organisation cliente). Rien à voir avec un SDLC.

## Ce que JDLC peut en retenir

### Revue en CI (claude-code-action)

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 1 | **Les mêmes plugins en local et en CI.** L'action accepte `plugins` et `plugin_marketplaces` : elle installe des plugins depuis une marketplace Git avant de lancer Claude. | La phase Revue de JDLC tourne en CI avec **le plugin JDLC lui-même**, installé depuis la marketplace du dépôt JDLC. Les mêmes skills servent en local et en CI. | **Nouvelle**. Prolonge l'idée 20 de [claude-code-docs.md](claude-code-docs.md) (runs scriptés) et l'ADR-0005 |
| 2 | **Une revue par spécialistes, filtrée deux fois.** La commande `/review-pr` du dépôt lance cinq sous-agents (qualité, performance, tests, documentation exacte, sécurité). Chacun ne rend que ce qui est « notable ». Puis l'agent principal ne publie que ce qu'il juge lui aussi notable. | Gabarit de revue JDLC : des relecteurs ciblés, puis un tri final. Le double filtre limite le bruit. | Confirmation : [superpowers.md](superpowers.md) (revue indépendante), idée 13 de [claude-code-docs.md](claude-code-docs.md) (cadrer la revue). **Nouvelle** pour le double filtre |
| 3 | **Un vérificateur adverse, seulement pour les verdicts positifs.** Le workflow `pr-stamp-sweep` fait relire chaque PR par un agent. Si celui-ci propose d'approuver, un second agent reçoit pour mission de **réfuter** cette approbation. Une PR n'est retenue que si les deux sont d'accord. Les verdicts négatifs ne sont pas revérifiés. | Pour la phase Revue : un agent sceptique contre-vérifie toute conclusion « c'est bon ». Le coût de la double vérification ne porte que sur ce qui va passer la porte. | Confirmation : [anthropic.md](anthropic.md) A3 (évaluateur réglé pour le scepticisme), idée 11 (évaluateur proportionné). **Nouvelle** pour l'asymétrie |
| 4 | **Des verdicts structurés.** Le même workflow impose un schéma JSON : verdict dans une liste fermée (`stamp`, `skip`, `needs-discussion`), changement de comportement visible, sensibilité sécurité, doublon, niveau de confiance. L'action expose aussi `--json-schema` : la sortie validée devient une variable utilisable par les étapes suivantes. | Le relecteur JDLC en CI rend un verdict structuré. Le workflow décide sur des champs, pas sur du texte libre. | Confirmation : idée 18 de [single-agent-loop-kit.md](single-agent-loop-kit.md) (réponse structurée validée par du code) |
| 5 | **Des critères explicites pour une fusion légère.** Une PR peut être approuvée sans discussion si elle est petite, sans changement de comportement majeur, vérifiée contre **le code actuel** (pas seulement le diff), sans risque de sécurité. Pour une PR de documentation, on vérifie que ce qu'elle affirme est vrai dans le code. | Une grille JDLC « changement mineur » : ce qui peut passer avec une validation humaine allégée, et ce qui exige la revue complète. | **Nouvelle**. Rejoint le zonage feuilles/tronc ([anthropic-talks-erik.md](anthropic-talks-erik.md), idée 1) |
| 6 | **La configuration qui juge vient de la branche cible.** Sur une PR, l'action restaure `.claude/`, `CLAUDE.md`, `.mcp.json` et les hooks git depuis la branche de base. La version de la PR est mise de côté. Une PR ne peut donc pas réécrire les règles qui la jugent. | Règle JDLC : la revue tourne avec la configuration JDLC de la branche cible. Une modification de JDLC dans un projet ne s'applique qu'après sa fusion. | **Nouvelle** |
| 7 | **Des hooks autonomes.** Corollaire : le `package.json` vient, lui, de la PR. Un hook qui appelle `npm run …` exécute donc du code choisi par la PR. Le dépôt recommande d'appeler l'outil directement, avec une version figée (`bunx prettier@3.5.3 --no-config`). | Les hooks fournis par JDLC n'appellent jamais un script du projet cible. Ils appellent un outil à version figée. | **Nouvelle**. Rejoint l'idée 24 de [single-agent-loop-kit.md](single-agent-loop-kit.md) (scripts sans dépendance) |

### Livraison et portes humaines

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 8 | **L'agent ne fusionne pas et n'approuve pas.** Par défaut, Claude pousse une branche et donne un lien vers la création de PR. C'est l'humain qui ouvre la PR. Claude ne peut ni approuver une PR, ni fusionner. | Confirme la porte humaine « merge » de la vision. | Confirmation : principe 6 ; menu de fin de branche de [superpowers.md](superpowers.md) |
| 9 | **Une approbation liée à un commit précis.** `agent-approval-check` exige N approbations humaines sur toute PR qui contient un commit d'agent. Une approbation par commentaire cite le SHA du commit. Dès qu'un nouveau commit arrive, l'ancienne approbation est périmée. L'outil échoue en mode fermé : en cas d'erreur, la PR reste bloquée. | En solo, JDLC peut garder l'idée centrale : **une validation humaine porte sur une version précise**. Si l'agent modifie le code après le « oui », il faut un nouveau « oui ». | **Nouvelle** pour le lien au SHA. Prolonge l'idée 5 de [single-agent-loop-kit.md](single-agent-loop-kit.md) (un oui vaut pour un seul arrêt) |
| 10 | **La CI vérifie, l'humain publie.** Le playground publie à la main : un mainteneur relit le diff complet et pousse lui-même. « La CI ne fait que vérifier ; elle ne publie jamais. » | Règle par défaut de la phase Livraison : aucun déploiement ni publication déclenché par un agent sans action humaine. | Confirmation : principe 6. **Nouvelle** formulation, courte et réutilisable |
| 11 | **Corriger une CI rouge sur une branche à part.** L'exemple `ci-failure-auto-fix` se déclenche quand la CI échoue. Il récupère les journaux d'erreur, puis Claude corrige sur une **nouvelle branche** préfixée. Le préfixe empêche le workflow de se relancer sur ses propres corrections. Le fichier prévient : à réserver aux dépôts où tous les contributeurs sont de confiance. | Piste pour plus tard : une correction de CI proposée, jamais poussée sur la branche d'origine. | **Nouvelle**. À relativiser (voir plus bas) |

### Autonomie sûre : des garanties, pas des consignes

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 12 | **Une règle du CLAUDE.md, un script CI qui la fait respecter.** Le `CLAUDE.md` de claude-code-action impose trois protections aux workflows qui appellent Claude. Un script CI échoue si l'une manque. Les exceptions sont listées dans une table, chacune avec sa raison. | Forme type d'une règle JDLC importante : la consigne écrite, le contrôle automatique qui la vérifie, et une liste d'exceptions motivées. | Confirmation : idée 10 de [claude-code-docs.md](claude-code-docs.md) (consigne ou garantie). **Nouvelle** pour la table d'exceptions motivées |
| 13 | **Protections cumulées en CI.** Les jobs du dépôt combinent : outils autorisés au strict minimum, mode de permission `auto` (une revue de sécurité valide les appels non prévus), et un runner dont le trafic sortant est filtré par liste de domaines. Chaque domaine autorisé est commenté. | Configuration CI recommandée par JDLC : permissions minimales, mode auto, réseau restreint quand c'est possible. | Confirmation : [anthropic.md](anthropic.md) A4 (mode auto), idée 3 (permissions par paliers) |
| 14 | **Isoler le système de fichiers et le réseau, les deux.** `srt` explique pourquoi l'un sans l'autre ne suffit pas. Sans isolation des fichiers, un processus lit les clés SSH. Sans isolation réseau, il les envoie ailleurs. Écriture interdite par défaut, autorisée chemin par chemin. | Le mode autonome de JDLC s'appuie sur le *sandbox* natif de Claude Code (bâti sur `srt`), avec les deux isolations actives. | Confirmation : idée 10 de [claude-code-docs.md](claude-code-docs.md). **Nouvelle** pour la double isolation |
| 15 | **L'agent ne peut pas réécrire ses propres règles.** Certains chemins sont toujours interdits en écriture, même dans un dossier autorisé : `.claude/commands/`, `.claude/agents/`, `.git/hooks/`, `.mcp.json`, la configuration du shell et de git. | En mode autonome, les fichiers installés par JDLC (skills, agents, hooks, réglages) sont hors d'atteinte de l'agent. Les faire évoluer est un acte humain. Même logique que l'idée 6. | **Nouvelle** |
| 16 | **Montrer l'impact avant de demander.** Le mod *Blast Radius* bloque une commande risquée (`rm -rf`, `git reset --hard`, `git push --force`, migrations). Il affiche ce qu'elle va toucher : fichiers, commits perdus, migrations en attente. L'humain choisit ; « Annuler » est le choix par défaut. | Une porte humaine JDLC montre un aperçu concret de la conséquence, pas seulement la commande. Le refus est l'option par défaut. | **Nouvelle**. Rejoint la fatigue d'approbation ([anthropic.md](anthropic.md) A4) : une demande informée vaut mieux qu'une demande de plus |
| 17 | **Écrire ce que la garantie ne couvre pas.** Le README de `srt` liste ses failles : un domaine large comme `github.com` permet d'exfiltrer, un `srt` installé dans le `node_modules` du projet peut être réécrit, le mode imbriqué dans Docker est affaibli. | Chaque garde-fou JDLC documente ses limites connues. | Confirmation : idée 13 de [single-agent-loop-kit.md](single-agent-loop-kit.md) (dire ce qui a vraiment été vérifié) |
| 18 | **Le contenu d'une issue ou d'une PR est une donnée non fiable.** L'action nettoie les commentaires HTML et caractères invisibles, filtre les auteurs, et conseille de ne jamais extraire le code de la PR à la racine sur certains déclencheurs. | Règle JDLC pour tout skill qui lit une issue ou une PR externe : c'est une donnée, jamais une instruction. | Confirmation : [anthropic.md](anthropic.md) A4 (modèle de menace) |

### Mesurer l'effet de JDLC

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 19 | **La télémétrie native suffit pour commencer.** Avec `CLAUDE_CODE_ENABLE_TELEMETRY=1` et l'exportateur `console`, Claude Code émet ses métriques sans aucune infrastructure : coût, tokens (dont le cache), sessions, commits, PR, lignes, et décisions d'acceptation ou de refus des modifications. | Mesure **terrain** de JDLC sur mes propres projets, avant et après adoption : coût par PR, taux de refus des modifications proposées. Elle complète `claude plugin eval`, qui mesure en laboratoire ([claude-code-docs.md](claude-code-docs.md), idée 15). | **Nouvelle** |
| 20 | **Toutes les métriques ne se valent pas.** Le guide classe lui-même la fiabilité : nombre de PR (haute), nombre de commits (moyenne, dépend des habitudes), lignes de code (basse : un bon refactoring en retire). | Indicateurs de JDLC : jamais les lignes de code. Préférer des unités de travail terminé (PR fusionnée, tâche validée). | **Nouvelle** dans sa forme. Rejoint le principe 1 (empirique) |
| 21 | **Le taux de refus comme signal.** La métrique `code_edit_tool.decision` compte les modifications acceptées et refusées par l'humain. | Un taux de refus qui monte après un changement de skill est un signal de régression, à verser dans la revue de JDLC. | **Nouvelle**. Alimente l'idée 14 d'[anthropic.md](anthropic.md) (les retours d'usage sont un signal) |

### Pédagogie et publication (claude-code-playground)

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 22 | **Chaque entrée dit comment elle a été construite** : le modèle utilisé, les prompts exacts, la transcription si possible, et surtout les itérations, « ce qui n'a pas marché ». Le gabarit appelle cette dernière partie « la plus utile ». | Pour la documentation pédagogique de JDLC : chaque composant garde une trace de sa conception et de ses échecs. Le journal de travail le fait déjà en partie. | Confirmation : principe 4 (pédagogique), idée 13 de [single-agent-loop-kit.md](single-agent-loop-kit.md) |
| 23 | **Une liste de contrôle avant publication** : dépendances listées avec leur licence (permissives seulement), en-tête copyright et SPDX dans chaque fichier de code, aucun secret, pas de marque déposée dans le nom d'une entrée. | Base d'une liste de contrôle avant release de JDLC, à fusionner avec la section 4 de [licences.md](../licences.md). Le point « pas de marque dans le nom » est à retenir pour nommer les skills JDLC. | Confirmation : [licences.md](../licences.md). **Nouvelle** pour la règle des marques |
| 24 | **Un hook qui repère les règles de CLAUDE.md à transformer en hooks.** Annoncé, pas encore publié. | C'est exactement l'idée « consigne ou garantie » de JDLC, outillée. À surveiller avant de construire la nôtre. | Confirmation : idée 10 de [claude-code-docs.md](claude-code-docs.md) |

### Un CLAUDE.md de référence

| # | Idée | Ce que ça donnerait dans JDLC | Nouvelle ou confirmation |
|---|---|---|---|
| 25 | **Le CLAUDE.md de claude-code-action** tient en une soixantaine de lignes : commandes, ce qu'est le projet, son fonctionnement en quelques phrases, une section « ce qui va vous mordre » (les pièges), les conventions. | Exemple concret de `CLAUDE.md` court pour le gabarit que JDLC installera. La section des pièges est le bon endroit pour ce que Claude ne peut pas deviner. | Confirmation : idée 5 de [claude-code-docs.md](claude-code-docs.md) |

## Ce qu'on écarte ou relativise

- **L'intégration GitHub complète** (application GitHub, mentions `@claude`, commentaires de suivi) suppose un dépôt GitHub et une clé API en CI. JDLC ne doit pas en dépendre. La revue en CI reste une **option** de la phase Revue.
- **La correction automatique de CI** (idée 11) exécute le code de la branche avec des droits d'écriture. C'est acceptable sur un dépôt solo, risqué ailleurs. Hors périmètre de la première version.
- **Les prompts des sous-agents relecteurs** de claude-code-action sont longs et génériques (« relecteur de sécurité d'élite », listes OWASP). C'est le style que [single-agent-loop-kit.md](single-agent-loop-kit.md) (idée 21) recommande d'éviter. On garde l'architecture, pas le texte.
- **Le guide de mesure** vise le retour sur investissement d'une organisation (Prometheus, Grafana, Linear, déploiement par MDM). C'est trop lourd pour un développeur seul. Il date de juillet 2025 : noms de métriques et réglages sont à revérifier dans la documentation actuelle.
- **La feature devcontainer** est mince (un `npm install -g`) et n'a pas bougé depuis juin 2025 ; elle installe Node 18 par défaut. Un environnement reproductible reste une bonne idée pour un agent autonome, mais ce dépôt n'apporte rien de plus que l'idée. De plus, `srt` prévient que son mode imbriqué dans Docker est affaibli : conteneur et *sandbox* ne s'additionnent pas toujours.
- **Le *sandbox* sous Windows** est en alpha et demande une installation administrateur. À vérifier avant d'en faire une condition du mode autonome de JDLC sur ce poste.
- **Les mods** du playground reposent sur une API de Claude Code récente. *Token Weather* (jauge du contexte) et *Replay Theater* (rejouer les modifications du dernier tour) sont des confort d'interface, sans effet sur le processus.

## Dépôts sans intérêt pour JDLC

- **axt-verify** : vérification cryptographique du journal d'accès d'Anthropic aux données d'une organisation. Sujet de conformité entreprise, sans lien avec le cycle de développement.

## Licence et réutilisation

- **claude-code-action (MIT)** : import possible, par exemple `agent-approval-check`, la commande `/review-pr`, le schéma de verdict de `pr-stamp-sweep`. Il faut garder la mention « Copyright (c) 2025 Anthropic, PBC », copier le texte MIT dans `licenses/` et poser l'en-tête de provenance de [licences.md](../licences.md). En pratique, JDLC **référencera** l'action dans un workflow plutôt que de la copier.
- **claude-code-monitoring-guide (aucune licence)** : idée uniquement. Les noms de métriques sont des faits techniques de Claude Code, à reprendre depuis la documentation officielle, pas depuis le guide.
- **devcontainer-features (MIT)** : s'utilise par référence (`ghcr.io/anthropics/devcontainer-features/claude-code`), sans rien copier.
- **sandbox-runtime (Apache-2.0)** : s'utilise comme dépendance ou via le *sandbox* intégré à Claude Code. Un import (peu probable) devrait garder la licence et signaler les modifications. Pas de `NOTICE` racine au commit étudié.
- **claude-code-playground (Apache-2.0)** : le gabarit d'entrée et la liste de contrôle pourraient être adaptés. Garder l'en-tête `Copyright 2026 Anthropic PBC` / `SPDX-License-Identifier: Apache-2.0` sur ce qui serait importé, copier la licence, signaler les modifications.
- Dans tous les cas, revérifier la licence au commit exact au moment d'un import.
