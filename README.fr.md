<img src="assets/icon.svg" width="96" height="96" alt="Icône de Trellis">

# Trellis

*English: [README.md](README.md)*

Un plugin [Claude Code](https://claude.com/claude-code) de skills et d'agents qui accompagne un projet de l'idée brute jusqu'au logiciel livré. Il donne au projet une structure pour grandir, sans décider de sa direction.

> Statut : travail en cours (0.1.0). Sous [licence MIT](LICENSE).

## Le principe

Chaque phase écrit un fichier dans le dépôt du projet, et la phase suivante le lit. Les fichiers sont la mémoire partagée ; rien ne vit uniquement dans une conversation.

```
idée ──► INCUBATION ──► CONCEPTION ────────► DÉVELOPPEMENT ──────► VIE DU PROJET
         SPEC.md        ARCHITECTURE.md      PLAN.md               STATUS.md
                        docs/adr/*.md        (tranches livrables)  CHANGELOG.md
```

Trellis est **strict mais souple** : les skills signalent ce qui manque (pas de spécification validée, pas de critères d'acceptation) et ce que cela risque, puis te laissent décider. Les dérogations sont consignées dans les documents.

## Contenu

| Nom | Type | Phase | État |
| --- | --- | --- | --- |
| `incubate` | skill | incubation | écrit |
| `architect` | skill | conception | prévu |
| `adr` | skill | conception | prévu |
| `slice` | skill | développement | prévu |
| `deliver` | skill | développement | prévu |
| `resume` | skill | toutes | prévu |
| `devils-advocate` | agent | incubation, conception | prévu |
| `verifier` | agent | développement | prévu |

Candidats pour plus tard : `change-course`, `project-overview`, `publish`, `retro`, `prior-art`, `spike`, un agent de contrôle de cohérence, un hook `SessionStart`.

## Utilisation

Charger le plugin depuis une copie locale :

```sh
claude --plugin-dir /chemin/vers/trellis
```

Puis demander à Claude d'incuber une idée, ou invoquer directement `/trellis:incubate`.

## Langues

Les noms (skills, agents, fichiers) sont en anglais. La documentation et les gabarits sont en anglais et en français ; les documents générés suivent la langue du projet.
