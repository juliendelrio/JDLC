# Politique d'inspiration et de licences

Décision associée : [ADR-0003](adr/0003-politique-inspirations-licences.md).

> Ce document est une politique de bonne conduite, pas un avis juridique. En cas de doute, on n'importe pas : on se limite à l'idée et on la réécrit.

## 1. Trois niveaux de reprise

| Niveau | Définition | Obligations |
|---|---|---|
| **Idée** | On reprend un concept, une méthode, une structure de processus, et on l'exprime avec nos propres mots. | Créditer la source dans [CREDITS.md](../CREDITS.md) (courtoisie, honnêteté intellectuelle). |
| **Adaptation** | On s'inspire étroitement d'un contenu précis (un prompt, un gabarit, une checklist) en le réécrivant ou en le modifiant. | Créditer **et** vérifier la licence : une adaptation proche reste une œuvre dérivée. Si la licence n'est pas compatible, on redescend au niveau « idée » (réécriture complète sans le texte source sous les yeux). |
| **Import** | On copie tout ou partie d'un contenu (texte, code, fichier), modifié ou non. | Licence compatible obligatoire + conservation des mentions de copyright et de licence + signalement des modifications + entrée dans CREDITS.md. |

## 2. Matrice de compatibilité

Établie pour la licence de JDLC : **MIT** ([ADR-0004](adr/0004-licence-du-projet.md)). À revoir si ce choix change.

| Licence source | Import possible ? | Conditions |
|---|---|---|
| MIT, BSD-2/3, ISC, 0BSD, MIT-0, Unlicense, CC0 | ✅ Oui | Conserver la mention de copyright et le texte de licence (sauf licences « sans condition » comme 0BSD, MIT-0, CC0, où c'est une simple courtoisie). |
| Apache-2.0 | ✅ Oui | Conserver la licence et le fichier `NOTICE` s'il existe, signaler les modifications dans les fichiers modifiés. |
| CC BY 4.0 | ✅ Oui (documentation) | Attribution : auteur, titre, lien, licence, mention des modifications. |
| CC BY-SA 4.0 | ⚠️ À éviter | Les contenus dérivés doivent rester sous CC BY-SA : seulement si isolés dans un répertoire distinct clairement licencié. Par défaut : niveau « idée ». |
| MPL-2.0 | ⚠️ Au cas par cas | Copyleft au niveau du fichier : un fichier importé reste sous MPL-2.0. |
| GPL, LGPL, AGPL | ❌ Non | Imposerait la licence copyleft à JDLC. Niveau « idée » uniquement. |
| CC BY-NC, CC BY-ND et variantes | ❌ Non | Restrictions incompatibles (usage commercial / modifications). Niveau « idée » uniquement. |
| **Aucune licence** | ❌ Non | Par défaut, tous droits réservés. Niveau « idée » uniquement, ou demander l'autorisation à l'auteur. |
| Propriétaire (documentation produit, CGU…) | ❌ Non | Niveau « idée » uniquement. |

## 3. Procédure d'import

1. **Identifier la version exacte** reprise : URL du dépôt + commit ou tag.
2. **Lire la licence à cette version** (fichier `LICENSE`, en-têtes de fichiers, éventuel `NOTICE`). Ne jamais se fier à sa mémoire ni à une licence lue sur une autre version.
3. **Vérifier la compatibilité** avec la matrice ci-dessus.
4. **Placer le contenu importé** avec un en-tête de provenance (format ci-dessous) ; conserver les mentions de copyright d'origine.
5. **Copier le texte de licence** de la source dans `licenses/<source>-LICENSE` (et `NOTICE` le cas échéant).
6. **Ajouter une entrée** dans [CREDITS.md](../CREDITS.md).
7. **Mentionner l'import dans le message de commit** (`Imported from <source>@<commit> (<licence>)`).

### En-tête de provenance (fichiers importés ou adaptés)

```markdown
<!--
Source: <nom du projet> — <URL>
Version: <commit ou tag>
Copyright: <titulaire(s) des droits>
License: <identifiant SPDX> (see licenses/<source>-LICENSE)
Changes: <résumé des modifications, ou "none">
-->
```

## 4. Contrôles

- Avant chaque release de JDLC : relecture de CREDITS.md et vérification que chaque fichier importé porte son en-tête.
- Les agents qui travaillent sur JDLC appliquent cette procédure (voir `CLAUDE.md`).
