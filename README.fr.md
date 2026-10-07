<h1 align="center">QitQode</h1>

<p align="center"><strong>Agent de codage IA en terminal, doté d'une mémoire.</strong></p>

<p align="center">
  <a href="https://qitqode.com">Site web</a>
</p>

<p align="center">
  <a href="./README.md">English</a> | <a href="./README.zh.md">简体中文</a> | <a href="./README.zht.md">繁體中文</a> | <a href="./README.ja.md">日本語</a> | <strong>Français</strong> | <a href="./README.ru.md">Русский</a> | <a href="./README.es.md">Español</a> | <a href="./README.pt.md">Português</a>
</p>

---

La plupart des agents de codage oublient tout dès qu'une session se termine. Pas QitQode. Il lit et écrit du code, exécute des commandes, gère Git — et conserve une mémoire persistante et consultable de votre projet d'une session à l'autre, reconstruisant son propre contexte lorsqu'une session s'éternise afin de poursuivre le travail plutôt que de repartir de zéro.

Un seul compte, huit capacités — **Free (no cost)**, **Adaptive**, **Fast**, **Economy**, **Planner**, **Repair**, **Max intelligence**. Le pipeline **Orchestrated** est une capacité séparée de Qortex pour le flux multi-étapes gate → plan → build → repair, pas une sélection interactive normale. Aucun tableau de bord de fournisseur, aucune jonglerie avec les clés d'API, aucun tableur de facturation par modèle.

---

## Démarrage rapide

```bash
npm install -g @qitqode/cli
# or: bun add --global @qitqode/cli

# Run
qitqode
```

Au premier lancement, un assistant vous guide pour la connexion :

- **Se connecter avec QitQode** — un flux par code d'appareil qui fonctionne partout, y compris dans les sessions SSH et les bacs à sable distants : la CLI affiche une URL de vérification et un code (et ouvre votre navigateur lorsqu'il y en a un de disponible) ; validez sur n'importe quel appareil pour terminer
- **Clé d'API** — collez plutôt une clé d'API QitQode

Choisissez ensuite un niveau dans le sélecteur de modèles et commencez à travailler. C'est toute l'installation.

### Utiliser QitQode en Français

La TUI détecte automatiquement la langue de votre système. Pour la changer manuellement, exécutez `/language` (ou `/lang`) dans QitQode et choisissez Français dans la liste.

<details>
<summary><strong>WSL : problèmes de presse-papiers</strong></summary>

Si vous rencontrez du texte illisible lors d'une copie sous WSL, installez `xsel` :

```bash
sudo apt install xsel
```

</details>

---

## Pourquoi QitQode

Vous n'avez pas besoin d'une énième surcouche de chat. Vous avez besoin d'un agent capable de mener un travail de longue haleine sans perdre le fil. QitQode repose sur quatre mécanismes :

### 1. Une mémoire qui survit à la session

Chaque projet dispose d'une couche de mémoire persistante appuyée sur la recherche plein texte de SQLite : les connaissances du projet dans `MEMORY.md`, des points de contrôle de session automatiques, des notes de travail et des journaux de progression par tâche. À la reprise, la mémoire pertinente est injectée automatiquement — classée et limitée par un budget de tokens, sans tout déverser. L'agent reprend là où il s'était arrêté au lieu de réapprendre votre base de code.

### 2. Un contexte qui se reconstruit tout seul

Les tâches longues dépassent vite les fenêtres de contexte. QitQode surveille la fenêtre, enregistre un point de contrôle avant qu'elle ne se remplisse, puis reconstruit le contexte de travail à partir du dernier point de contrôle, de la mémoire du projet et de la progression des tâches — pour qu'un refactoring de plusieurs heures ne meure pas à la limite de tokens.

### 3. Une autonomie dont vous gardez le contrôle

Définissez une condition d'arrêt avec `/goal`. Lorsque l'agent estime avoir terminé, un modèle juge indépendant relit la conversation et décide si l'objectif est réellement atteint — fini les « c'est bon, tout est fait ! » optimistes à mi-parcours. Combinez cela avec le suivi de tâches arborescent (`T1`, `T1.1`, …) et les sous-agents parallèles pour un vrai travail sans surveillance.

### 4. Un seul abonnement, zéro tuyauterie de fournisseur

Sept capacités interactives, une seule connexion. Changez de capacité en pleine session avec `/free`, `/fast`, `/economical`, `/adaptive`, `/planner`, `/repair` ou `/max-int`. La capacité `/orchestrated` est réservée pour le chemin de pipeline Qortex plutôt que pour des tours interactifs ordinaires.

### Et les détails que les autres laissent de côté

- **Identifiants chiffrés au repos** — vos jetons d'authentification sont scellés avec une clé conservée dans le trousseau du système d'exploitation, et les variables d'environnement d'identifiants sont retirées par défaut de chaque processus enfant lancé par l'agent.
- **Une TUI utilisable par tous** — mode compatible lecteur d'écran, prise en charge de `NO_COLOR`, mouvement réduit, contrôle du niveau de verbosité des annonces et un thème à fort contraste conforme WCAG AA. Intégré d'emblée, pas rajouté après coup. (Détails plus bas.)
- **Licence MIT simple** — pas de fichier séparé de restrictions d'usage, pas de conditions de service enfouies à la fin du README.
- **Connexion par code d'appareil conçue pour de vraies machines** — fonctionne via SSH, dans des conteneurs et dans des bacs à sable distants, là où une redirection de navigateur en boucle locale ne le pourrait jamais.

---

## Fonctionnalités principales

### Plusieurs agents

| Agent       | Description                                                                 |
| ----------- | --------------------------------------------------------------------------- |
| **build**   | Par défaut. Autorisations d'outils complètes pour le développement          |
| **plan**    | Mode d'analyse en lecture seule pour l'exploration de code et la conception de solutions |
| **compose** | Mode d'orchestration pour le développement piloté par spécifications et les workflows pilotés par compétences |

Appuyez sur `Alt+M` pour passer d'un agent principal à l'autre. Les sous-agents sont créés par le système selon les besoins.

### Mémoire persistante

Mémoire inter-sessions propulsée par la recherche plein texte SQLite FTS5 :

- **Mémoire du projet** (`MEMORY.md`) — connaissances persistantes du projet, règles et décisions d'architecture
- **Point de contrôle de session** (`checkpoint.md`) — instantanés d'état structurés maintenus automatiquement par le sous-agent chargé d'écrire les points de contrôle
- **Notes de travail** (`notes.md`) — zone de notes temporaires pour les agents
- **Progression des tâches** (`tasks/<id>/progress.md`) — journaux par tâche

La mémoire est injectée automatiquement à la reprise d'une session, de sorte que l'agent n'a pas besoin de réapprendre le contexte du projet.

### Gestion intelligente du contexte

- **Points de contrôle automatiques** — décide quand enregistrer l'état de la session en fonction de la fenêtre de contexte du modèle
- **Reconstruction du contexte** — lorsque le contexte approche de la limite, il est reconstruit à partir du dernier point de contrôle, de la mémoire du projet, de la progression des tâches et des messages récents conservés, afin que l'agent puisse poursuivre la tâche en cours
- **Injection budgétée** — utilise un budget de tokens pour contrôler la quantité de contenu de point de contrôle, de mémoire et de notes qui entre dans le contexte, avec un classement par importance

### Suivi des tâches

Un système de tâches arborescent (`T1`, `T1.1`, `T1.2`, …) qui s'intègre automatiquement au système de points de contrôle, de sorte que la progression des tâches est préservée à la reprise des sessions.

### Système de sous-agents

L'agent principal peut créer des sous-agents à la demande. Les sous-agents partagent le contexte de la session en cours et peuvent travailler en parallèle, avec suivi du cycle de vie, annulation et exécution en arrière-plan.

### Objectif / Condition d'arrêt

La commande `/goal` définit une condition d'arrêt pour une session. Lorsque l'agent tente de s'arrêter, un modèle juge indépendant évalue la conversation pour déterminer si la condition est réellement satisfaite — évitant les « arrêts optimistes » prématurés pendant le travail autonome.

### Mode Compose

Le mode Compose offre un workflow structuré pour le développement piloté par spécifications. Il inclut des compétences intégrées pour la planification, l'exécution, la revue de code, le TDD, le débogage, la vérification et la fusion — orchestrant tout le cycle de vie, de la spécification au code livré.

### Prédiction de prompt

Des suggestions en texte fantôme incrustées prédisent votre prochain prompt au fil du travail — appuyez sur `Tab` pour accepter.

### Recherche approfondie

Le workflow intégré `/deep-research` mène une investigation structurée en plusieurs étapes pour les questions qui exigent plus qu'une simple recherche.

### Usage headless et IDE

Exécutez `qitqode serve` pour un serveur HTTP headless, ou `qitqode acp` pour la prise en charge d'Agent Client Protocol afin de piloter QitQode depuis des éditeurs compatibles et des environnements distants.

**Exécutions sans surveillance.** Trois options décident de la fréquence à laquelle la TUI s'arrête pour vous demander quelque chose :

| Option | Questions | Autorisations d'outils |
| --- | --- | --- |
| `--never-ask` | décidées automatiquement | vous sont toujours demandées |
| `--fullauto` | décidées automatiquement | approuvées automatiquement, sauf ce que votre configuration refuse explicitement |
| `--headless` | décidées automatiquement | approuvées automatiquement — et aucune TUI ; nécessite `--prompt` ou stdin |

`--fullauto` conserve la TUI interactive normale : vous suivez la session en direct et un badge `FULL-AUTO` reste affiché sur le prompt tant que des autorisations sont accordées à votre place. Tout ce que vous passez à `deny` dans votre configuration reste refusé. `--fullauto` implique `--never-ask`, et le combiner avec `--headless` est sans effet (headless se comporte déjà ainsi).

### Entrée vocale

Entrée vocale en flux temps réel propulsée par TenVAD. Activez-la avec `/voice`, puis parlez — l'audio est segmenté par les pauses et transcrit progressivement dans la saisie. Nécessite `sox` (`brew install sox` sur macOS, procédure similaire sur les autres plateformes) et un modèle de reconnaissance vocale explicitement configuré via le champ de configuration `voice`.

> **Remarque :** Les modèles de codage sont accessibles uniquement par niveau via le backend QitQode. Le champ `voice` est une exception ciblée servant uniquement à la reconnaissance vocale et au contrôle vocal — il n'ajoute pas de modèles à la liste des modèles de codage.

<details>
<summary><strong>Configuration audio WSLg</strong></summary>

```bash
sudo apt install -y sox pulseaudio libasound2-plugins
export PULSE_SERVER=unix:/mnt/wslg/PulseServer
```

</details>

<details>
<summary><strong>Audio distant via SSH (Mac → hôte distant)</strong></summary>

```bash
# Mac (local)
brew install pulseaudio
pulseaudio --load="module-native-protocol-tcp auth-ip-acl=127.0.0.1" --exit-idle-time=-1 --daemonize
# Add to ~/.ssh/config: RemoteForward 4713 127.0.0.1:4713

# Remote host
apt install -y pulseaudio pulseaudio-utils sox
export PULSE_SERVER=tcp:127.0.0.1:4713
# Verify: pactl info
```

</details>

### Dream et Distill

- **`/dream`** — parcourt les traces des sessions récentes, extrait les connaissances persistantes dans la mémoire du projet et supprime les entrées obsolètes
- **`/distill`** — repère les workflows manuels répétés dans le travail récent et transforme les candidats à forte confiance en compétences, sous-agents ou commandes réutilisables

---

## Configuration

QitQode se configure via `.qitqode/qitqode.json` dans le répertoire du projet (ou `~/.config/qitqode/qitqode.json` globalement). Les principales options incluent :

- La sélection de la capacité de modèle (Free (no cost), Adaptive, Fast, Economy, Planner, Repair, Max intelligence, et le pipeline Orchestrated)
- Les autorisations des agents et les agents personnalisés
- Le comportement des points de contrôle et de la mémoire
- Les connexions aux serveurs MCP
- Les raccourcis clavier et le thème

Le Max Mode (raisonnement parallèle best-of-N avec sélection par juge) peut être activé via `experimental.maxMode` dans la configuration.

---

## Accessibilité

La TUI de QitQode est livrée avec une prise en charge de l'accessibilité de premier plan :

- **Mode accessible** — définissez `QITQODE_TUI_ACCESSIBLE=1` (ou `"tui": { "accessible": true }` dans la configuration) pour une expérience adaptée aux lecteurs d'écran : rendu linéaire de l'écran principal (pas d'écran alternatif), pas de capture de la souris, faible fréquence d'images, mouvement réduit et aucun signal sonore.
- **NO_COLOR** — toute valeur non vide de [`NO_COLOR`](https://no-color.org) bascule vers un rendu monochrome avec arrière-plans transparents. La gravité n'est jamais transmise par la couleur seule (les notifications portent les symboles `ℹ ✓ ▲ ✗`, les diffs conservent les marqueurs `+`/`-`).
- **Réduction du mouvement** — définissez `QITQODE_REDUCE_MOTION=1` (ou `"tui": { "reduce_motion": true }`) pour remplacer les indicateurs animés et les animations par du texte statique. Également activable en cours d'exécution depuis la liste des commandes.
- **Son** — désactivez les signaux sonores avec `QITQODE_TUI_SOUND=0`, `"tui": { "sound": false }` ou le commutateur en cours d'exécution dans la liste des commandes. Le mode accessible désactive toujours le son.
- **Verbosité des annonces** — en mode accessible, contrôlez le degré de bavardage des annonces du lecteur d'écran avec `QITQODE_TUI_ANNOUNCEMENTS=quiet|normal|verbose`, `"tui": { "announcements": "quiet" }` ou le sélecteur en cours d'exécution dans la liste des commandes. `quiet` annonce uniquement les frontières de tours ; `normal` (par défaut) ajoute les lignes de démarrage d'outils ; `verbose` ajoute les lignes d'achèvement d'outils. Les erreurs et les interruptions sont toujours annoncées, à tous les niveaux.
- **Thème à fort contraste** — sélectionnez le thème intégré `high-contrast` pour des surfaces noir/blanc pures avec des couleurs conformes WCAG AA.
- **Petits terminaux** — la TUI se dégrade proprement dans les terminaux étroits et affiche un message clair lorsque la fenêtre passe sous le minimum de 40x8.
- **Boîtes de dialogue au clavier uniquement** — chaque boîte de dialogue est entièrement utilisable sans souris : `Esc` ferme toujours, `Tab` (et les touches fléchées, lorsqu'une boîte de dialogue comporte une rangée de boutons ou une liste) déplace le focus, et `Enter` ou `Space` active le contrôle ciblé. Dans les terminaux simples/NO_COLOR, la ligne de liste en surbrillance porte aussi un marqueur `›` afin que la sélection soit distinguable sans couleur.

Les variables d'environnement d'accessibilité sont des interrupteurs à sens unique : `QITQODE_TUI_ACCESSIBLE=1`, `QITQODE_REDUCE_MOTION=1`, `NO_COLOR` et `QITQODE_TUI_SOUND=0` l'emportent toujours sur les valeurs de configuration et les commutateurs en cours d'exécution — les garanties d'accessibilité ne peuvent pas être désactivées de nouveau. `QITQODE_TUI_ANNOUNCEMENTS` l'emporte de la même façon sur la valeur de configuration et le sélecteur en cours d'exécution lorsqu'elle est définie.

---

## Développement

```bash
bun install              # Install dependencies
bun run dev              # Run in development mode
bun turbo typecheck      # Type check
```

---

## Licence

Le code source est distribué sous [licence MIT](./LICENSE).
