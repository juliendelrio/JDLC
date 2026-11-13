# ADR-0021 — Accompagner l'humain : dire ce qu'on fait, faire ce qu'on dit

- **Statut** : Accepté (hypothèse à vérifier par l'usage, voir [ADR-0011](0011-versionnement.md))
- **Date** : 2026-11-13
- **Inspirations** : aucune source importée. L'exigence vient de l'auteur.

## Contexte

JDLC pose un cycle, des portes, des rôles et un vocabulaire. Un humain qui ne comprend pas ce que le framework fait, ou pourquoi il s'arrête, le subit au lieu de s'en servir. Le principe 9 de la [vision](../vision.md) demande déjà que l'humain n'accepte pas ce qu'il ne comprend pas : cela vaut aussi pour le framework lui-même.

## Options envisagées

1. **Un framework pour initiés** : la documentation existe, à l'humain de la lire.
2. **Un framework qui accompagne** : il explique ce qu'il fait au moment où il le fait, et renvoie à la documentation quand c'est utile.

## Décision

**Option 2.** JDLC doit être le plus simple possible à utiliser.

- **Dire ce qu'on fait, faire ce qu'on dit.** JDLC annonce ce qu'il va faire, puis fait exactement cela. Sa documentation décrit son comportement réel.
- **L'humain comprend toujours ce qu'il fait.** À chaque porte et à chaque question, un agent dit à l'humain ce qu'on lui demande de décider, pourquoi, et ce qui se passera ensuite.
- **JDLC est pédagogique.** Il accompagne l'humain et le renvoie vers la documentation quand c'est utile.
- **Un module optionnel peut détecter quand un humain sort du cadre du framework**, et lui proposer d'y revenir si besoin. Il propose, il n'impose pas. L'humain choisit de l'activer.
- **Le coût d'un module optionnel est annoncé avant son activation**, en tokens estimés. Cette estimation se construit empiriquement, par la mesure.

## Conséquences

- Chaque composant de JDLC se juge aussi sur ce critère : un humain qui le rencontre pour la première fois comprend-il ce qui se passe ?
- Le [glossaire](../glossaire.md) et la documentation de conception servent de cible aux renvois. Une documentation pour l'utilisateur d'un projet cible reste à écrire.
- Annoncer un coût en tokens demande de le mesurer : cela s'ajoute aux mesures de l'[ADR-0015](0015-mesure-minimale.md).
- **Piste d'amélioration** : estimer de la même façon, empiriquement, le coût en tokens du framework dans son ensemble.
- Restent à concevoir :
  - ce que « sortir du cadre du framework » veut dire, et comment un agent le détecte sans surveiller l'humain plus qu'il ne le souhaite ;
  - la forme des explications, pour qu'elles n'alourdissent pas le travail d'un humain qui connaît déjà JDLC ;
  - la méthode de mesure du coût en tokens.
- Cette décision ne contredit pas l'[ADR-0018](0018-exigences-de-qualite.md) : JDLC fait respecter les règles du cadre pendant la réalisation, et il explique à l'humain pourquoi un ticket n'avance pas.
