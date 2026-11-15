# Politique de sécurité

*English: [SECURITY.md](SECURITY.md)*

Trellis est un plugin [Claude Code](https://claude.com/claude-code) fait d'instructions en Markdown (skills et agents), de gabarits et d'un petit script shell (le hook `SessionStart` dans `hooks/`). Il n'y a ni serveur ni service réseau. Cela dit, les instructions d'un plugin s'exécutent avec les permissions que vous donnez à votre session Claude Code : la sécurité compte ici.

## Versions prises en charge

Seule la **dernière version** reçoit des correctifs. Mettez à jour (`claude plugin marketplace update trellis`, puis `claude plugin update trellis@trellis`) avant de signaler un problème, pour vérifier qu'il existe encore.

## Signaler une vulnérabilité

**Merci de ne pas ouvrir d'issue ni de pull request publique pour un problème de sécurité.**

Utilisez plutôt le signalement privé de GitHub : onglet **Security** du dépôt, puis **Report a vulnerability**, ou ouvrez
<https://github.com/sebmafate/trellis/security/advisories/new>.

Détails utiles :

- le fichier concerné (un skill, un agent, le script du hook, un gabarit) et la version ;
- ce qu'un attaquant pourrait faire faire au plugin, et dans quelles conditions ;
- les étapes pour reproduire, ou une courte description du scénario ;
- une éventuelle proposition de correctif.

C'est un projet personnel maintenu par une seule personne. Je ferai de mon mieux pour accuser réception sous environ une semaine et vous tenir informé. Je ne peux promettre ni délai fixe ni récompense. Merci de laisser un temps raisonnable pour corriger avant toute divulgation publique ; vous serez cité dans l'avis si vous le souhaitez.

## Ce qui est concerné

- Le **script du hook** (`hooks/session-status.sh`) : injection de commande, traitement dangereux du contenu d'un fichier, tout ce qui pourrait exécuter du code non prévu ou faire fuiter des données.
- Les **skills et agents qui agissent hors de la conversation**, par exemple `autopilot` (poussées, pull requests), `publish` (étiquettes, envois, déploiements), `developer` et `deliver` (modifications de fichiers et commits), `bootstrap` : toute façon d'agir sans la confirmation promise, de pousser ou fusionner dans une branche protégée, d'exposer des secrets ou d'écrire hors du projet.
- Les **instructions qui peuvent être retournées contre l'utilisateur** : par exemple, un contenu lu dans les fichiers d'un projet (un `STATUS.md`, une consigne, un rapport) qui ferait suivre à un agent les instructions d'un attaquant avec des conséquences réelles.
- Le **dépôt lui-même** : identifiants divulgués, publication compromise, ou modification malveillante du manifeste de la marketplace.

## Ce qui n'est pas concerné

- Les vulnérabilités de **Claude Code**, des modèles ou des services d'Anthropic : signalez-les à Anthropic.
- Le comportement général d'un modèle (par exemple une réponse erronée occasionnelle) qui n'implique aucune instruction ni aucun script de Trellis.
- Les problèmes qui supposent une machine ou un compte déjà compromis.
- Les constats sur le **code de votre propre projet** produits par l'agent `security-reviewer` : ils relèvent de votre projet.

## Bonnes pratiques avec Trellis

- Lisez les skills et agents d'un plugin avant de l'installer, comme tout code que vous laissez agir en votre nom.
- Gardez les demandes d'autorisation de Claude Code pour les poussées, publications et suppressions. `autopilot` et `publish` demandent une autorisation explicite pour les actions visibles de l'extérieur ; n'autorisez pas plus que nécessaire.
- Ne mettez jamais de secrets dans les documents que Trellis écrit (`SPEC.md`, `STATUS.md`, consignes, rapports) : ils finissent dans l'historique git.
