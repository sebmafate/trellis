# <Nom du projet> : notes de travail pour Claude

<Une phrase : ce qu'est ce projet.>

## Documents

| Fichier | Ce que c'est | Règle |
| --- | --- | --- |
| `SPEC.md` | Quoi et pourquoi : exigences `R1..`, périmètre, risques, critères d'abandon | Les identifiants sont stables ; ne jamais renuméroter |
| `ARCHITECTURE.md` | Comment : composants, index des décisions, traçabilité | À mettre à jour quand la réalité change |
| `docs/adr/` | Une décision par fichier | Ne jamais modifier un ADR accepté ; le remplacer |
| `DESIGN.md` | Parcours et écrans UX, jetons et composants UI | Ignorer s'il n'y a pas d'interface |
| `TESTING.md` | Niveaux de test, matrice de vérification, définition de « fini » | À suivre pour chaque tranche |
| `PLAN.md` | Tranches `SL0..` avec critères d'acceptation | Une tranche à la fois |
| `STATUS.md` | Phase en cours, prochaine action | À jour après chaque tranche |
| `CHANGELOG.md` | Changements visibles par l'utilisateur | Une ligne par tranche livrée |

## Commandes

```sh
# construire
...
# tester
...
# lint / formatage
...
# lancer
...
```

## Conventions

- Stack : ... (voir ADR ...).
- Branches et commits : ...
- Les tests d'abord ; une tranche n'est finie que lorsque ses critères d'acceptation sont vérifiés et les documents à jour (voir `TESTING.md`).
- Ni données personnelles ni secrets dans le dépôt.

## Travailler ici avec Trellis

- Reprendre le projet : `/trellis-flow:resume`, puis `/trellis-flow:deliver`.
- Une exigence, une décision ou le design change : `/trellis-flow:change-course`.
- Un choix à consigner : `/trellis-flow:adr`. Une inconnue à lever : `/trellis-flow:spike`.
