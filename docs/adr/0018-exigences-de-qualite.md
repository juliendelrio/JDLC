# ADR-0018 — Exigences de qualité : un guide à tiroirs au cadrage, des règles garanties ensuite

- **Statut** : Accepté (hypothèse à vérifier par l'usage, voir [ADR-0011](0011-versionnement.md))
- **Date** : 2026-11-12
- **Inspirations** : aucune source importée. La liste des pratiques vient de l'auteur.

## Contexte

La relecture du cycle ([ADR-0017](0017-cycle-complet.md)) a relevé que la sécurité et les qualités non fonctionnelles n'apparaissaient nulle part : ni dans le cadre, ni dans les contrôles. La question était de savoir ce que JDLC exige d'un projet, et ce qu'il lui fournit.

## Options envisagées

1. **JDLC impose des contrôles** : un projet ne passe pas une porte sans eux.
2. **JDLC ne dit rien** : la qualité relève entièrement du projet cible.
3. **JDLC guide au cadrage, garantit ensuite** : une rubrique dans le cadre, un guide à tiroirs dont l'humain décide, puis des règles que JDLC fait respecter.

## Décision

**Option 3.**

- Le cadre contient une rubrique **« exigences de qualité »**. Un agent la rédige avec un humain pendant le cadrage, et un humain la valide avec le reste du cadre.
- JDLC fournit pour cela un **guide « à tiroirs »** : chaque pratique est un tiroir que l'on ouvre ou non selon le projet. Aucun tiroir n'est imposé.
- **Au moment du cadrage et de l'architecture, l'humain reste décisionnaire.** JDLC y est un guide et un conseiller, pas un point de blocage.
- **Une fois le cadre validé, JDLC est garant des règles cadrées.** Dans les étapes automatisées et d'implémentation, il peut bloquer : un ticket qui ne respecte pas une exigence retenue n'avance pas.

Les pratiques devenues incontournables :

- *Security by design* ;
- *Privacy by design* ;
- *Accessibility by design* ;
- *Eco-design* ;
- *Compliance by design*, pour l'IA.

Les pratiques complémentaires :

- *Observability by design* ;
- *Testability by design* ;
- *Resilience by design* ;
- *Operability / Deployability by design* ;
- *Maintainability / Evolvability by design* ;
- *Interoperability by design* ;
- *Reversibility / Portability by design* ;
- *Ethics / Explainability by design* ;
- *Inclusion by design*.

## Conséquences

- Les exigences retenues font partie du cadre : les agents les respectent comme le reste, et le vérificateur contrôle un ticket contre elles.
- « Incontournable » veut dire que JDLC propose toujours ces tiroirs à l'humain au cadrage. Cela ne veut pas dire qu'il refuse d'avancer si l'humain les écarte.
- La limite est nette : JDLC ne bloque pas le **choix** des règles, il bloque leur **non-respect**. Une règle ne se change qu'en revenant au cadre, devant un humain.
- Restent à concevoir : le contenu de chaque tiroir, la façon dont un agent les propose sans alourdir le cadrage, et la trace laissée quand un humain écarte une pratique.
- Les outils de contrôle (intégration continue, analyse statique, détection de secrets, surveillance) restent ceux du projet cible.
