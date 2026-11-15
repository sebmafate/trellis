## Résumé

<Une ou deux phrases : ce qui est vrai maintenant pour l'utilisateur.>

Tranche **<ID> : <titre>** de `PLAN.md`. Répond à : <R1, R3>.

## Changements

| Tâche | Ce qui a changé | Commit |
| --- | --- | --- |
| T1 | | |

## Critères d'acceptation

| Critère | Résultat | Preuve |
| --- | --- | --- |
| | atteint | nom du test ou commande |

## QA

Verdict : **<accepté / accepté avec remarques>** après <N> tour(s).

<Remarques de la QA, le cas échéant.>

## Décisions

<Décisions prises pendant le travail, avec liens vers les ADR écrits dans cette branche. « Aucune » s'il n'y en a pas.>

## Écarts et non vérifié

- <Où le travail a différé de la consigne ou de l'architecture, et ce qui a été choisi.>
- <Ce qui n'a pas pu être vérifié, et pourquoi.>

## Relecture et fusion

- Base : `<branche de base>`.
- <Empilée : dépend de #<N> ; fusionner dans l'ordre, puis changer la cible si besoin.>
- Nettoyage après fusion : `git worktree remove <chemin>` et `git branch -d <branche>`.
