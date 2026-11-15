<img src="assets/icon.svg" width="96" height="96" alt="Icône de Trellis">

# Trellis

*English: [README.md](README.md)*

Un plugin [Claude Code](https://claude.com/claude-code) de skills et d'agents qui accompagne un projet de l'idée brute jusqu'au logiciel livré. Il donne au projet une structure pour grandir, sans décider de sa direction.

> Statut : travail en cours (0.4.0). Sous [licence MIT](LICENSE).

## Le principe

Chaque phase écrit un fichier dans le dépôt du projet, et la phase suivante le lit. Les fichiers sont la mémoire partagée ; rien ne vit uniquement dans une conversation.

```
idée ──► INCUBATION ──► CONCEPTION ───────────────► DÉVELOPPEMENT ──────► VIE DU PROJET
         SPEC.md        ARCHITECTURE.md + ADR     PLAN.md               STATUS.md
                        DESIGN.md (UX, UI)        (tranches livrables)  CHANGELOG.md
                        TESTING.md
```

Trellis est **strict mais souple** : les skills signalent ce qui manque (pas de spécification validée, pas de critères d'acceptation) et ce que cela risque, puis te laissent décider. Les dérogations sont consignées dans les documents.

## Contenu

| Nom | Type | Phase | État |
| --- | --- | --- | --- |
| `incubate` | skill | incubation | **disponible** |
| `prior-art` | skill | incubation | **disponible** |
| `devils-advocate` | agent | incubation, conception | **disponible** |
| `ux` | skill | conception | **disponible** |
| `architect` | skill | conception | **disponible** |
| `adr` | skill | conception | **disponible** |
| `spike` | skill | conception | **disponible** |
| `ui` | skill | conception | **disponible** |
| `design-reviewer` | agent | conception | **disponible** |
| `security-reviewer` | agent | conception, publication | **disponible** |
| `test-strategy` | skill | conception | **disponible** |
| `bootstrap` | skill | développement | **disponible** |
| `slice` | skill | développement | **disponible** |
| `deliver` | skill | développement | **disponible** |
| `verifier` | agent | développement | **disponible** |
| `publish` | skill | publication | **disponible** |
| `resume` | skill | toutes | **disponible** |
| `change-course` | skill | toutes | **disponible** |
| `retro` | skill | toutes | **disponible** |
| `project-overview` | skill | toutes | **disponible** |
| `coherence-checker` | agent | toutes | **disponible** |
| `SessionStart` hook | hook | toutes | **disponible** |

Idées pour plus tard : une phase de maintenance (mises à jour de dépendances, dépréciation), un skill de documentation pour les utilisateurs finaux, un skill de prototype cliquable.

## Utilisation

```
/plugin install trellis --marketplace https://git.vbmaf.net/sebastien/trellis.git
```

Tant que le dépôt est privé, utiliser la forme SSH : `--marketplace git@git.vbmaf.net:sebastien/trellis.git`.

Puis demander à Claude d'incuber une idée, ou invoquer directement `/trellis:incubate`. Guide complet (installation SSH, essai sans installer, détail de chaque étape, dépannage) : [docs/usage.fr.md](docs/usage.fr.md).

## Langues

Les noms (skills, agents, fichiers) sont en anglais. La documentation et les gabarits sont en anglais et en français ; les documents générés suivent la langue du projet.
