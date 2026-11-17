# Exemples

*English: [README.md](README.md)*

## `commit-report/` : les documents d'un petit projet, phase par phase

Un projet **fictif** (un outil en ligne de commande qui transforme un mois d'historique git en compte rendu d'activité), présenté tel que Trellis le laisserait en cours de développement. Les documents ont été **écrits à la main** pour montrer leur forme et la façon dont ils se renvoient les uns aux autres ; ce n'est pas la transcription d'un vrai run, et le projet n'existe pas. Rien ici ne contient de vrais produits, liens ou dates sur lesquels s'appuyer. Les documents sont en anglais.

À lire dans cet ordre, chaque fichier étant le résultat d'une phase :

| Phase | Skill | Fichier |
| --- | --- | --- |
| Incubation | `incubate` | [SPEC.md](commit-report/SPEC.md) |
| Incubation (facultatif) | `prior-art` | [docs/prior-art.md](commit-report/docs/prior-art.md) |
| Conception | `architect`, `adr` | [ARCHITECTURE.md](commit-report/ARCHITECTURE.md), [docs/adr/](commit-report/docs/adr/README.md) |
| Conception | `ux` (une ligne de commande est aussi une interface) | [DESIGN.md](commit-report/DESIGN.md) |
| Conception | `test-strategy` | [TESTING.md](commit-report/TESTING.md) |
| Développement | `slice` | [PLAN.md](commit-report/PLAN.md) |
| À tout moment | `deliver`, `resume` | [STATUS.md](commit-report/STATUS.md), [CHANGELOG.md](commit-report/CHANGELOG.md) |
| Développement (facultatif) | `autopilot --review`, `follow-up` | [autopilot-run-sample/](commit-report/autopilot-run-sample/README.md) |

À remarquer :

- **Les identifiants d'exigences** (`R1`...) traversent la spécification, la table de traçabilité de l'architecture, la matrice de tests et les critères d'acceptation du plan. Une exigence sans contrôle, ou une Must sans tranche, serait signalée.
- **L'hypothèse la plus risquée** (A1), son **test le moins cher** et les **critères d'arrêt** sont écrits avant tout code.
- **Chaque décision** a sa propre fiche, avec les options *non* retenues.
- **`STATUS.md`** se termine par une prochaine action, donc une pause ne coûte rien.

`naming` n'est pas montré : sa valeur tient aux vrais résultats de recherche, et des résultats inventés seraient trompeurs.
