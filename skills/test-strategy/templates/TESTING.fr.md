# <Nom du projet> : stratégie de tests

| | |
| --- | --- |
| Statut | brouillon / acceptée |
| Version | 0.1 |
| Date | AAAA-MM-JJ |
| Basée sur | `SPEC.md`, `ARCHITECTURE.md`, `DESIGN.md` |

## Objectifs et risques

Objectif en une ou deux phrases.

| # | Ce qui ferait le plus mal en cas d'erreur | Pourquoi c'est risqué | Où c'est testé |
| --- | --- | --- | --- |
| T1 | | | |

Difficile à tester : ... (et l'approche retenue).

## Niveaux de test

| Niveau | Couvre | Outil | S'exécute où | Quand |
| --- | --- | --- | --- | --- |
| Unitaire | | | local, CI | à chaque commit |
| Intégration | | | | |
| Bout en bout | | | | |
| Accessibilité | | | | |
| Manuel et exploratoire | | | | |

Niveaux volontairement écartés, et pourquoi : ...

## Matrice de vérification

| Exigence | Priorité | Niveau | Vérification concrète (observable) | Automatisée |
| --- | --- | --- | --- | --- |
| R1 | Must | | | oui / non |

Critères de succès mesurés seulement après la publication ou à l'usage :

| Critère (de SPEC.md) | Comment on l'observe | Quand |
| --- | --- | --- |

## Données de test et environnements

- Jeux de données et fabriques : ...
- Données synthétiques ou réelles, et où chacune vit : ...
- Doublures des services externes : ...
- Maîtrise du temps, de l'aléatoire et de la concurrence : ...
- Environnements : ...
- Migrations, conflits de synchronisation et reprise : ...

## Portes de qualité

| Moment | Ce qui s'exécute | Bloque | Avertit |
| --- | --- | --- | --- |
| Chaque commit | | | |
| Chaque pull request | | | |
| Chaque publication | | | |

Politique pour les tests instables : ...

## Tests non fonctionnels et manuels

- Budgets de performance : ...
- Contrôles de sécurité et de vie privée : ...
- Contrôles d'accessibilité : ...
- Liste de contrôle manuelle et exploratoire : ...
- Critères de sortie de la bêta ou de l'usage au quotidien : ...

## Définition de « fini » (par tranche)

- [ ] Tests écrits d'abord ou en parallèle, et qui passent
- [ ] Critères d'acceptation vérifiés
- [ ] Lignes concernées de la matrice de vérification mises à jour
- [ ] Documentation et `STATUS.md` à jour
- [ ] Aucun nouvel avertissement

## Choix d'outillage

| Décision | ADR ou note |
| --- | --- |
| | |

## Constats de testabilité pour l'architecture

Tout ce qui est difficile à tester à cause de la façon dont le système est construit, à remonter à `architect`.

- ...

## Points ouverts

- ...
