# Documentation officielle de Claude Code

JDLC cible uniquement Claude Code ([ADR-0006](../adr/0006-claude-code-uniquement.md)). Sa documentation officielle est donc à la fois une source d'idées (bonnes pratiques) et la **référence technique** des briques sur lesquelles JDLC sera construit : skills, sous-agents, hooks, plugins, marketplaces, permissions, mémoire.

Cette note complète [anthropic.md](anthropic.md), qui couvre les articles de recherche d'Anthropic : on ne les redit pas ici.

| | |
|---|---|
| **Source** | Site canonique : https://code.claude.com/docs (index : https://code.claude.com/docs/llms.txt). Pages principales consultées : [Best practices](https://code.claude.com/docs/en/best-practices), [Extend Claude Code](https://code.claude.com/docs/en/features-overview), [Explore the .claude directory](https://code.claude.com/docs/en/claude-directory), [Memory](https://code.claude.com/docs/en/memory), [Skills](https://code.claude.com/docs/en/skills), [Subagents](https://code.claude.com/docs/en/sub-agents), [Hooks](https://code.claude.com/docs/en/hooks-guide), [Permission modes](https://code.claude.com/docs/en/permission-modes), [Output styles](https://code.claude.com/docs/en/output-styles), [Goal](https://code.claude.com/docs/en/goal), [Workflows](https://code.claude.com/docs/en/workflows), [Common workflows](https://code.claude.com/docs/en/common-workflows), et la section *Plugins* : [Overview](https://code.claude.com/docs/en/plugins/overview), [Install](https://code.claude.com/docs/en/plugins/install), [Components](https://code.claude.com/docs/en/plugins/components), [Publish](https://code.claude.com/docs/en/plugins/publish), [Create a marketplace](https://code.claude.com/docs/en/plugins/create-marketplace), [Host a marketplace](https://code.claude.com/docs/en/plugins/host-marketplace), [Manifest reference](https://code.claude.com/docs/en/plugins/manifest-reference), [Loading reference](https://code.claude.com/docs/en/plugins/loading), [Security](https://code.claude.com/docs/en/plugins/security), [Dependencies](https://code.claude.com/docs/en/plugins/dependencies), [Plugin evals](https://code.claude.com/docs/en/plugin-evals) |
| **Auteur** | Anthropic |
| **Version** | Consultée le 2026-11-11. Pas de numéro de version de la documentation ; le [changelog](https://code.claude.com/docs/en/changelog) indique Claude Code **2.1.322** (2026-11-10) comme dernière version publiée. |
| **Licence** | Aucune licence de réutilisation. La page [Legal and compliance](https://code.claude.com/docs/en/legal-and-compliance) porte la mention *« © Anthropic PBC. All rights reserved. Use is subject to applicable Anthropic Terms of Service. »* Les autres pages n'ont pas de mention propre. |
| **Niveau de reprise possible** | **Idée uniquement** : synthèses reformulées, citations courtes et attribuées ([politique](../licences.md)). Les formats techniques (noms de champs, structure de fichiers) sont des interfaces de l'outil : on s'y conforme, on ne copie pas les exemples. |
| **Niveau actuel** | Idée |

> La documentation évolue très vite (plusieurs versions par semaine). Tout ce qui suit est daté du 2026-11-11 et doit être revérifié au moment de construire un composant.

---

## Synthèse

### Les bonnes pratiques (page *Best practices*)

La page a été réécrite autour d'un constat : avec les modèles actuels, un simple prompt dans une session neuve fait déjà le bon travail. La configuration ne sert que lorsque le projet a des connaissances, des règles ou des contraintes que Claude **ne peut pas voir** dans le code. Les échecs courants se rangent en quatre familles, chacune avec sa réponse.

1. **Dire à Claude ce qu'il ne peut pas voir.**
   - N'écrire que ce que Claude ne trouverait pas en lisant le dépôt, ou qu'il redécouvrirait à chaque session. Test : *pourrait-il le trouver en lisant le code ? irait-il vraiment chercher ?* Si oui aux deux, on ne l'écrit pas.
   - Un prompt précis (fichier, symptôme, ce que « réparé » veut dire) évite des allers-retours.
   - Quand le besoin est flou, demander à Claude de **m'interviewer** jusqu'à pouvoir écrire une spec (`SPEC.md`), puis repartir d'une session neuve pour construire à partir de cette spec.
   - `CLAUDE.md` **court** (moins de 200 lignes) : commandes de vérification, pièges, décisions absentes du dépôt, et ce qu'on a dû corriger deux fois. Les règles locales à une partie du code vont dans `.claude/rules/` avec un filtre `paths:`, les procédures dans des skills.
2. **Donner à Claude une vérification à exécuter.**
   - Sans rien qui réponde « réussi / échoué », Claude s'arrête quand le travail *a l'air* fini, et l'humain devient la suite de tests.
   - Quatre niveaux d'exigence, du plus souple au plus strict : la consigne dans le prompt ; un `/goal` vérifié par un autre modèle après chaque tour ; un **hook `Stop`** qui renvoie Claude au travail tant qu'une commande échoue ; une **revue par quelqu'un qui n'a pas écrit le code** (`/code-review` dans un sous-agent neuf).
   - Toujours demander **la preuve** : la commande lancée et sa sortie. « Tous les tests passent » seul ne dit presque rien.
   - Un relecteur à qui on demande des problèmes en trouvera toujours : cadrer la revue (« par rapport à la spec, ignorer le style ») pour éviter la sur-ingénierie.
3. **Imposer les règles qui ne souffrent aucune exception.**
   - Distinction centrale : les instructions **non déterministes** (prompt, `CLAUDE.md`, skills : le modèle décide) et **déterministes** (hooks, règles `deny`, réglages gérés : le *harness* applique toujours).
   - Si on ajoute de l'emphase (*IMPORTANT*, majuscules) à une ligne que Claude rate encore, c'est qu'elle doit devenir un hook. Inversement, un hook qui ne porte qu'une préférence doit être supprimé.
   - Garder l'ensemble imposé **petit** : chaque hook ou règle `deny` se déclenche parfois à tort et doit être maintenu.
   - Où vit une règle décide à qui elle s'applique : `settings.local.json` (moi), `.claude/settings.json` (le dépôt, après acceptation de la confiance), réglages gérés (l'organisation).
4. **Changer un défaut quand la tâche coûte trop** (temps, tokens, demandes de permission).
   - Le **mode auto** est désormais le défaut des sessions interactives : un classifieur filtre les actions risquées. Il ne remplace ni les règles `deny` ni les hooks, qui passent avant lui.
   - L'**effort** (`/effort`) remplace les formules du type « réfléchis bien ».
   - Les sous-agents coûtent des tokens et ne donnent pas une meilleure réponse : ils donnent du parallélisme ou **un regard neuf**. Un relecteur en lecture seule (`tools: Read, Grep, Glob`) est imposé par l'outil, pas demandé au modèle.

Compléments :

- **Piloter la session.** Surveiller la première minute ; `Esc` dès que Claude dérive ; après deux corrections ratées, `/clear` et un meilleur prompt plutôt qu'une troisième correction. `/rewind` permet de tenter l'approche audacieuse et de revenir en arrière (les checkpoints complètent git, ne le remplacent pas).
- **Travail sans surveillance : cinq questions** avant de lancer un run. Sait-il à quoi ressemble « terminé » ? Peut-il vérifier son travail ? Que peut-il toucher ? Qu'est-ce qui l'arrête (tours, budget) ? Où va le résultat, et **qui signe** ?
- **Ce qu'on peut arrêter de faire** : gérer agressivement la fenêtre de contexte, mettre des majuscules, utiliser les sous-agents pour économiser le contexte, décrire l'architecture dans `CLAUDE.md`… Ce sont des contournements de limites que les modèles ont dépassées.
- **Plan d'abord.** Le cycle « explorer, planifier, coder, commiter » n'est plus présenté comme tel sur la page de bonnes pratiques, mais le **mode plan** reste l'outil natif : Claude explore et propose un plan sans rien modifier, puis l'humain l'approuve, le refuse ou l'édite (`Ctrl+G`).

### Les surfaces d'extension

Tableau de référence pour l'architecture future de JDLC. « Projet » signifie versionné dans le dépôt cible, « utilisateur » signifie `~/.claude/`.

| Surface | Ce que c'est | Où ça vit | Chargement / coût en contexte | Déterministe ? | Quand l'utiliser | Livrable par un plugin ? |
|---|---|---|---|---|---|---|
| **`CLAUDE.md`** | Instructions persistantes | `./CLAUDE.md` ou `./.claude/CLAUDE.md` (projet), `CLAUDE.local.md` (perso, gitignoré), `~/.claude/CLAUDE.md`, politique gérée | Chargé intégralement à chaque session ; imports `@chemin` (4 niveaux max) | Non | Faits valables à chaque session : commandes, pièges, décisions hors dépôt | **Non** (un `CLAUDE.md` à la racine d'un plugin est ignoré) |
| **Règles** | Instructions thématiques, éventuellement filtrées par chemins | `.claude/rules/*.md`, `~/.claude/rules/` | À chaque session, ou seulement quand Claude touche des fichiers correspondant à `paths:` | Non | Conventions propres à un langage ou un dossier | **Non** |
| **Mémoire automatique** | Notes que Claude écrit pour lui-même (types : `user`, `feedback`, `project`, `reference`) | `~/.claude/projects/<projet>/memory/` (`MEMORY.md` + un fichier par sujet) | 200 premières lignes ou 25 Ko de `MEMORY.md` | Non | Corrections et préférences apprises au fil de l'eau | Non (propre à chaque machine) |
| **Skills** (ex-commandes) | `SKILL.md` + fichiers annexes : connaissance de référence ou procédure invocable par `/nom` | `.claude/skills/<nom>/`, `~/.claude/skills/`, plugin `skills/` | Nom + description à chaque tour ; contenu complet seulement à l'usage | Non | Procédures, checklists, savoir utilisé « parfois » | Oui, préfixé : `/plugin:skill` |
| **Sous-agents** | Travailleur isolé avec son prompt, ses outils, son modèle ; renvoie un résumé | `.claude/agents/*.md`, `~/.claude/agents/`, plugin `agents/` | Contexte séparé | Restrictions d'outils : oui | Revue indépendante, recherche volumineuse, parallélisme, rôle à outils limités | Oui, mais `permissionMode`, `hooks`, `mcpServers` sont ignorés pour un agent de plugin |
| **Hooks** | Commande, requête HTTP, outil MCP, prompt ou agent déclenché à un événement du cycle (`PreToolUse`, `PostToolUse`, `Stop`, `SessionStart`, `PreCompact`, `SubagentStop`…) | `settings.json` (`hooks`), frontmatter d'un skill ou d'un agent, plugin `hooks/hooks.json` | Nul, sauf si le hook renvoie du texte | **Oui** pour `command` et `http` (les hooks `prompt`/`agent` font juger un modèle) | Ce qui doit arriver *toujours* ou *jamais* : garde-fous, preuve imposée, formatage | Oui |
| **Permissions** | Règles `allow` / `ask` / `deny` + modes (`default`/manuel, `acceptEdits`, `plan`, `auto`, `dontAsk`, `bypassPermissions`) + sandbox | `settings.json`, `settings.local.json`, réglages gérés | Nul | **Oui** (`deny` tient dans tous les modes) | Interdits absolus (secrets, dossiers protégés), cadre des runs autonomes | **Non** (rien dans la doc ne le permet ; le `settings.json` d'un plugin ne retient que `agent` et `subagentStatusLine`) |
| **Mode auto** | Classifieur qui juge les actions risquées | Réglage `autoMode` **uniquement dans les réglages utilisateur** (ignoré au niveau projet) | — | Non (jugement d'un modèle) | Réduire la fatigue d'approbation | Non |
| **Output styles** | Rôle, ton et format des réponses pour toute la session | `.claude/output-styles/`, plugin `output-styles/` | Chargé à chaque requête | Non | Un « mode » de réponse qu'on active ou désactive (ex. explicatif) | Oui |
| **MCP** | Connexion à des services externes (outils, données) | `.mcp.json` (projet), réglages utilisateur, plugin `.mcp.json` | Noms d'outils au démarrage, schémas à la demande | — | Système externe sans CLI | Oui (approbation par serveur) |
| **Workflows dynamiques** | Script JS écrit par Claude qui orchestre de nombreux sous-agents | `.claude/workflows/*.js`, plugin `workflows/` | Hors conversation | Le script décide de l'enchaînement | Audits ou migrations à grande échelle, constats recoupés | Oui |
| **`/goal`** | Condition de fin vérifiée par un modèle après chaque tour | Commande de session | — | Non | Travail long avec un état final vérifiable | — |
| **Plugins** | Paquet installable de skills, agents, hooks, MCP, LSP, output styles, workflows, `bin/`… | `.claude-plugin/plugin.json` + dossiers standard | Chaque skill/agent invocable par le modèle coûte sa description à chaque tour | — | Réutiliser une configuration sur plusieurs dépôts, la distribuer, la versionner | — |
| **Marketplaces** | Catalogue `.claude-plugin/marketplace.json` qui dit où chercher chaque plugin | Un dépôt git (ou URL, chemin local) | — | — | Distribuer et mettre à jour des plugins | — |

Superposition des niveaux : les `CLAUDE.md` **s'additionnent** ; skills et sous-agents **se remplacent par nom** (les skills de plugin sont préfixés, donc sans conflit) ; les hooks **fusionnent** (tous se déclenchent).

À noter aussi :

- **Les commandes ont fusionné dans les skills.** `.claude/commands/x.md` fonctionne encore, mais les skills sont recommandés (dossier pour fichiers annexes, contrôle d'invocation).
- **Contrôle d'invocation d'un skill** : `disable-model-invocation: true` (seul l'humain le déclenche, pour les actions à effet de bord comme une release) ; `user-invocable: false` (savoir de fond que seul Claude utilise). Autres champs utiles : `allowed-tools`, `context: fork` (exécution dans un sous-agent), `paths`, `hooks`, `model`, `effort`.
- **Les skills suivent le standard ouvert [Agent Skills](https://agentskills.io)**, étendu par Claude Code.
- **Mémoire des sous-agents** : champ `memory: project` → `.claude/agent-memory/<agent>/`, versionnable. Un relecteur peut ainsi accumuler les motifs rencontrés.
- **Skills fournis avec Claude Code** qui recoupent un SDLC : `/code-review` (relecture par un sous-agent neuf), `/verify` et `/run` (lancer l'application pour constater le changement), `/run-skill-generator` (enregistre la recette de lancement comme skill du projet), `/simplify`, `/batch` (découpe une grosse modification en unités, chacune dans son worktree), `/doctor prompt-audit` (audit des instructions périmées ou contradictoires). Un skill projet nommé `verify` ou `simplify` est lancé automatiquement avant chaque commit.

### Plugins et marketplaces : structure, installation, mises à jour, portée

**Structure d'un plugin.** Un dossier avec un manifeste optionnel `.claude-plugin/plugin.json` (seul `name` est obligatoire) et des emplacements par défaut : `skills/`, `agents/`, `hooks/hooks.json`, `.mcp.json`, `.lsp.json`, `output-styles/`, `workflows/`, `monitors/`, `bin/` (ajouté au `PATH`), `settings.json`.

- `settings.json` d'un plugin n'accepte que `agent` (faire d'un agent du plugin le fil principal) et `subagentStatusLine`. Tout le reste est ignoré : **un plugin ne peut livrer ni permissions, ni règles, ni `CLAUDE.md`**.
- Variables de chemin : `${CLAUDE_PLUGIN_ROOT}` (change à chaque mise à jour, ne rien y écrire), `${CLAUDE_PLUGIN_DATA}` (persistant entre mises à jour), `${CLAUDE_PROJECT_DIR}`.
- `userConfig` : valeurs demandées à l'utilisateur à l'installation (les secrets vont dans un stockage sécurisé).
- `dependencies` : dépendances entre plugins, avec plages semver résolues sur des tags git `<plugin>--v<version>` (`claude plugin tag`).

**Structure d'une marketplace.** Un fichier `.claude-plugin/marketplace.json` (nom, propriétaire, liste de plugins avec leur `source`) dans un dépôt. Une source peut être un chemin relatif, un dépôt GitHub, une URL git, un sous-dossier git, npm, une archive ou une commande. Le nom de la marketplace doit être unique côté utilisateur ; les noms officiels sont réservés aux dépôts `github.com/anthropics/`.

**Installation.**

- `claude plugin marketplace add owner/repo`, puis `claude plugin install <plugin>@<marketplace>` ; ou en une commande depuis les versions récentes (`--marketplace`).
- Trois **portées** : *user* (tous mes projets), *project* (inscrit dans le `.claude/settings.json` commité ; chaque collaborateur installe quand même sur sa machine, après avoir accepté la confiance du dossier), *local* (moi, dans ce dépôt).
- Une marketplace peut être déclarée pour tout un dépôt (`extraKnownMarketplaces` via `--scope project`), mais pas dans les sessions cloud.
- Sans marketplace : `--plugin-dir` (une session) ou un dossier sous `~/.claude/skills/`.

**Versions et mises à jour.**

- La version calculée vient d'abord de `version` dans `plugin.json`, puis de l'entrée de marketplace, sinon **du SHA du commit**. Piège : si `version` est fixée et qu'on pousse sans l'incrémenter, personne ne reçoit la mise à jour. Deux stratégies : incrémenter à chaque release, ou omettre `version` pour suivre les commits.
- Mise à jour à la demande (`claude plugin update`, `/plugin marketplace update`) ; l'**auto-update est désactivée par défaut** pour une marketplace tierce, chaque utilisateur l'active.
- Pas de notion de canal : pour stable / bêta, deux marketplaces pointant sur deux refs.
- Ne jamais renommer un plugin publié (carte `renames` sinon) ; il n'existe pas d'état « déprécié ».
- Qualité de release : `claude plugin validate --strict` (en CI), installation depuis une marketplace locale avant publication, et **`claude plugin eval`** : une suite de cas (prompt + correcteurs) exécutés en sessions isolées, **comparés à une exécution sans le plugin**, avec seuil pour bloquer la CI.

**Sécurité et coût.** Un plugin activé fait partie de **toutes** les sessions : ses descriptions de skills consomment du contexte à chaque tour, ses hooks et serveurs tournent avec les droits de l'utilisateur, hors sandbox. `/usage` ventile la consommation par plugin. La documentation recommande de relire un plugin avant installation, et note que l'auto-update peut changer les fichiers relus.

---

## Ce que JDLC peut en retenir

### Idées qui confirment des choix existants

| # | Idée | Ce que ça donnerait dans JDLC | Convergence |
|---|---|---|---|
| 1 | **Exiger la preuve, pas l'affirmation** | La sortie réelle des commandes accompagne toute déclaration de fin. | Principe 8 ; [SFEIR](sfeir-sdlc-ai.md) |
| 2 | **Relecture par un agent qui n'a pas écrit le code**, en lecture seule, au regard de la spec | Agent relecteur JDLC avec `tools: Read, Grep, Glob`, imposé par l'outil. | [AIDD](aidd.md) (*checker* neuf), [Anthropic](anthropic.md) A3 |
| 3 | **Interview → `SPEC.md` → session neuve pour construire** | Phase Spécification : un skill qui interroge l'humain puis écrit la spec ; l'implémentation repart d'un contexte propre. | AIDD (Frame → contrat), A3 (contrat de sprint), principe 10 du manifeste AIDD |
| 4 | **Mode plan = porte de validation native** | La validation du plan s'appuie sur le mode plan de Claude Code plutôt que sur un mécanisme maison. | Principe 6 ; AIDD (pause sur le plan) |
| 5 | **`CLAUDE.md` court, seulement ce que Claude ne peut pas voir** | Le `CLAUDE.md` posé par JDLC dans un projet cible reste minimal ; le reste va en règles filtrées et en skills. | Principe 10 ; A2, A7 |
| 6 | **Garder l'ensemble imposé petit** | Peu de hooks, chacun justifié. | A4 (fatigue d'approbation) |
| 7 | **« Ce que vous pouvez arrêter de faire »** | Anthropic applique à sa propre doc l'idée que les composants se périment avec les modèles. | A3, A5 ; idée 1 d'[anthropic.md](anthropic.md) (revue de JDLC à chaque modèle) |
| 8 | **Après deux corrections ratées, repartir d'un contexte propre** | Règle d'hygiène dans les skills JDLC. | Manifeste AIDD (reconstruire le contexte), principe 11 |
| 9 | **Distribution par marketplace dans le dépôt** | Confirmée et détaillée par la doc officielle (voir implications ADR-0005). | AIDD ; ADR-0005, ADR-0006 |

### Idées nouvelles pour JDLC

| # | Idée | Ce que ça donnerait dans JDLC | Convergence |
|---|---|---|---|
| 10 | **Déterministe ou non déterministe** | Classer chaque règle JDLC : *consigne* (CLAUDE.md, skill : le modèle juge) ou *garantie* (hook, `deny` : l'outil applique). Critère : une règle qui ne supporte aucune exception devient un hook ; une préférence reste une consigne. À documenter pour chaque composant, à côté de l'hypothèse qu'il compense. | Prolonge principe 8 ; A4 |
| 11 | **La preuve imposée par un hook `Stop`** | Un hook `Stop` optionnel fourni par JDLC (par le plugin ou posé par l'init) : Claude ne peut pas finir son tour tant que la commande de vérification du projet échoue. C'est la version *déterministe* du principe 8. | Principe 8 ; SFEIR |
| 12 | **Cinq questions avant un run autonome** | Checklist d'entrée d'un éventuel mode autonome JDLC : définition de « terminé », vérification, périmètre, limite (tours, budget), destinataire et signataire. | Principe 6 ; AIDD (mode auto) ; A4 |
| 13 | **Cadrer la revue (« ignorer le style »)** | Les prompts de revue JDLC disent ce qu'il faut chercher *et ce qu'il faut ignorer*, sinon la revue pousse à la sur-ingénierie. | [Vidéo](video-senior-engineers-coding-agents.md) (simplification) ; A3 (évaluateur calibré) |
| 14 | **Invocation contrôlée des skills** | Les skills à effet de bord (release, merge, init) en `disable-model-invocation: true` : seul l'humain les déclenche. C'est une **porte humaine déterministe**. Le savoir de fond en `user-invocable: false`. | Principe 6 |
| 15 | **Évaluer JDLC comme un produit** | Une suite `claude plugin eval` pour JDLC : cas réalistes, correcteurs, **comparaison avec et sans JDLC**, exécutée en CI et à chaque nouveau modèle. Rend mesurable la question « JDLC apporte-t-il quelque chose ? ». | A6 (changer les instructions = changer le comportement) ; *Outcome over Output* (manifeste AIDD) |
| 16 | **Coût permanent d'un plugin activé** | Chaque skill invocable par le modèle coûte sa description à chaque tour, dans chaque projet. JDLC doit avoir **peu de skills auto-invocables** et des descriptions courtes ; vérifier avec `/usage`. Argument contre un découpage en nombreux plugins. | Principe 7 ; A2 ; AIDD relativisé (51 skills) |
| 17 | **Mémoire d'agent versionnée** | Un relecteur JDLC avec `memory: project` qui accumule les motifs récurrents du projet dans `.claude/agent-memory/`. Piste pour la phase Retour d'expérience, à confronter au skill `learn` d'AIDD. | A7 (hiérarchie de mémoire), AIDD (`learn`) |
| 18 | **S'appuyer sur les skills fournis** | Ne pas réinventer `/code-review`, `/verify`, `/run-skill-generator`, `/simplify`, `/doctor prompt-audit` : les composer dans le cycle JDLC. Un skill projet `verify` se lance même automatiquement avant chaque commit. | Principe 7 ; A1 (simplicité) |
| 19 | **Règles filtrées par chemin** | Les gabarits JDLC (ADR, specs, journal) peuvent porter leurs règles dans `.claude/rules/` avec `paths: docs/adr/**`, sans alourdir `CLAUDE.md`. | A2 (contexte juste à temps) |
| 20 | **Les runs scriptés** | Pour la CI : `claude -p` avec `--bare` (reproductible), `--allowedTools`, `--permission-mode dontAsk`, `--max-turns`. Base d'une éventuelle vérification JDLC en CI. | Principe 8 |

### Implications pour l'ADR-0005 (installation)

- **Confirmé** : le dépôt JDLC peut être sa propre marketplace (`.claude-plugin/marketplace.json`), avec un ou plusieurs plugins en chemins relatifs. Installation et mise à jour natives.
- **Contrainte forte, nouvelle** : un plugin ne peut livrer **ni `CLAUDE.md`, ni `.claude/rules/`, ni règles de permissions**. Il peut en revanche livrer des hooks (actifs dans tous les projets où il est activé). Tout ce qui doit vivre dans le dépôt cible (CLAUDE.md, règles, `docs/`, gabarits, règles `deny`, commande de vérification propre au projet) doit être **écrit par un skill d'initialisation**. L'orientation hybride de l'ADR-0005 n'est donc pas un choix de confort : c'est la seule voie.
- **Hook livré par le plugin ou posé dans le projet ?** Un hook `Stop` livré par le plugin s'appliquerait à tous les projets où JDLC est activé ; il devrait lire la commande de vérification dans un fichier du projet et ne rien faire en son absence. Posé par l'init dans `.claude/settings.json`, il reste visible et ajustable localement. À trancher.
- **Versionnement** : choisir entre `version` incrémentée à chaque release (avec `claude plugin tag`) ou suivi des commits (pas de `version`). Pour un projet pédagogique avec releases, la première paraît plus lisible. `claude plugin validate --strict` en CI.
- **Savoir quelle version est installée dans un projet** : la version installée du plugin est enregistrée **par machine** (`~/.claude/plugins/installed_plugins.json`), pas dans le dépôt. Pour tracer la version des éléments projet, le skill d'init devra écrire un marqueur dans le projet (ex. version JDLC + date). *À concevoir ; aucune solution native trouvée.*
- **Mises à jour sans écraser l'existant** : les mises à jour du plugin sont natives, mais ne concernent pas les fichiers posés dans le projet. Le skill d'init/mise à jour devra comparer et proposer, jamais écraser (rejoint la règle AIDD « n'écrire que ce qui est validé »).
- **Portée** : installer JDLC en portée *project* rend la dépendance visible dans le `.claude/settings.json` du projet cible. Pour un usage solo, la portée *user* est plus simple. À trancher.
- **Auto-update désactivée par défaut** pour une marketplace tierce : documenter comment l'activer, ou assumer des mises à jour manuelles.
- **Canaux** (stable / bêta) : deux marketplaces si besoin un jour. **Ne jamais renommer** un plugin publié : choisir les noms avec soin dès le départ.
- **Le mode auto ne se configure pas au niveau projet** : la configuration de permissions recommandée par JDLC (idée 3 d'[anthropic.md](anthropic.md)) ne peut passer que par des règles `allow`/`ask`/`deny` et des hooks, pas par `autoMode`.

## Ce qu'on écarte ou relativise

- **Les fonctions d'organisation** (réglages gérés, allowlists de marketplaces, déploiement en entreprise, passerelles, HIPAA) : hors périmètre solo.
- **Les surfaces récentes ou spécialisées** (workflows dynamiques, *agent teams*, messages entre sessions, *mods*, *monitors*, *channels*, artefacts, routines cloud) : intéressantes, mais à n'envisager que si un besoin réel apparaît (principe 1). Plusieurs dépendent du plan ou de réglages.
- **Les hooks `prompt` et `agent`** : ils font juger un modèle, donc ne garantissent rien. Pour une vraie garantie, `command` ou `http`.
- **Le réglage `agent` d'un plugin** (faire d'un agent JDLC le fil principal) : séduisant pour un orchestrateur, mais il remplace le comportement par défaut de toutes les sessions. À éviter tant que le besoin n'est pas démontré.
- **Les chiffres et versions** (numéros de version minimale, plafonds de tokens, limites de blocage) : ils changent d'une semaine à l'autre. On ne les recopie pas dans les artefacts JDLC.
- **Le « explore → plan → code → commit »** : il n'est plus mis en avant tel quel par la page de bonnes pratiques actuelle. On garde l'esprit (mode plan, vérification, commit), pas la formule.
- **L'ouverture multi-outils** offerte par le standard Agent Skills et `AGENTS.md` : notée, mais hors périmètre ([ADR-0006](../adr/0006-claude-code-uniquement.md)).

## Pages à revisiter au moment de construire

| Composant JDLC | Pages |
|---|---|
| Structure du dépôt et des plugins | [Create a plugin](https://code.claude.com/docs/en/plugins/create), [Add components](https://code.claude.com/docs/en/plugins/components), [Manifest reference](https://code.claude.com/docs/en/plugins/manifest-reference), [Create a marketplace](https://code.claude.com/docs/en/plugins/create-marketplace), [Marketplace reference](https://code.claude.com/docs/en/plugins/marketplace-reference) |
| Release, versions, mises à jour | [Publish](https://code.claude.com/docs/en/plugins/publish), [Host a marketplace](https://code.claude.com/docs/en/plugins/host-marketplace), [Loading reference](https://code.claude.com/docs/en/plugins/loading) (versions, cache), [Dependencies](https://code.claude.com/docs/en/plugins/dependencies), [Plugin commands](https://code.claude.com/docs/en/plugins/cli-reference) |
| Évaluation de JDLC | [Test plugins with evals](https://code.claude.com/docs/en/plugin-evals), [Measure plugin cost](https://code.claude.com/docs/en/plugins/measure), section *Evaluate and iterate* de [Skills](https://code.claude.com/docs/en/skills) |
| Skills (gabarit, invocation, sous-agent) | [Skills](https://code.claude.com/docs/en/skills) (frontmatter, cycle de vie, `context: fork`) |
| Agents relecteur / vérificateur | [Subagents](https://code.claude.com/docs/en/sub-agents) (frontmatter, mémoire, outils) |
| Garde-fous et preuve imposée | [Hooks guide](https://code.claude.com/docs/en/hooks-guide), [Hooks reference](https://code.claude.com/docs/en/hooks), [Permissions](https://code.claude.com/docs/en/permissions), [Permission modes](https://code.claude.com/docs/en/permission-modes), [Sandboxing](https://code.claude.com/docs/en/sandboxing) |
| Mémoire projet et `CLAUDE.md` posé par l'init | [Memory](https://code.claude.com/docs/en/memory), [Explore the .claude directory](https://code.claude.com/docs/en/claude-directory) |
| Runs autonomes / CI | [Best practices](https://code.claude.com/docs/en/best-practices) (*Let Claude run without you*), [Headless](https://code.claude.com/docs/en/headless), [Goal](https://code.claude.com/docs/en/goal), [GitHub Actions](https://code.claude.com/docs/en/github-actions) |
| Sécurité, section « Trust and safety » | [Plugin security](https://code.claude.com/docs/en/plugins/security) |
| Vue d'ensemble des choix | [Extend Claude Code](https://code.claude.com/docs/en/features-overview) |
