# Utiliser Trellis

*English: [usage.md](usage.md)*

Trellis est un plugin [Claude Code](https://claude.com/claude-code). Il ajoute des skills (et, plus tard, des agents) qui guident un projet de l'idée brute jusqu'au logiciel livré. Cette page explique comment l'installer et comment travailler avec.

## 1. Installer

Il faut Claude Code. Puis installe le plugin depuis la marketplace de ce dépôt :

```
/plugin install trellis --marketplace https://git.vbmaf.net/sebastien/trellis.git
```

Tant que le dépôt est privé, l'URL HTTPS échoue (Git ne peut pas demander de mot de passe dans ce contexte). Utilise plutôt la forme SSH :

```
/plugin install trellis --marketplace git@git.vbmaf.net:sebastien/trellis.git
```

Écris-la sous cette forme `git@hôte:chemin`. Claude Code **refuse les URL `ssh://…`** (« Invalid marketplace source format »). Cette forme ne peut pas porter de port : un port SSH non standard doit donc être déclaré dans `~/.ssh/config` (ici : `Host git.vbmaf.net` avec `Port 2222`).

La même chose en deux étapes, utile à connaître pour les mises à jour :

```
/plugin marketplace add <URL du dépôt>
/plugin install trellis@trellis
/plugin marketplace update trellis
```

### Essayer sans installer

Clone le dépôt et pointe Claude Code dessus le temps d'une session :

```sh
git clone <URL du dépôt> trellis
claude --plugin-dir ./trellis
```

C'est aussi ainsi qu'on teste ses propres modifications en travaillant sur le plugin.

### Vérifier que ça marche

Lance `/help` ou tape `/trellis:` et cherche `trellis:incubate`. Si tu le vois, le plugin est chargé.

## 2. Le principe en une image

Chaque phase écrit un fichier dans **le dépôt de ton projet** ; la phase suivante le lit. Les fichiers, pas l'historique de conversation, sont la mémoire partagée. Tu peux t'arrêter un mois, revenir, et rien n'est perdu.

```
idée ──► INCUBATION ──► CONCEPTION ────────► DÉVELOPPEMENT ──────► VIE DU PROJET
         SPEC.md        ARCHITECTURE.md      PLAN.md               STATUS.md
                        docs/adr/*.md        (tranches livrables)  CHANGELOG.md
```

Trellis est **strict mais souple**. Si un prérequis manque (par exemple pas de spécification validée avant la conception), le skill dit ce qui manque et ce que cela risque, puis te laisse décider. Si tu continues quand même, la dérogation est écrite dans le journal des décisions du document : le choix reste visible plus tard.

## 3. Ce qui est disponible aujourd'hui

| Nom | Type | Phase | État |
| --- | --- | --- | --- |
| `incubate` | skill | incubation | **disponible** |
| `architect` | skill | conception | **disponible** |
| `adr` | skill | conception | **disponible** |
| `slice` | skill | développement | prévu |
| `deliver` | skill | développement | prévu |
| `resume` | skill | toutes | prévu |
| `devils-advocate` | agent | incubation, conception | **disponible** |
| `verifier` | agent | développement | prévu |

Cette page documente ce qui existe. Les éléments prévus sont décrits dans le [README](../README.fr.md) et auront leur propre section ici à leur sortie.

## 4. Incuber une idée (`incubate`)

### Démarrer

Ouvre Claude Code **dans le dossier où vivra le projet** (un dossier vide ou un dépôt existant), puis décris ton idée :

> J'aimerais construire un petit outil qui transforme mes commits en compte rendu d'activité mensuel. On peut l'incuber ?

ou appelle directement le skill :

```
/trellis:incubate un petit outil qui transforme mes commits en compte rendu d'activité mensuel
```

Les deux fonctionnent. L'appel direct est pratique si Claude ne reconnaît pas le skill de lui-même.

### Ce qui se passe

1. **Contexte.** Claude lit le dossier (`SPEC.md` existant, README, notes) avant de poser quoi que ce soit, pour ne pas redemander ce qui est déjà écrit. Si un `SPEC.md` existe, il le met à jour au lieu de repartir de zéro.
2. **Graine.** Il reformule ton idée en deux phrases. Corrige-la si elle est fausse.
3. **Entretien.** Une question à la fois, chacune avec une réponse suggérée. Réponds « ok » pour l'accepter, ou corrige. Les sujets, dans l'ordre :
   problème et utilisateur, pourquoi ceci et pourquoi maintenant, critères de succès, périmètre et **hors-périmètre**, contraintes, solutions existantes, hypothèse la plus risquée, test le moins cher, critères d'abandon.
4. **Challenge.** Avant d'écrire, il te dit ce qui l'inquiète : périmètre qui gonfle, critère de succès vague, hypothèse non testée, outil existant qui fait déjà l'essentiel.
5. **Brouillon.** Il écrit `SPEC.md` et un `STATUS.md` minimal.
6. **Verdict.** Il termine par une recommandation :

| Verdict | Signification |
| --- | --- |
| **Proceed** (continuer) | La spécification est solide ; la suite est la conception |
| **Test first** (tester d'abord) | L'hypothèse la plus risquée doit être vérifiée avant ; le test est décrit |
| **Park** (mettre en pause) | Pas maintenant ; ce qui rouvrirait le sujet est noté |
| **Abandon** (abandonner) | Ça ne vaut pas la peine d'être construit, avec la raison |

Abandonner ou mettre en pause est un résultat valable et utile : l'idée a été peu coûteuse à écarter.

### Ce que tu obtiens

- **`SPEC.md`** : résumé, problème, critères de succès, périmètre (dedans et dehors), exigences, contraintes, existant, risques et hypothèses, test le moins cher, critères d'abandon, questions ouvertes, journal des décisions.
- **`STATUS.md`** : la phase en cours, la prochaine action et l'état d'avancement.

Les exigences ont des identifiants stables (`R1`, `R2`, ...), une priorité (Must / Should / Could) et *la façon de les vérifier*. Les phases suivantes s'appuient sur ces identifiants ; évite donc de les renuméroter à la main.

### Conseils

- **Les réponses courtes suffisent.** « Ok », « oui » ou quelques mots : le skill propose, tu corriges.
- **Dis « je ne sais pas ».** Les inconnues vont dans les questions ouvertes ou les hypothèses au lieu d'être inventées.
- **La taille suit l'idée.** Un petit outil personnel donne un entretien court et une spécification d'une page.
- **Déroge librement.** Si le verdict est « tester d'abord » et que tu veux continuer, dis-le. C'est consigné, pas refusé.
- **Reviens plus tard.** Relance-le dans le même dossier pour réviser la spécification ; il fonctionne alors en mode mise à jour.

## 5. Concevoir l'architecture (`architect`)

Lance-le dans le même dossier, une fois `SPEC.md` en place :

```
/trellis:architect
```

ou demande simplement : « Quelle est la bonne architecture pour ça ? »

1. **Contexte.** Il lit `SPEC.md` et `STATUS.md`. Si la spécification manque ou n'est pas validée, il t'avertit du risque et propose d'incuber d'abord ; tu peux continuer quand même, et les hypothèses qu'il fait sont listées.
2. **Critères de décision.** À partir de tes exigences Must, il propose les trois à cinq qualités qui tranchent les arbitrages (vie privée, usage hors ligne, coût d'exploitation, maintenabilité par une seule personne...). Tu ajustes le classement.
3. **La liste des décisions.** Plateforme, langage, stockage, hébergement, authentification, tests, publication... Il sépare ce qui mérite un ADR (difficile à défaire) de ce qui tient en une ligne de note.
4. **Une décision à la fois.** Deux à quatre vraies options, comparées sur *tes* critères, avec une recommandation et ce dont il est le moins sûr. Si un fait clé est inconnu, il propose un **spike** (petite expérience bornée dans le temps) et consigne la décision comme `proposée` jusqu'à sa réalisation. Tu décides ; il consigne.
5. **`ARCHITECTURE.md`.** Contexte, critères, vue d'ensemble avec un diagramme Mermaid, composants, données, flux principaux, aspects transverses, un index des décisions et une **table de traçabilité** (exigence → composants → ADR).
6. **Contrôle de couverture.** Chaque Must doit aboutir à un composant ; chaque composant doit servir une exigence. Les écarts sont signalés sans détour.

Conseils : demande la valeur par défaut qu'il prendrait si tu hésites ; dis « c'est un outil de week-end » pour garder un résultat court ; relance-le plus tard pour réviser la conception (les ADR acceptés sont remplacés, jamais réécrits).

## 6. Consigner une décision (`adr`)

`architect` l'utilise automatiquement, mais tu peux l'appeler seul chaque fois que tu fais un choix technique :

```
/trellis:adr on a choisi SQLite plutôt que Postgres car l'app est mono-utilisateur et hors ligne
```

Il écrit `docs/adr/NNNN-titre-court.md` (numéroté, une décision par fichier) avec le contexte, **les options envisagées et pourquoi les autres sont écartées**, la décision, les conséquences (y compris négatives) et un déclencheur mesurable pour la réexaminer. Il tient aussi à jour `docs/adr/README.md` (l'index) et le tableau des décisions d'`ARCHITECTURE.md`.

Un ADR accepté n'est jamais modifié. Quand une décision change, demande un nouvel ADR qui **remplace** l'ancien : seule la ligne de statut de l'ancien change. Les ADR ne sont jamais supprimés ni renumérotés.

## 7. Demander un second avis (`devils-advocate`)

Un relecteur sceptique qui s'exécute comme agent séparé : son avis n'est donc pas ancré dans la conversation qui a produit le document. Il est en **lecture seule** : il rend un rapport, il ne modifie rien.

Demande-le avec des mots simples, par exemple :

> Utilise l'agent devils-advocate pour relire mon SPEC.md.

> Lance trellis:devils-advocate sur l'architecture et les ADR.

Il lit `SPEC.md`, `ARCHITECTURE.md`, `docs/adr/` et `STATUS.md`, et rend : un jugement d'ensemble, les problèmes **bloquants**, les **importants**, les mineurs, les deux ou trois questions auxquelles tu dois répondre, et ce qui est solide. Chaque constat cite le fichier et la section. Il s'adapte au projet (un outil de week-end n'est pas jugé selon des normes d'entreprise) et dit « aucun problème bloquant » quand c'est vrai.

Bons moments pour l'appeler : juste après `incubate` (avant d'investir), et après `architect` (avant de découper le travail).

## 8. Langues

Les noms (skills, agents, fichiers) sont en anglais. Les documents sont générés dans la langue de ton projet : celle dans laquelle tu écris, sauf si le projet a déjà des documents dans une autre. Les gabarits existent en anglais et en français (`skills/<nom>/templates/*.en.md` et `*.fr.md`).

## 9. Dépannage

| Symptôme | Cause probable et solution |
| --- | --- |
| `/trellis:incubate` est inconnu | Le plugin n'est pas chargé. Vérifie `/plugin`, ou lance Claude Code avec `--plugin-dir`. Redémarre la session après l'installation. |
| Claude n'utilise pas le skill quand tu décris une idée | Appelle-le explicitement avec `/trellis:incubate`. |
| Il demande ce que tu as déjà écrit | Mets tes notes dans le dossier du projet (README, notes) avant de commencer, pour qu'il les lise à l'étape 1. |
| L'entretien paraît trop long | Dis-le. Le skill dimensionne l'entretien selon l'idée et peut sauter les sujets déjà réglés. |
| `Invalid marketplace source format` | Tu as passé une URL `ssh://…`. Utilise `git@hôte:propriétaire/dépôt.git` (voir section 1). |
| `unable to get password from user` | Tu as utilisé l'URL HTTPS sur un dépôt privé. Utilise la forme SSH. |
| Une mise à jour n'est pas arrivée | Lance `/plugin marketplace update trellis`, puis redémarre la session. |

## 10. Contribuer et travailler sur le plugin

Les conventions du dépôt (organisation, nommage, documentation bilingue, « strict mais souple ») sont dans [`CLAUDE.md`](../CLAUDE.md). Pour vérifier une modification :

```sh
claude plugin validate .
```
