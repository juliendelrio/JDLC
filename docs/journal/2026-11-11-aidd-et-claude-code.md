# 2026-11-11 — AIDD, et le choix de Claude Code uniquement

## Ce qu'on a fait

Étude de deux sources de la communauté AI-Driven Dev ([note d'analyse](../inspirations/aidd.md)) :

- le **framework AIDD**, un SDLC complet distribué en plugins Claude Code, sous licence MIT ;
- le **manifeste AIDD** : 4 valeurs et 12 principes, texte sous licence CC BY 4.0.

Contrairement à SFEIR, les licences sont ouvertes et compatibles avec la nôtre. On pourrait donc aller jusqu'à l'import. Pour l'instant, on reste au niveau « idée » : on n'importe que lorsqu'on construit un composant précis.

**Bonne pratique appliquée :** la licence a été lue sur le **commit exact** consulté, et pas seulement dans les métadonnées GitHub. Pour le manifeste, GitHub affiche `NOASSERTION`, car le fichier `LICENSE.md` combine deux licences : seule sa lecture permet de savoir que le texte est sous CC BY 4.0 et le code sous MIT.

## Décision prise

**JDLC cible uniquement Claude Code pour l'instant** ([ADR-0006](../adr/0006-claude-code-uniquement.md)). L'étude d'AIDD a rendu visible le coût du multi-outils : CLI de traduction, formats par outil, garde-fous de portabilité. Ce coût n'est pas justifié pour un projet solo qui démarre. Conséquence directe : la distribution par marketplace Claude Code devient la piste naturelle d'installation ([ADR-0005](../adr/0005-mecanisme-installation.md), toujours à affiner).

## Principes intégrés à la vision

Trois principes du manifeste rejoignent la [vision](../vision.md) :

- **n°9** — ne pas déléguer ce qu'on ne sait pas évaluer ;
- **n°10** — le contexte se traite comme du code ;
- **n°11** — réparer le système avant de pousser le modèle.

On reste en phase d'analyse : le reste des idées est noté, et le tri définitif se fera plus tard.

## Ce qu'on a appris

- **Les sources convergent.** SFEIR et AIDD, indépendamment, insistent sur :
  - la spec comme contrat ;
  - les validations humaines sur quelques artefacts clés ;
  - la preuve plutôt que la parole de l'agent ;
  - la capitalisation des leçons.
- **La question des validations humaines s'éclaire.** AIDD rend les trois pauses (contrat, plan, résultat) optionnelles selon le mode. C'est peut-être la réponse à « 2 ou 3 portes ? ».
- **Une question nouvelle et légitime :** pourquoi construire JDLC plutôt qu'utiliser AIDD ? Ce n'est pas une menace pour le projet, c'est une décision à prendre consciemment, dans un ADR, au moment de concevoir le premier composant.
