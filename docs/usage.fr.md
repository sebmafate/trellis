# Utiliser Trellis

*English: [usage.md](usage.md)*

Trellis est un plugin [Claude Code](https://claude.com/claude-code). Il ajoute des skills (et, plus tard, des agents) qui guident un projet de l'idée brute jusqu'au logiciel livré. Cette page explique comment l'installer et comment travailler avec.

## 1. Installer

Il faut Claude Code. Les commandes ci-dessous sont des **commandes de terminal** (`claude plugin …`), qui fonctionnent partout où Claude Code est installé, y compris dans des hôtes comme T3 Code. Dans l'interface interactive de Claude Code, on peut taper la même chose en commande à barre oblique (`/plugin install …`, `/plugin marketplace update …`) ; là où cette commande n'existe pas, utilise le terminal. Installe le plugin depuis la marketplace de ce dépôt :

```
claude plugin install trellis --marketplace https://github.com/sebmafate/trellis.git
```

La forme abrégée `owner/repo` est acceptée aussi :

```
claude plugin install trellis --marketplace sebmafate/trellis
```

**Depuis un fork privé ou une copie auto-hébergée**, l'URL HTTPS échoue (Git ne peut pas demander de mot de passe dans ce contexte). Utilise plutôt la forme SSH `git@hôte:propriétaire/dépôt.git`, par exemple :

```
claude plugin install trellis --marketplace git@example.com:proprietaire/trellis.git
```

Claude Code **refuse les URL `ssh://…`** (« Invalid marketplace source format »). La forme `git@hôte:chemin` ne peut pas porter de port : un port SSH non standard doit donc être déclaré dans `~/.ssh/config` (`Host example.com` avec `Port 2222`, par exemple).

La même chose en deux étapes, utile à connaître pour les mises à jour :

```
claude plugin marketplace add <URL du dépôt>
claude plugin install trellis@trellis
claude plugin marketplace update trellis
claude plugin update trellis@trellis
claude plugin list
```

### Essayer sans installer

Clone le dépôt et pointe Claude Code dessus le temps d'une session :

```sh
git clone <URL du dépôt> trellis
claude --plugin-dir ./trellis
```

C'est aussi ainsi qu'on teste ses propres modifications en travaillant sur le plugin.

### Vérifier que ça marche

Lance `claude plugin list` dans un terminal : `trellis@trellis` doit apparaître comme activé. Les plugins sont chargés au démarrage d'une session : **démarre une nouvelle session** (un nouveau fil dans T3 Code) après l'installation ou une mise à jour. Puis tape `/trellis:` là où les commandes à barre oblique existent et cherche `trellis:incubate`, ou demande simplement à Claude d'incuber une idée.

## 2. Le principe en une image

Chaque phase écrit un fichier dans **le dépôt de ton projet** ; la phase suivante le lit. Les fichiers, pas l'historique de conversation, sont la mémoire partagée. Tu peux t'arrêter un mois, revenir, et rien n'est perdu.

```
idée ──► INCUBATION ──► CONCEPTION ───────────────► DÉVELOPPEMENT ──────► VIE DU PROJET
         SPEC.md        ARCHITECTURE.md + ADR     PLAN.md               STATUS.md
                        DESIGN.md (UX, UI)        (tranches livrables)  CHANGELOG.md
                        TESTING.md
```

Trellis est **strict mais souple**. Si un prérequis manque (par exemple pas de spécification validée avant la conception), le skill dit ce qui manque et ce que cela risque, puis te laisse décider. Si tu continues quand même, la dérogation est écrite dans le journal des décisions du document : le choix reste visible plus tard.

## 3. Ce qui est disponible aujourd'hui

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

Chaque élément a sa propre section ci-dessous, suivie d'un ordre d'utilisation conseillé.

### Vous avez déjà un projet ?

Trellis accompagne un projet depuis son idée, mais vous pouvez faire adopter un **projet existant et avancé** par le skill `adopt` (voir la section 26). Il reconstitue les documents à partir du code, des tests, de la CI et de l'historique git, marque tout **« reconstitué, à confirmer »**, et ne touche jamais à votre code. Le reste (`slice`, `deliver`, `autopilot`, `change-course`...) peut ensuite travailler dessus.

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
- **Produits existants.** Quand le projet est destiné à **d'autres personnes**, `incubate` propose par défaut l'étude `prior-art` (avec son angle marché) et le consigne si tu la sautes. Pour un outil strictement personnel, une recherche rapide suffit.
- **Un nom.** Un nom de travail suffit. `incubate` demande une fois si tu veux de l'aide pour en trouver un vrai, et propose le skill `naming` à la fin (voir ci-dessous).

### Trouver un nom (`naming`)

```
/trellis:naming
```

Facultatif : il demande d'abord si tu veux de l'aide, et s'arrête si ce n'est pas le cas. Il pose un court cahier des charges (ce que le nom doit évoquer et ne pas suggérer, qui le dira et le lira, les langues, où il doit être disponible, où vivra le projet), propose une douzaine de candidats dans des pistes différentes (métaphore, mot inventé, évocateur, autres langues) avec la raison et le risque connu de chacun, et t'aide à retenir trois à cinq finalistes. Il **passe ensuite les finalistes au crible sur le web** et note où et quand : noms identiques ou proches dans le domaine, domaines, noms de dépôts et de paquets, boutiques d'applications, sens dans d'autres langues, et les registres publics de marques qu'il a réellement pu interroger. Le verdict est « aucun conflit trouvé dans les endroits vérifiés », « conflit proche » ou « bloquant », jamais « libre ». Écrit dans `docs/naming.md` ; le nom choisi entre dans le titre de la spécification et son journal des décisions. Tu peux aussi lui demander de vérifier un nom que tu as déjà. C'est un premier criblage, pas une recherche de marque ni un avis juridique : pour un projet commercial, fais consulter les registres par un professionnel.

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

## 8. Concevoir l'expérience utilisateur (`ux`)

Pour tout projet avec une interface. Lance-le après `incubate`, avant ou en parallèle d'`architect` :

```
/trellis:ux
```

Il lit `SPEC.md` et traite : les utilisateurs et leur contexte, des **objectifs UX testables**, les **parcours** (un par exigence Must, dont la première utilisation), la structure de l'information, l'**inventaire des écrans avec tous leurs états** (vide, chargement, erreur, hors ligne, première utilisation...), des maquettes filaires en ASCII pour les écrans critiques, et les textes clés de l'interface. Tout cite des identifiants d'exigences, et il se termine par un parcours à blanc de chaque parcours et un contrôle de couverture (chaque Must visible par l'utilisateur a un écran ; chaque écran sert une exigence).

Le résultat est la **partie UX de `DESIGN.md`**. Il ne dit rien des couleurs ni des frameworks. Pour un outil en ligne de commande ou une API, il traite les commandes, la sortie et les messages d'erreur comme l'interface. Un projet sans aucune interface est averti et ignoré.

## 9. Définir le système visuel (`ui`)

Lance-le quand la partie UX existe et que la plateforme est décidée :

```
/trellis:ui
```

Il nomme les conventions de plateforme à suivre, propose un **petit ensemble de jetons sémantiques** (couleur avec valeurs claire et sombre, typographie, espacements, formes, mouvement) avec les ratios de contraste calculés et consignés, liste les **composants avec leurs états** (en préférant les composants natifs et en justifiant ceux sur mesure), et écrit les règles d'accessibilité (taille des cibles, mise à l'échelle du texte, étiquettes pour lecteur d'écran, ordre du focus, réduction des animations). Le résultat est la **partie UI de `DESIGN.md`**.

Si la plateforme n'est pas décidée, il le signale et propose de produire des jetons indépendants de la plateforme.

## 10. Planifier les tests (`test-strategy`)

Lance-le après l'architecture (et le design, s'il y a une interface), avant le découpage :

```
/trellis:test-strategy
```

Il identifie les risques qui comptent, choisit un dosage proportionné de niveaux de test et construit une **matrice de vérification** : chaque exigence reçoit une vérification concrète et observable à un niveau donné. Il couvre aussi les données de test (pas de données personnelles dans le dépôt), les environnements, les **portes de qualité** (ce qui bloque, ce qui avertit seulement), les tests non fonctionnels et manuels, et une **définition de « fini »** pour chaque tranche. Le résultat est `TESTING.md`.

Si quelque chose est difficile à tester à cause de la façon dont le système est construit, il le remonte comme un constat à rapporter à `architect`. Il n'écrit pas les tests ; cela se fait pendant la livraison.

## 11. Obtenir une critique du design (`design-reviewer`)

L'équivalent design de `devils-advocate` : un agent indépendant en **lecture seule**.

> Utilise l'agent design-reviewer sur mon DESIGN.md.

Il vérifie les parcours par rapport aux exigences, les états manquants, le nombre d'étapes, les messages d'erreur, la cohérence du vocabulaire et, pour la partie UI, l'adéquation à la plateforme, les jetons, le contraste, l'accessibilité et les composants. Il rend des constats bloquants, importants et mineurs avec fichier et section, et dit « aucun problème bloquant » quand c'est vrai. Il ne peut pas lancer l'app : les ratios de contraste qu'il n'a pas vérifiés sont signalés « annoncés, non vérifiés ».

## 12. Passer en revue l'existant (`prior-art`)

```
/trellis:prior-art
```

Une version plus poussée et sourcée du survol fait par `incubate`, proposée par défaut quand le projet est destiné à d'autres personnes. Vous convenez de cinq à huit **critères tirés de vos exigences**, puis il examine produits, projets open source, fonctions de plateformes et combinaisons d'outils, évalue chacun selon les critères (avec liens et date de vérification, et affirmations des éditeurs signalées comme telles), et conclut par un verdict : **adopter, adapter, construire ou ne rien faire**. Écrit dans `docs/prior-art.md`. « Adopte plutôt X » est un résultat valable.

Pour un projet destiné à d'autres personnes, il ajoute un court **angle marché** : comment les candidats se positionnent et fixent leurs prix, comment les utilisateurs les trouvent et les choisissent, les signes de demande et ce qui différencierait votre projet. Ce n'est pas une étude de marché : savoir si des gens paieraient ou changeraient devient l'hypothèse la plus risquée et un test peu coûteux.

## 13. Lever une inconnue (`spike`)

```
/trellis:spike une extension de partage peut-elle écrire dans le stockage partagé en moins de 200 ms ?
```

Cadre **une seule question**, avec des critères de succès, d'échec et de non-conclusion écrits **avant** l'expérience, et une durée maximale. Il aide à concevoir l'expérience la moins chère, à l'exécuter et à consigner le résultat dans `docs/spikes/NNNN-titre.md`, puis reporte le verdict dans l'ADR (une décision `proposée` devient `acceptée`, ou est remplacée), la table des risques de l'architecture, les critères d'abandon de la spécification et le plan. Le code d'un spike est jetable.

## 14. Mettre en place le projet (`bootstrap`)

```
/trellis:bootstrap
```

À lancer après l'architecture et la stratégie de tests. Il reprend la stack de vos ADR, montre son plan, puis utilise le **générateur officiel** de la stack pour créer la structure, ajoute `docs/`, `.gitignore`, un README minimal et `CHANGELOG.md`, et écrit le **`CLAUDE.md` du projet** (où est chaque document, les vraies commandes de build et de test, les règles). Il se termine par un **squelette fonctionnel** qui compile et passe un test. Il n'écrase jamais de fichier existant, et créer un dépôt distant, pousser ou activer la CI attend votre accord.

## 15. Planifier le travail (`slice`)

```
/trellis:slice
```

Découpe le travail dans `PLAN.md` : des **tranches verticales** (`SL0`, `SL1`...), chacune observable, testable et petite, avec des critères d'acceptation tirés de la matrice de vérification, les exigences, parcours et écrans qu'elle sert, une taille (S / M / L) et ses dépendances. La tranche 0 est le squelette fonctionnel ; les points risqués viennent tôt ; chaque Must est couvert par une tranche (un tableau de couverture le vérifie) ; les états d'erreur et de vide sont planifiés aussi. Les tranches lointaines restent grossières. Le relancer après un changement conserve les identifiants des tranches faites.

## 16. Construire des tranches (`deliver` ou `autopilot`)

```
/trellis:deliver SL1
```

ou « livre la prochaine tranche ». Pour une tranche : il reformule les critères, écrit les **tests d'abord** et les voit échouer, implémente le minimum pour qu'ils passent, lance tests, lint et build (et essaie réellement ce que voit l'utilisateur), propose une vérification indépendante par le `verifier`, puis met à jour `PLAN.md`, `STATUS.md`, `CHANGELOG.md` et la matrice de tests, et commite si vous l'avez accepté. Il signale les écarts au lieu de les cacher, n'affaiblit jamais un test pour le faire passer, et s'arrête après chaque tranche. Il demande une fois si chaque tranche doit être commitée sur sa propre branche.

### Ou laisser une équipe le faire : `autopilot`

```
/trellis:autopilot next --push
/trellis:autopilot SL3 SL4 --parallel 2 --rounds 3 --push
```

`autopilot` fait travailler une petite **équipe d'agents** qui mène des tranches du plan jusqu'à une **pull request ouverte**, une PR par tranche. Il s'exécute dans votre session (lui seul peut vous parler) ; les agents font le travail :

```
product-owner ─► task-writer ─► developer ◄──► QA (verifier) ─► push + une PR par tranche
       │              │             │               │
       └─────── décision à prendre ? ┴───────────────┘   il vous demande, puis reprend
```

| Rôle | Agent | Ce qu'il fait |
| --- | --- | --- |
| Product owner | `product-owner` | Propose la ou les prochaines tranches de `PLAN.md` : état de préparation, priorité, risque, lesquelles peuvent tourner en parallèle, et sur quelle branche de base. Vous confirmez. |
| Rédacteur de tâches | `task-writer` | Décrit la tranche de façon **atomique** : petites tâches ordonnées, chacune avec son résultat attendu observable, le test à écrire d'abord, les fichiers probables et ce qui est hors périmètre. Dit ce qui manque s'il n'y arrive pas. |
| Développeur | `developer` | Code et teste tâche par tâche sur une **branche dédiée dans un worktree dédié**, un commit par tâche, documents mis à jour. |
| QA | `verifier` | Contrôle de façon indépendante par rapport à la consigne et aux critères. S'il rejette, ses constats numérotés repartent chez le **même développeur**, qui corrige, et la QA retest, jusqu'à `--rounds` tours (3 par défaut). |
| Relecteur (facultatif, `--review`) | `pr-reviewer` | Relit le vrai diff comme le ferait un relecteur humain, avant la poussée : bugs, lisibilité, cohérence, risques, périmètre, exactitude de la description de la PR. Voir « Relire la pull request » ci-dessous. |

**Décisions.** Les agents ne peuvent pas vous poser de questions. Quand l'un d'eux a besoin d'une décision, il s'arrête et rend une demande structurée (question, options, recommandation, caractère architectural ou non). `autopilot` vous la pose, consigne un ADR dans la branche de la tranche si la décision est architecturale, puis **reprend le même agent** avec votre réponse. Les autres tranches indépendantes continuent pendant ce temps. Dans une session non interactive, il sauvegarde les questions dans le `state.md` du run et s'arrête, sans rien construire ni rien pousser ; relancez-le avec vos réponses dans le message (par exemple « Q1 = a, Q2 = a, et oui vous pouvez réécrire le critère de SL3 ») pour reprendre. Un critère vague dans le plan est traité de la même façon : il demande l'autorisation de le remplacer par des exemples concrets, dans la branche de la tranche.

**Le résultat.** Pour chaque tranche qui passe la QA, il fait lui-même un contrôle final (tests, arbre propre, seulement les fichiers de la tranche, pas de secret évident), pousse la branche et ouvre **une pull request** avec une description générée (résumé, tâches, critères avec preuves, verdict QA, décisions, écarts, comment fusionner). Il utilise `fj` pour un hôte Forgejo ou Gitea et `gh` pour GitHub. **Il ne fusionne jamais, ne force jamais de poussée et ne pousse jamais sur votre branche par défaut.** Pousser et ouvrir des pull requests demandent l'autorisation donnée au départ (`--push` ou un oui) ; sans elle, vous n'obtenez que des branches locales.

| Option | Signification | Par défaut |
| --- | --- | --- |
| `SL3 SL4` ou `next` | Tranches à traiter ; `next` laisse le product owner choisir | `next` |
| `--push` | Autorise à pousser les branches et à ouvrir des pull requests | demandé |
| `--parallel N` | Tranches menées en même temps | 1 |
| `--rounds N` | Tours de QA par tranche avant de vous solliciter | 3 |
| `--base BRANCHE` | Branche de base des tranches qui ne s'empilent pas | branche par défaut |
| `--draft` | Ouvre les pull requests en brouillon | non |
| `--review` | Ajoute une relecture par l'agent `pr-reviewer` après la QA et avant la poussée | désactivée |
| `--review-rounds N` | Tours de correction après une relecture qui demande des modifications (`0` = rapport seulement) | 1 |
| `--review-comment` | Poste chaque relecture en un commentaire sur sa pull request, sans redemander | non (demandé une fois les PR ouvertes) |
| `--dev-model MODÈLE` | Modèle du `developer` (`sonnet`, `opus` ou `haiku`) | celui de l'agent (`sonnet`) |
| `--review-model MODÈLE` | Modèle des juges, `verifier` et `pr-reviewer` | celui des agents (`opus`) |

**Exemples**

```
# En local seulement : voir ce que produit l'équipe ; rien ne quitte votre machine
/trellis:autopilot SL2

# Construire la prochaine tranche prête, la pousser et ouvrir une pull request
/trellis:autopilot next --push

# Deux tranches indépendantes en même temps, en brouillon, avec une relecture dont le commentaire est posté pour vous
/trellis:autopilot SL3 SL4 --parallel 2 --draft --push --review --review-comment

# Une tranche délicate : un développeur plus fort, et pas plus de 2 tours de QA
/trellis:autopilot SL5 --dev-model opus --rounds 2 --push

# Relecture seule, sans boucle : vous avez le rapport, rien n'est corrigé dans votre dos
/trellis:autopilot SL2 --push --review --review-rounds 0
```

Vous pouvez aussi simplement le dire : « fais tourner l'équipe sur la prochaine tranche, pousse-la et fais relire la pull request ».

#### Quel modèle pour quel agent ?

Chaque agent déclare son modèle dans la ligne `model:` de son en-tête, dans `agents/<nom>.md`. Les valeurs par défaut suivent une idée : **un modèle plus fort là où le travail consiste à juger ou à spécifier, un modèle moins coûteux là où il consiste à exécuter ou à lire de façon mécanique.**

| Agent | Modèle | Pourquoi |
| --- | --- | --- |
| `developer` | `sonnet` | Il écrit et lance le plus de choses : c'est le plus gros poste de coût. |
| `verifier`, `pr-reviewer` | `opus` | Ce sont des juges : un juge plus fort que l'auteur attrape plus de défauts. Un modèle différent apporte aussi un peu plus d'indépendance vis-à-vis de l'auteur du code. |
| `task-writer` | `opus` | Sa consigne conditionne tout ce qui suit. |
| `devils-advocate`, `security-reviewer`, `design-reviewer` | `opus` | Jugement et esprit critique, appelés rarement. |
| `product-owner`, `coherence-checker` | `sonnet` | Lecture et mise en relation, plutôt mécaniques. |

`haiku` n'est pas utilisé par défaut : le gain est faible face au risque de rater un constat. Pour changer pour un seul run, utilisez `--dev-model` et `--review-model` ; pour changer durablement, modifiez la ligne `model:` (`sonnet`, `opus`, `haiku`, ou `inherit` pour utiliser le modèle de votre session). Les alias pointent toujours vers la dernière version de chaque famille. Si votre abonnement n'inclut pas un modèle, `autopilot` retombe sur celui de l'agent et le note dans l'état du run ; pour un agent utilisé seul, mettez `model: inherit`. Le coût n'est pas mesuré ici : passer le développeur sur `sonnet` ne le réduit que si votre session ne tourne pas déjà sur un modèle moins cher.

#### Relire la pull request (`--review`)

```
/trellis:autopilot next --push --review
/trellis:autopilot SL3 SL4 --push --review --review-comment
```

La QA répond à « la tranche respecte-t-elle ses critères ? ». La relecture répond à l'autre question, celle qu'un collègue attentif se pose avant de fusionner : « serais-je à l'aise pour maintenir ce code ? ». Avec `--review`, quand une tranche a passé la QA et le contrôle final, l'agent `pr-reviewer` lit le **vrai diff** par rapport à la branche de base et signale les bugs et les cas non gérés, le code peu clair ou inutilement complexe, les doublons, les écarts avec les conventions du code et les ADR, les changements risqués (sécurité, dépendances, migrations), le périmètre qui déborde, et si la description de la pull request dit bien ce que fait le diff. Il **ne reçoit ni le rapport du développeur ni celui de la QA** : il se forge son propre avis. Il rend un rapport ; il ne modifie rien, n'approuve rien et ne poste rien.

- **Avant la poussée.** La relecture a lieu avant que la branche soit poussée : les corrections arrivent dans la même pull request, et un run sans `--push` est relu aussi.
- **Verdict.** `approve`, `comments` (remarques, rien de bloquant) ou `changes-requested` (au moins un constat bloquant). Le verdict est consultatif ; rien n'est bloqué sur la forge.
- **Boucle de correction.** Sur `changes-requested`, les constats numérotés repartent chez le même développeur, la QA retest et le relecteur regarde de nouveau, pendant `--review-rounds` tours (1 par défaut ; `0` = rapport seulement). S'il reste des constats bloquants, `autopilot` vous demande : un tour de plus, reprendre avec `deliver`, abandonner la tranche, ou la pousser en brouillon avec les constats listés. Les remarques sont résumées dans une section « Relecture automatique » de la description de la pull request.
- **Poster un commentaire est une étape à part, qui demande votre accord.** Par défaut, **rien n'est posté** : une fois les pull requests ouvertes, on vous montre chaque relecture (verdict et texte complet) et on vous demande lesquelles poster, en **un simple commentaire** par pull request (jamais via « approuver » ou « demander des modifications » de la forge). Avec `--review-comment`, ou un oui au départ, chaque relecture est postée sans autre question. En session non interactive, rien n'est posté ; les fichiers et les commandes exactes sont dans le rapport final. Le commentaire commence par une ligne qui précise qu'il s'agit d'une relecture automatique et consultative. Poster demande une pull request ouverte, donc aussi l'autorisation de pousser.
- **Fichiers.** `.trellis/runs/<run>/<ID>/review-N.md` (rapport complet) et `review-comment.md` (le texte qui serait posté).

C'est un passage d'agent de plus par tranche (davantage avec des tours de correction), et il utilise le même modèle que le reste de l'équipe : il complète une relecture humaine et ne la remplace pas.

**Tranches empilées.** Une tranche qui dépend d'une autre pas encore fusionnée est construite sur **la branche de cette tranche** et sa pull request la cible : vous obtenez une chaîne de PR à fusionner **dans l'ordre**. Les tranches qui touchent les mêmes fichiers ou composants ne tournent pas en parallèle.

**Fichiers conservés.** L'état du run, les consignes et les rapports vivent dans `.trellis/runs/<run>/` et les worktrees dans `.trellis/worktrees/`. Tout le dossier `.trellis/` est exclu de git en local (autopilot l'ajoute à `.git/info/exclude`, ce qui peut demander votre permission ; `bootstrap` le met dans `.gitignore`). Un run interrompu peut reprendre depuis son `state.md`.

Vous pouvez aussi utiliser les agents seuls : demandez au `product-owner` « que construire ensuite ? », au `task-writer` de préparer une tranche avant de la construire à la main, au `verifier` de contrôler n'importe quel travail, ou au `pr-reviewer` de relire n'importe quelle branche ou pull request.

### `deliver` ou `autopilot` ?

| | `deliver` (skill) | `autopilot` (skill qui fait travailler une équipe) |
| --- | --- | --- |
| Qui travaille | Claude dans votre conversation | Une équipe d'agents : product owner, rédacteur de tâches, développeur, QA |
| Supervision | Pas à pas ; vous voyez chaque test et chaque modification et pouvez piloter | Sans surveillance entre vos décisions ; un rapport et des pull requests à la fin |
| Questions | Vous les pose au fil de l'eau | Rassemble les décisions, vous les pose, puis reprend l'agent |
| Contrôle qualité | `verifier` facultatif à la fin | Intégré : une boucle QA (jusqu'à `--rounds`) avant toute poussée, plus une relecture de la pull request facultative (`--review`) |
| Votre copie de travail | Il y travaille (sur une branche de tranche si vous êtes d'accord) | Il n'y touche jamais ; worktrees et branches à lui |
| Résultat | Un commit, si vous êtes d'accord | Une branche poussée et **une pull request par tranche** (avec autorisation) |
| Travail en parallèle | Une tranche à la fois | Plusieurs tranches indépendantes ; PR empilées pour les dépendantes |
| Contexte | Connaît toute la conversation | Chaque agent ne connaît que la consigne et les fichiers du projet |
| Coût | Une conversation | Plusieurs agents par tranche et plusieurs tours ; le parallèle le multiplie |

**Utilisez `deliver` quand**

- c'est l'une des premières tranches, le squelette fonctionnel, ou tout ce qui fixe un modèle que la suite reprendra ;
- la tranche est incertaine, touche l'architecture, ou demande du jugement visuel et d'interaction ;
- les critères ne sont pas encore nets, ou vous prévoyez de prendre des décisions en route ;
- vous voulez voir le code prendre forme et le corriger au fur et à mesure.

**Utilisez `autopilot` quand**

- le plan est solide : critères d'acceptation observables, aucune décision ouverte sur le chemin ;
- la tranche suit un modèle déjà fixé par les précédentes ;
- vous préférez des pull requests à relire plutôt qu'une conversation à suivre ;
- plusieurs tranches sont indépendantes et vous les voulez en parallèle, ou vous voulez vous absenter pendant ce temps ;
- tout ce qu'il lui faut peut se faire sans vous : pas de secrets, pas d'appareil, pas d'étape manuelle.

**Évitez `autopilot` pour** le squelette fonctionnel (utilisez `bootstrap` et `deliver`), tout ce qui touche à la signature, aux identifiants ou à un appareil physique, et les tranches qui demandent une décision à laquelle vous n'avez pas réfléchi.

**À savoir sur `autopilot`**

- Chaque agent travaille uniquement à partir de la consigne et des fichiers du projet : meilleurs sont `PLAN.md`, `DESIGN.md` et `TESTING.md`, meilleur est le résultat. Un plan faible produit des tranches bloquées et des demandes de décision.
- Il coûte plus cher que `deliver` : comptez plusieurs agents par tranche, davantage avec des tours de QA.
- Les tranches parallèles modifient les mêmes fichiers de suivi (`PLAN.md`, `STATUS.md`, `CHANGELOG.md`) ; attendez-vous à de petits conflits de fusion, résolus en gardant les deux côtés.
- **Forges prises en charge : GitHub (`gh`), Forgejo ou Gitea (`fj`) et, **à titre expérimental**, GitLab (`glab`).** Tout le reste de Trellis n'est que du git et du Markdown et fonctionne sur n'importe quel hôte. Le support de GitLab a été écrit d'après la documentation de `glab` et n'a pas encore été exécuté contre un vrai GitLab : essayez-le d'abord sur un projet jetable, et dites ce qui casse. Les merge requests sont ouvertes avec `glab mr create`, les commentaires postés avec `glab mr note create` ; `follow-up` lit les discussions et les journaux des jobs en échec via `glab api`. Il faut `glab` installé et authentifié (`glab auth login`, puis `glab auth status`). Sur toute autre forge (Bitbucket…), `autopilot` construit, vérifie et pousse quand même les branches mais s'arrête avant la pull request : il vous donne les noms de branche et la description prête à coller (`.trellis/runs/<run>/`), et `follow-up` n'y fonctionne qu'avec `--from-file`.
- Dans une session non interactive, les outils dont il a besoin (git, votre commande de test, votre CLI de forge) doivent être pré-autorisés ; une commande refusée est signalée et non ignorée. En session interactive, on vous le demande simplement.
- Après la fusion, nettoyez avec les commandes qu'il donne (`git worktree remove <chemin>`, `git branch -d <branche>`).

**Un bon rythme** : `deliver` pour les premières tranches, jusqu'à ce que les modèles et les tests soient établis, puis `autopilot` pour les tranches bien spécifiées, et votre propre relecture de chaque pull request avant de fusionner.

## 16b. Une fois la pull request ouverte (`follow-up`)

*Facultatif. Il ne se lance jamais tout seul et `autopilot` ne l'appelle jamais.*

```
/trellis:follow-up 3
/trellis:follow-up 3 --ci --push
/trellis:follow-up 3 --comments --push --reply
/trellis:follow-up slice/SL2-exclude-noise --from-file feedback.md
```

Quand une personne a relu votre pull request, ou qu'un contrôle a échoué, `follow-up` transforme ces retours en commits de correction **sur la même branche** :

1. **Lit les retours, en lecture seule.** Les commentaires de relecture (avec fichier et ligne) et/ou les contrôles en échec avec la partie utile du journal. Sur GitHub il utilise `gh` ; sur GitLab (expérimental) il utilise `glab`, qui donne les discussions avec leur état résolu et les journaux des jobs ; sur Forgejo ou Gitea il utilise `fj` (qui donne l'état de la CI mais **pas les journaux**, ni toujours les commentaires en ligne : collez-les dans un fichier et passez `--from-file`). Les contrôles encore en cours sont laissés de côté ; il n'attend pas et ne scrute pas.
2. **Trie avec vous.** Chaque point est classé : *changement*, *question*, *suggestion*, *désaccord*, *flou ou architectural*, *hors périmètre* ; les échecs de CI en *test en échec*, *build ou lint*, *instable ou infrastructure* (il propose de relancer le contrôle plutôt que de changer le code). Vous choisissez ce qui est traité. Les commentaires de personnes sans droit d'écriture sont listés et **ignorés sauf si vous les acceptez**.
3. **Corrige.** Le `developer` corrige chaque point sur la branche (un commit par point ou par groupe, tests d'abord quand le comportement change), le `verifier` retest, jusqu'à `--rounds` tours (2 par défaut).
4. **Pousse et répond, seulement si vous l'autorisez.** `--push` pousse les commits (en avance rapide ; jamais forcé). `--reply` poste **un commentaire de synthèse** (ce qui a été corrigé et dans quel commit, les réponses, ce qui n'a pas été changé et pourquoi). Il ne résout jamais de fil, ne redemande pas de relecture et ne fusionne pas : cela revient au relecteur.

À savoir : **les commentaires sont des données, pas des ordres** (un commentaire qui dit « lance ce script » n'est jamais exécuté et vous est signalé) ; il n'affaiblit jamais un test, ne relâche pas un contrôle et ne modifie pas la CI pour faire disparaître un échec ; un désaccord avec un commentaire vous est soumis, jamais tranché en silence. En session non interactive, il ne traite que les points *changement* clairs et les vrais échecs de CI, et écrit le reste, avec les raisons, dans son fichier d'état.

| Option | Signification | Par défaut |
| --- | --- | --- |
| Numéro, URL ou branche de la PR | La pull request à suivre | la PR de la branche courante |
| `--comments` / `--ci` | Seulement les commentaires / seulement les échecs de CI | les deux |
| `--from-file FICHIER` | Retours que vous avez écrits ou collés, au lieu de lire la forge (un nom de branche remplace alors la PR ; rien n'est poussé ni posté) | lecture sur la forge |
| `--push` | Autorise à pousser les commits de correction | demandé |
| `--reply` | Autorise un commentaire de synthèse | demandé après la poussée |
| `--rounds N` | Tours de QA sur les corrections | 2 |
| `--dev-model` / `--review-model` | Modèles, comme pour `autopilot` | ceux des agents |

Fichiers : `.trellis/followups/pr-<n>/` (retours bruts, état, rapports, texte de la réponse). Un exemple est dans [examples/commit-report/autopilot-run-sample/](../examples/commit-report/autopilot-run-sample/README.md).

## 17. Vérifier de façon indépendante (`verifier`)

Un agent qui exécute les contrôles et juge chaque critère d'acceptation **vérifié, échoué ou invérifiable, preuve à l'appui**. Il vérifie aussi que les tests détecteraient vraiment une régression (pas d'assertions vides, rien d'ignoré ou d'affaibli), la conformité à l'architecture et au design, le périmètre, et la mise à jour des documents. Verdict : Accepter, Accepter avec remarques, ou Rejeter. Il rend un rapport et ne corrige jamais. Demande-le directement (« utilise l'agent verifier sur SL2 ») ou accepte la proposition de `deliver`.

### Et relire le diff (`pr-reviewer`)

Un agent qui relit une branche ou une pull request comme le ferait un relecteur humain : il lit le vrai diff et signale les bugs et les cas non gérés, la lisibilité et les doublons, les écarts avec les conventions du code et les ADR, les risques (sécurité, dépendances, changements incompatibles, migrations), le périmètre qui déborde et une description qui ne correspond pas au diff. Les constats sont classés **Bloquant / Important / Suggestions** avec `fichier:ligne`, et il termine par un verdict (`approve`, `comments`, `changes-requested`) et un commentaire prêt à poster. Il ne rejoue pas les critères d'acceptation (c'est le travail du `verifier`) et ne modifie, ne poste et n'approuve rien. Demandez-le directement (« utilise l'agent pr-reviewer sur cette branche par rapport à main ») ou laissez `autopilot --review` le lancer.

## 18. Publier (`publish`)

```
/trellis:publish
```

Contrôles avant publication (arbre propre, tranches prévues faites, tests complets, portes de publication, changelog), une version proposée, des notes de version, une construction depuis le commit de publication (dont **signature et notarisation Apple**, magasins d'applications, paquets ou conteneurs selon vos ADR), une **vérification de l'artefact construit dans un environnement propre**, puis la publication et un plan de retour arrière. Chaque étape visible de l'extérieur (étiquette, envoi, soumission à un magasin, déploiement, annonce) demande votre feu vert explicite.

## 19. Reprendre un projet (`resume`)

```
/trellis:resume
```

Lit `STATUS.md`, `PLAN.md` et git, et répond en un écran : la phase, ce qui est fait, ce qui est en cours (branche, travail non commité), ce qui est ouvert ou périmé, et **une prochaine action** avec la commande exacte. Il signale quand le document de statut et le dépôt ne s'accordent pas. Lecture seule ; il propose de rafraîchir `STATUS.md` et ne le fait que si vous êtes d'accord.

## 20. Changer de cap (`change-course`)

```
/trellis:change-course abandonner la synchronisation
```

Pour une exigence nouvelle, modifiée ou abandonnée, un pivot ou un changement de technologie. Il montre l'**impact** sur la spécification, les ADR, l'architecture, le design, les tests, le plan et le code, mesure le changement et le coût de ne pas changer, vérifie si un critère d'abandon est touché, et avance sur votre décision. Il met ensuite les documents à jour **dans l'ordre des dépendances** sans rien renuméroter : les exigences abandonnées sont marquées `dropped`, les décisions changées deviennent de nouveaux ADR qui remplacent les anciens.

## 21. Faire le point (`retro`)

```
/trellis:retro
```

Pour la fin d'une phase, d'un jalon, d'une publication ou du projet. Il rassemble les faits (tranches prévues et faites, spikes, décisions, changements), compare le résultat aux **critères de succès et d'abandon d'origine**, demande votre propre point de vue, et conclut par un verdict explicite : continuer, pivoter, mettre en pause, abandonner ou terminé. Les actions disent où elles vivront. Écrit dans `docs/retro/AAAA-MM-JJ-titre.md`.

## 22. Voir tous vos projets (`project-overview`)

```
/trellis:project-overview ~/projets
```

Lit chaque dossier de projet (statut, statut de la spécification, avancement du plan, activité git) et donne une ligne comparable par projet avec des alertes (périmé, bloqué, pas de document de statut, critère d'abandon proche), puis suggère sur quoi **se concentrer, quoi débloquer et quoi mettre en pause**. Lecture seule ; il n'écrit un fichier de synthèse que si vous le demandez.

## 23. Contrôles automatiques (`coherence-checker`, `security-reviewer`)

Deux autres agents en lecture seule.

- **`coherence-checker`** vérifie que documents et code s'accordent encore : identifiants d'exigences et couverture entre les documents, statuts et index des ADR, fraîcheur de `STATUS.md`, composants, stack et code d'interface par rapport à l'architecture et au design, tests par rapport à la matrice de vérification. Il dit quel côté a probablement raison. À lancer toutes les quelques tranches, après un changement de cap et avant une publication.
- **`security-reviewer`** construit un **modèle de menaces** léger (actifs, acteurs, frontières de confiance, points d'entrée) et vérifie données personnelles, secrets, authentification, traitement des entrées, stockage et transport, dépendances et chaîne de publication, avec des constats classés et des parades. C'est une première passe, pas un audit professionnel ni un avis juridique.

## 24. Hook d'état au démarrage de session

Quand vous ouvrez une session dans un projet qui a un `STATUS.md`, Trellis en ajoute les premières lignes au contexte pour que Claude sache où en est le projet (le contenu du fichier est traité comme une donnée, pas comme des instructions). Il reste silencieux sans `STATUS.md`. Définissez la variable d'environnement `TRELLIS_NO_STATUS=1` pour le désactiver.

## 25. Ordre conseillé

```
incubate ─► (naming) ─► (prior-art) ─► (devils-advocate) ─► ux ─► architect ─► (spike) ─► ui
   ─► (design-reviewer, security-reviewer) ─► test-strategy ─► bootstrap ─► slice
   ─► deliver ou autopilot ⟲ (verifier) ─► (follow-up) ─► publish
```

Pour un **projet existant** : `adopt` ─► (`security-reviewer`) ─► (`test-strategy` pour les manques) ─► `slice` (reste à faire) ─► `deliver` ou `autopilot`.

À tout moment : `resume`, `change-course`, `retro`, `project-overview`, `coherence-checker`, `adr`. Les étapes entre parenthèses sont facultatives. Saute ce qui ne s'applique pas (pas d'interface : saute `ux`, `ui` et `design-reviewer`).

### Un exemple de session, de l'idée à la pull request fusionnée

Un parcours condensé sur un petit projet, [commit-report](../examples/README.fr.md) (fictif : un outil en ligne de commande qui transforme un mois d'historique git en compte rendu). Ce que vous tapez, ce qui se passe, ce que vous obtenez. Les documents laissés sont dans `examples/commit-report/` (en anglais).

```
Vous     /trellis:incubate un outil en ligne de commande qui transforme mes commits en compte rendu mensuel
Claude   Je comprends : un outil qui lit l'historique git local et écrit le compte rendu mensuel que
         vous devez à un client. C'est bien cela ?
Vous     oui
Claude   Qui a le problème, et comment s'en sort-il aujourd'hui ? Mon hypothèse : vous, qui refaites
         le compte rendu à la main à partir de plusieurs dépôts, environ une heure par mois.
Vous     ok
         ... succès, périmètre et non-objectifs, contraintes, existant, une question à la fois ...
Claude   L'hypothèse la plus risquée, selon moi : les sujets de commit sont assez bons pour se lire
         comme un compte rendu. Test le moins cher : mettre en forme l'historique du mois dernier avec
         une commande git log d'une ligne et chronométrer votre relecture. Critère d'arrêt : plus de
         20 minutes de retouche.
Claude   Verdict : Tester d'abord.   Écrit : SPEC.md, STATUS.md
```

| Étape | Vous tapez | Vous obtenez |
| --- | --- | --- |
| 1. Idée | `/trellis:incubate …` | `SPEC.md` (R1 à R7, critères d'arrêt), `STATUS.md` |
| 2. Second avis | « Utilise l'agent devils-advocate sur mon SPEC.md. » | un rapport : bloquant, important, mineur |
| 3. Outils existants | `/trellis:prior-art` (proposé par défaut pour un produit) | `docs/prior-art.md` : « adopter d'abord la boucle `git log` » |
| 4. Le test | vous le faites (une heure) | le journal de la spécification : « A1 confirmée, on continue » |
| 5. Architecture | `/trellis:architect` | `ARCHITECTURE.md`, ADR 0001 et 0002 |
| 6. Interface | `/trellis:ux` | `DESIGN.md` (commandes, sortie, messages d'erreur) |
| 7. Tests | `/trellis:test-strategy` | `TESTING.md` (exigence → contrôle) |
| 8. Squelette | `/trellis:bootstrap` | `CLAUDE.md` du projet, CI, un hello-world qui passe |
| 9. Plan | `/trellis:slice` | `PLAN.md` (SL0 à SL5 avec critères d'acceptation) |
| 10. Premières tranches | `/trellis:deliver SL0`, `/trellis:deliver SL1` | tests d'abord, commits, documents à jour |
| 11. L'équipe | `/trellis:autopilot SL2 --push --review` | une pull request ; rapports dans `.trellis/runs/` |
| 12. Retours | `/trellis:follow-up 3 --push --reply` | corrections poussées, un commentaire de synthèse |
| 13. Publication | `/trellis:publish` | version, changelog, tag (chaque étape sortante confirmée) |

Pour reprendre après une pause : `/trellis:resume`. Il lit `STATUS.md`, le plan et git, et dit où vous en êtes et l'unique prochaine action.

### Recettes : que taper quand…

| Situation | Ce qu'il faut taper |
| --- | --- |
| J'ai une idée vague | `/trellis:incubate <l'idée en une phrase>` |
| Est-ce que quelque chose comme ça existe déjà ? | `/trellis:prior-art` |
| Il me faut un nom | `/trellis:naming` (ou « aide-moi à trouver un nom pour ce projet ») |
| Je viens de faire un choix technique | `/trellis:adr nous avons choisi X plutôt que Y parce que …` |
| Je ne sais pas si X va marcher | `/trellis:spike X peut-il faire Y en moins de 200 ms ?` |
| Je veux un avis sceptique sur ma spécification | « Utilise l'agent devils-advocate sur mon SPEC.md. » |
| Je reviens après deux semaines | `/trellis:resume` |
| Je veux ajouter ou retirer une exigence | `/trellis:change-course abandonner la synchronisation` |
| Construire la prochaine tranche sous mes yeux | `/trellis:deliver SL3` |
| Construire des tranches pendant que je fais autre chose | `/trellis:autopilot next --push --review` |
| Une personne a relu ma PR, ou la CI a échoué | `/trellis:follow-up <numéro de PR>` |
| Vérifier que documents et code concordent | « Utilise l'agent coherence-checker. » |
| Le projet a déjà du code | `/trellis:adopt` |
| Lequel de mes projets a besoin de moi ? | `/trellis:project-overview ~/projects` |
| Publier | `/trellis:publish` |

## 26. Adopter un projet existant (`adopt`)

```
/trellis:adopt standard
/trellis:adopt full --draft
```

Pour un projet qui **a déjà du code**. Le code sert de preuve et les documents sont des **hypothèses** tant que vous ne les avez pas confirmés.

1. **Périmètre.** Vous choisissez un niveau : `minimal` (le `CLAUDE.md` du projet et `STATUS.md`), `standard` (plus `ARCHITECTURE.md`, des ADR rétroactifs, une base `TESTING.md` et un rapport d'adoption) ou `full` (plus un `SPEC.md` de ce qui existe et de la suite, et un `PLAN.md` du **reste à faire uniquement**).
2. **Inventaire, en lecture seule.** Stack, structure, commandes de build et de test vérifiées, tests et leurs manques, CI et publication, dépendances, historique git (activité, fichiers les plus modifiés, conventions), documentation existante. Il signale l'existence de fichiers qui ressemblent à des secrets, jamais leur contenu.
3. **Court entretien** sur ce que le code ne dit pas : but et utilisateurs, état pour l'utilisateur, ce qui est fait et ce qui vient, points douloureux, pourquoi les choix clés ont été faits, contraintes, critères de succès et d'arrêt. « Je ne sais pas » devient une question ouverte. Avec `--draft`, pas d'entretien : chaque point d'intention devient une question ouverte, ce qui convient aux sessions non interactives.
4. **Brouillons dans `.trellis/adopt/`**, présentés un par un. Chacun porte le bandeau *« Reconstitué le <date> à partir du code et de l'historique. À confirmer. »* et, pour chaque élément, une **confiance** (haute, moyenne, basse) avec sa source. Les ADR rétroactifs ont le statut `accepted (reconstructed)`, ne listent des alternatives que si elles sont connues, et disent **« raison inconnue, à confirmer »** plutôt que d'en inventer une.
5. **Rapport d'adoption** (`ADOPTION.md`) : ce qui est vérifié ou déduit, questions ouvertes, constats (dette, risques, tests manquants, points chauds) et prochaines étapes.
6. **Contrôle croisé** de ses propres brouillons, avec proposition de lancer `coherence-checker` et `devils-advocate`.
7. **Promotion** : quand vous acceptez, les brouillons sont placés à leur emplacement final. Les documents existants ne sont jamais écrasés : les différences sont montrées et fusionnées seulement selon votre accord. `--promote` ne fait que cette étape, plus tard.

Il ne modifie jamais le code, les tests ni la configuration, et ne commite que si vous le demandez. Ensuite, les étapes utiles sont souvent `security-reviewer`, les manques de tests de la base, puis `slice` pour le reste à faire et `deliver` ou `autopilot`.

**Pour confirmer un élément**, modifiez le document et retirez sa mention « à confirmer » ; le journal des confirmations de `ADOPTION.md` en garde la trace.

## 27. Langues

Les noms (skills, agents, fichiers) sont en anglais. Les documents sont générés dans la langue de ton projet : celle dans laquelle tu écris, sauf si le projet a déjà des documents dans une autre. Les gabarits existent en anglais et en français (`skills/<nom>/templates/*.en.md` et `*.fr.md`).

## 28. Dépannage

| Symptôme | Cause probable et solution |
| --- | --- |
| `/trellis:incubate` est inconnu | Le plugin n'est pas chargé dans cette session. Vérifie `claude plugin list`, démarre une **nouvelle session** après l'installation, ou lance Claude Code avec `--plugin-dir`. |
| Claude n'utilise pas le skill quand tu décris une idée | Appelle-le explicitement avec `/trellis:incubate`. |
| Il demande ce que tu as déjà écrit | Mets tes notes dans le dossier du projet (README, notes) avant de commencer, pour qu'il les lise à l'étape 1. |
| L'entretien paraît trop long | Dis-le. Le skill dimensionne l'entretien selon l'idée et peut sauter les sujets déjà réglés. |
| `Invalid marketplace source format` | Tu as passé une URL `ssh://…`. Utilise `git@hôte:propriétaire/dépôt.git` (voir section 1). |
| `Cannot add marketplace "trellis": its source doesn't match its extraKnownMarketplaces entry` | Le nom de marketplace `trellis` est déjà déclaré depuis une autre source (une installation précédente depuis un miroir ou un fork). Claude Code n'ajoute pas le même nom depuis une source différente. Supprime-la puis ajoute-la depuis la source voulue : `claude plugin marketplace remove trellis`, puis `claude plugin install trellis --marketplace <nouvelle source>`. La suppression désinstalle aussi le plugin, que la seconde commande réinstalle. |
| `unable to get password from user` | Tu as utilisé une URL HTTPS sur un dépôt privé (un fork privé, par exemple). Utilise la forme SSH. |
| L'état du projet s'affiche à chaque début de session et je n'en veux pas | Définis la variable d'environnement `TRELLIS_NO_STATUS=1`. |
| Une mise à jour n'est pas arrivée | Lance `claude plugin marketplace update trellis` puis `claude plugin update trellis@trellis` dans un terminal, et démarre une nouvelle session. |

## 29. Contribuer et travailler sur le plugin

Les conventions du dépôt (organisation, nommage, documentation bilingue, « strict mais souple ») sont dans [`CLAUDE.md`](../CLAUDE.md). Pour vérifier une modification :

```sh
claude plugin validate .
```
