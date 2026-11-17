<img src="assets/icon.svg" width="96" height="96" alt="Icône de Trellis">

# Trellis

*English: [README.md](README.md)*

Un plugin [Claude Code](https://claude.com/claude-code) de skills et d'agents qui accompagne un projet de l'idée brute jusqu'au logiciel livré. Il donne au projet une structure pour grandir, sans décider de sa direction.

> Statut : travail en cours (0.11.0). Sous [licence MIT](LICENSE).

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
| `adopt` | skill | toutes (projet existant) | **disponible** |
| `prior-art` | skill | incubation | **disponible** |
| `naming` | skill | incubation | **disponible** |
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
| `autopilot` | skill | développement | **disponible** |
| `product-owner` | agent | développement | **disponible** |
| `task-writer` | agent | développement | **disponible** |
| `developer` | agent | développement | **disponible** |
| `verifier` | agent | développement | **disponible** |
| `pr-reviewer` | agent | développement | **disponible** |
| `follow-up` | skill | développement (facultatif) | **disponible** |
| `publish` | skill | publication | **disponible** |
| `resume` | skill | toutes | **disponible** |
| `change-course` | skill | toutes | **disponible** |
| `retro` | skill | toutes | **disponible** |
| `project-overview` | skill | toutes | **disponible** |
| `coherence-checker` | agent | toutes | **disponible** |
| `SessionStart` hook | hook | toutes | **disponible** |

Tout ce qui dépasse le parcours principal est **facultatif** : `naming`, `prior-art`, `autopilot`, son étape `--review` et `follow-up` ne s'exécutent que si vous les demandez.

**À voir avant d'utiliser :** [examples/](examples/README.fr.md) montre les documents d'un petit projet fictif phase par phase, et le guide d'usage contient un [exemple de session](docs/usage.fr.md) et un tableau de recettes (« que taper quand... »).

Idées pour plus tard : une phase de maintenance (mises à jour de dépendances, dépréciation), un skill de documentation pour les utilisateurs finaux, un skill de prototype cliquable, les leçons d'une rétrospective reprises au projet suivant.

## Utilisation

```
claude plugin install trellis --marketplace https://github.com/sebmafate/trellis.git
```

La forme abrégée `--marketplace sebmafate/trellis` est acceptée aussi. À lancer dans un terminal, puis démarrer une nouvelle session. (Dans l'interface interactive de Claude Code, on peut taper `/plugin install …` ; des hôtes comme T3 Code n'offrent pas cette commande.)

Puis demander à Claude d'incuber une idée, ou invoquer directement `/trellis:incubate`. Guide complet (options d'installation, essai sans installer, détail de chaque étape, dépannage) : [docs/usage.fr.md](docs/usage.fr.md).

## Ce que Trellis exécute

Trellis est du Markdown (skills et prompts d'agents) plus un petit script shell. Il n'a ni serveur, ni compte, ni télémétrie, ni dépendance à installer, et il ne lit, ne stocke ni n'envoie aucun identifiant. Voici tout ce qu'il peut faire sur ta machine ou sur le réseau, et quand.

**Au début d'une session (hook, automatique).** Le hook `SessionStart` (`hooks/session-status.sh`) lit les 30 premières lignes du `STATUS.md` de ton projet, s'il existe, et les ajoute au contexte de Claude, étiquetées comme des données du projet. Il ne fait aucun appel réseau et n'affiche rien s'il n'y a pas de `STATUS.md`. Définis `TRELLIS_NO_STATUS=1` pour le désactiver.

**Sur tes fichiers, quand tu invoques un skill ou un agent.**
- Les skills écrivent les documents du projet (`SPEC.md`, `ARCHITECTURE.md`, `PLAN.md`, `STATUS.md`, `docs/`…) et, pour `autopilot`, `follow-up` et `adopt`, des fichiers de travail sous `.trellis/`.
- L'agent `developer` crée une branche et un worktree git, lance les commandes de tests, de lint et de build propres à ton projet, et fait des commits locaux. Il ne pousse ni ne fusionne jamais.
- Les agents en lecture seule (`verifier`, `pr-reviewer`, `coherence-checker`, `product-owner`, `security-reviewer`) lancent des commandes de lecture comme `git log`, `git diff` et tes commandes de test, et ne modifient rien.

**Sur le web, seulement pour la recherche.** `naming`, `prior-art` et les agents `devils-advocate`, `design-reviewer` et `security-reviewer` peuvent chercher et lire des pages web publiques. Les requêtes portent sur ton idée, les noms comparés ou les normes vérifiées : elles peuvent donc reprendre ce que les documents de ton projet en disent.

**Sur ta forge git, seulement à ta demande.**
- `autopilot` pousse une branche (jamais avec `--force`) et ouvre une pull request par tranche **seulement si tu l'as autorisé**, avec ta connexion existante à `gh` (GitHub), `fj` (Forgejo ou Gitea) ou `glab` (GitLab, à titre expérimental). Avec `--review-comment`, il poste un commentaire de relecture par pull request.
- `follow-up` lit les commentaires de relecture et les contrôles d'une pull request, et ne pousse des corrections sur sa branche et ne poste un commentaire de synthèse qu'après les étapes que tu acceptes.
- `publish` demande un accord explicite avant chaque étape sortante : commit, push d'un tag, envoi d'un artefact, création d'une release, déploiement.

Rien d'autre ne quitte ta machine. Si tu n'utilises que les skills de cadrage et de conception, Trellis reste dans les fichiers de ton projet et la recherche web ci-dessus.

## Langues

Les noms (skills, agents, fichiers) sont en anglais. La documentation et les gabarits sont en anglais et en français ; les documents générés suivent la langue du projet.
