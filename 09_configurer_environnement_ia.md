# Chapitre 9 — Configurer son environnement IA

## Principe directeur

> Un agent IA bien configuré est comme un collègue bien onboardé : il connaît les conventions du projet, sait quoi éviter, et pose les bonnes questions. Le temps investi dans la configuration est remboursé dès la première session de travail.

Ce chapitre couvre en profondeur les mécanismes de configuration de Claude Code : le fichier CLAUDE.md, les hooks, les skills, la mémoire, et les permissions. Les principes sont transposables aux autres outils (Cursor, Copilot), avec des équivalents mentionnés lorsqu'ils existent.

---

## 9.1 — Le fichier CLAUDE.md en profondeur

### Rappel

Le chapitre 8 fournit un **template prêt à l'emploi** ([CLAUDE_TEMPLATE_BONNES_PRATIQUES.md](templates/CLAUDE_TEMPLATE_BONNES_PRATIQUES.md)) qui couvre les garde-fous comportementaux : reformulation, questions avant exécution, détection des demandes vagues. Ce chapitre se concentre sur **comment aller plus loin** en adaptant le CLAUDE.md au contexte technique de chaque projet.

### La hiérarchie des fichiers CLAUDE.md

Claude Code lit les fichiers CLAUDE.md à plusieurs niveaux, du plus spécifique au plus général :

| Niveau | Emplacement | Portée | Partagé via git ? |
|--------|-------------|--------|-------------------|
| **Managed** (le plus prioritaire) | `C:\Program Files\ClaudeCode\CLAUDE.md` (Windows) | Tous les utilisateurs de la machine | Non (déployé par l'IT) |
| **Projet** | `./CLAUDE.md` ou `./.claude/CLAUDE.md` | L'équipe entière | Oui |
| **Local** | `./CLAUDE.local.md` | Vous seul, sur ce projet | Non (à `.gitignore`) |
| **Utilisateur** | `~/.claude/CLAUDE.md` | Vous, tous vos projets | Non |

**Règle de chargement** : tous les fichiers sont chargés et concaténés (ils ne s'écrasent pas). En cas d'instructions contradictoires, les niveaux plus spécifiques prennent le dessus.

**Chargement par répertoire** : dans un monorepo, un fichier `packages/frontend/CLAUDE.md` sera chargé automatiquement quand Claude travaille dans ce répertoire. Cela permet des instructions spécifiques par module.

### Le CLAUDE.md projet : adapter au contexte technique

Le template du chapitre 8 couvre le comportement général. Voici comment le compléter selon votre stack.

#### Projet .NET / C#

```markdown
# Stack technique
- .NET 8, C# 12
- Entity Framework Core 8 (code first, jamais de raw SQL)
- MediatR pour CQRS (une commande/query par fichier)
- FluentValidation pour la validation d'entrée
- xUnit + Moq pour les tests

# Conventions
- Architecture Clean Architecture : Domain > Application > Infrastructure > API
- Les règles métier vivent dans le Domain, jamais dans les Controllers
- Nommage des tests : `Devrait_[résultat]_Quand_[condition]`
- Pas de logique dans les constructeurs
- Les exceptions métier héritent de DomainException

# Commandes
- `dotnet test` — lancer les tests
- `dotnet ef migrations add <Name>` — créer une migration
- `dotnet build` — vérifier la compilation

# Interdits
- Ne jamais modifier une migration existante
- Ne jamais utiliser DateTime.Now (utiliser IDateTimeProvider)
- Ne jamais retourner d'entité Domain depuis un Controller (toujours mapper en DTO)
```

#### Projet TypeScript / Node.js

```markdown
# Stack technique
- Node.js 20, TypeScript 5.4 (strict mode)
- Express.js avec express-validator
- Prisma ORM avec PostgreSQL
- Jest pour les tests, Supertest pour les tests d'intégration

# Conventions
- Structure par feature (pas par type de fichier)
- Imports absolus via tsconfig paths (@/modules, @/shared)
- Fonctions pures privilégiées, classes uniquement pour les services avec état
- Zod pour la validation runtime des entrées API
- Nommage : camelCase fonctions/variables, PascalCase types/interfaces

# Commandes
- `npm test` — tests unitaires
- `npm run test:e2e` — tests d'intégration
- `npm run lint` — ESLint + Prettier
- `npx prisma migrate dev` — migration de base de données

# Interdits
- Pas de `any` — utiliser `unknown` si le type est vraiment inconnu
- Pas de `console.log` en production — utiliser le logger structuré (pino)
- Pas de dépendance ajoutée sans en discuter d'abord
```

#### Projet Python / Django

```markdown
# Stack technique
- Python 3.12, Django 5.0, Django REST Framework
- PostgreSQL via Django ORM
- Celery + Redis pour les tâches asynchrones
- pytest + factory_boy pour les tests

# Conventions
- Une app Django par domaine métier
- Serializers DRF pour la validation, pas de forms Django
- Type hints obligatoires sur toutes les fonctions publiques
- Docstrings Google style sur les fonctions publiques
- Black + isort + ruff pour le formatage

# Commandes
- `pytest` — lancer les tests
- `python manage.py makemigrations` — créer une migration
- `python manage.py migrate` — appliquer les migrations
- `ruff check .` — linter

# Interdits
- Pas de queryset dans les views (passer par un service ou un manager)
- Pas de `*` dans les imports
- Pas de logique métier dans les serializers
```

#### Projet React / Frontend

```markdown
# Stack technique
- React 18 avec TypeScript
- Vite comme bundler
- TanStack Query pour le data fetching
- Zustand pour le state management global
- Tailwind CSS pour le styling

# Conventions
- Un composant par fichier, nommé en PascalCase
- Hooks custom dans des fichiers `use*.ts`
- Tests avec Vitest + Testing Library (tester le comportement, pas l'implémentation)
- Pas de prop drilling au-delà de 2 niveaux — utiliser un context ou Zustand

# Commandes
- `npm run dev` — serveur de développement
- `npm test` — tests unitaires
- `npm run build` — build de production
- `npm run storybook` — composants isolés

# Interdits
- Pas de `useEffect` pour de la synchronisation de données — utiliser TanStack Query
- Pas de `index.ts` barrel files (problèmes de tree-shaking)
- Pas de CSS-in-JS ni de styles inline
```

### Le CLAUDE.local.md : vos préférences personnelles

Ce fichier n'est pas commité (ajoutez-le dans `.gitignore`). Il contient vos préférences individuelles :

```markdown
# Préférences personnelles

- Je préfère les réponses concises. Pas de pédagogie sauf si je demande.
- Quand je dis "lance les tests", lance uniquement ceux du module en cours,
  pas toute la suite.
- Pour les messages de commit, utilise le format Conventional Commits en anglais.
- Je travaille sur Windows avec Git Bash.
```

### Le CLAUDE.md utilisateur : vos constantes

Fichier `~/.claude/CLAUDE.md`, actif sur tous vos projets :

```markdown
# Préférences globales

- Langue de communication : français
- Langue du code (variables, fonctions, commentaires) : anglais
- Quand je dis "commit", ne fais que le commit, ne push pas.
- Ne jamais ajouter d'emoji dans le code ou les messages de commit.
```

### Système d'imports

Le CLAUDE.md supporte les imports pour éviter la duplication :

```markdown
# Projet MonApp

Voir @README.md pour la vue d'ensemble du projet.
Voir @docs/architecture.md pour les décisions d'architecture.

# Conventions de code
@docs/coding-standards.md
```

Les chemins sont relatifs à l'emplacement du fichier CLAUDE.md.

### Erreurs courantes avec CLAUDE.md

| Erreur | Conséquence | Solution |
|--------|-------------|----------|
| Fichier trop long (500+ lignes) | L'agent perd en efficacité, instructions noyées | Garder sous 200 lignes, utiliser les imports |
| Instructions contradictoires | Comportement imprévisible | Relire et consolider régulièrement |
| Jamais mis à jour | Instructions obsolètes appliquées | Revoir à chaque changement de stack ou de convention |
| Trop de détails techniques | Redondant avec ce que l'agent peut lire dans le code | Documenter le « pourquoi », pas le « quoi » |
| Pas de CLAUDE.local.md | Préférences personnelles polluent le CLAUDE.md partagé | Séparer individuel et collectif |

---

## 9.2 — Les hooks : automatiser les garde-fous

### Qu'est-ce qu'un hook ?

Un hook est un **script qui s'exécute automatiquement** en réponse à un événement dans Claude Code. C'est l'équivalent des git hooks, mais pour l'agent IA.

### Les événements disponibles

| Événement | Quand il se déclenche | Bloquant ? | Cas d'usage |
|-----------|----------------------|------------|-------------|
| `SessionStart` | Début ou reprise de session | Non | Charger des variables d'environnement, notification |
| `UserPromptSubmit` | Avant que Claude traite la demande de l'utilisateur | Oui | Valider le prompt, ajouter du contexte |
| `PreToolUse` | Avant l'exécution d'un outil (Bash, Edit, Write...) | Oui | Bloquer des commandes dangereuses, vérifier les fichiers |
| `PostToolUse` | Après exécution réussie d'un outil | Oui | Lint automatique, notification |
| `Stop` | Quand Claude finit de répondre | Oui | Vérification finale, rapport |
| `FileChanged` | Un fichier surveillé change sur disque | Non | Recharger des variables d'environnement |

### Configuration

Les hooks se configurent dans les fichiers `settings.json` :

| Portée | Fichier |
|--------|---------|
| Ce projet (partagé) | `.claude/settings.json` |
| Ce projet (personnel) | `.claude/settings.local.json` |
| Tous les projets | `~/.claude/settings.json` |

### Types de hooks

| Type | Fonctionnement | Adapté pour |
|------|---------------|-------------|
| `command` | Exécute un script shell | Vérifications techniques (lint, secrets, format) |
| `prompt` | Évaluation par le LLM en une passe | Analyse sémantique du prompt ou du code |
| `agent` | Lance un sous-agent | Vérifications complexes nécessitant du raisonnement |
| `http` | Appel HTTP POST | Intégration avec des outils externes |

### Exemple 1 — Bloquer les commandes destructives

Ce hook empêche l'agent d'exécuter des commandes `rm -rf`, `drop table`, ou `git push --force` :

**`.claude/settings.json`** :
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "bash .claude/hooks/block-destructive.sh",
            "statusMessage": "Vérification de sécurité..."
          }
        ]
      }
    ]
  }
}
```

**`.claude/hooks/block-destructive.sh`** :
```bash
#!/bin/bash
# Lit l'entrée JSON depuis stdin
INPUT=$(cat)
COMMAND=$(echo "$INPUT" | jq -r '.tool_input.command')

# Patterns dangereux
DANGEROUS_PATTERNS=(
  "rm -rf"
  "drop table"
  "drop database"
  "git push --force"
  "git push -f"
  "git reset --hard"
  "truncate table"
  "> /dev/null"
)

for pattern in "${DANGEROUS_PATTERNS[@]}"; do
  if echo "$COMMAND" | grep -qi "$pattern"; then
    echo "{
      \"hookSpecificOutput\": {
        \"hookEventName\": \"PreToolUse\",
        \"permissionDecision\": \"deny\",
        \"permissionDecisionReason\": \"Commande destructive bloquée : '$pattern' détecté. Demandez confirmation à l'utilisateur avant de retenter.\"
      }
    }"
    exit 0
  fi
done

# Commande autorisée
exit 0
```

### Exemple 2 — Lint automatique après modification

Lancer automatiquement le linter après chaque modification de fichier :

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "bash .claude/hooks/auto-lint.sh",
            "statusMessage": "Lint en cours..."
          }
        ]
      }
    ]
  }
}
```

**`.claude/hooks/auto-lint.sh`** :
```bash
#!/bin/bash
INPUT=$(cat)
FILE=$(echo "$INPUT" | jq -r '.tool_input.file_path // empty')

if [ -z "$FILE" ]; then
  exit 0
fi

EXTENSION="${FILE##*.}"

case "$EXTENSION" in
  ts|tsx|js|jsx)
    npx eslint --fix "$FILE" 2>/dev/null
    ;;
  py)
    ruff check --fix "$FILE" 2>/dev/null
    ;;
  cs)
    dotnet format --include "$FILE" 2>/dev/null
    ;;
esac

exit 0
```

### Exemple 3 — Détection de secrets avec un hook prompt

Utiliser le LLM lui-même pour vérifier qu'un fichier modifié ne contient pas de secrets :

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Le fichier suivant vient d'être modifié. Vérifie s'il contient des secrets en dur (clés API, mots de passe, tokens, chaînes de connexion). Réponds UNIQUEMENT 'OK' si rien trouvé, ou 'ALERTE: [description]' si un secret potentiel est détecté. Fichier: $ARGUMENTS",
            "timeout": 15
          }
        ]
      }
    ]
  }
}
```

### Bonnes pratiques pour les hooks

| Faire | Ne pas faire |
|-------|--------------|
| Garder les hooks rapides (< 5 secondes) | Créer des hooks qui ralentissent chaque action |
| Commencer par des hooks non bloquants pour tester | Bloquer l'agent sans avoir testé le hook |
| Versionner les scripts de hooks avec le projet | Mettre des chemins absolus locaux |
| Documenter les hooks dans le CLAUDE.md | Avoir des hooks silencieux que personne ne connaît |
| Utiliser `jq` pour parser l'entrée JSON | Faire du parsing artisanal avec `grep` |

---

## 9.3 — Les skills : créer des workflows réutilisables

### Qu'est-ce qu'une skill ?

Une skill est une **commande personnalisée** invocable via `/nom-de-la-skill`. C'est un prompt structuré, réutilisable, avec sa propre configuration. L'équivalent d'un alias bash, mais pour l'agent IA.

### Emplacement des skills

| Portée | Chemin |
|--------|--------|
| Projet (partagé) | `.claude/skills/<nom>/SKILL.md` |
| Personnel (tous projets) | `~/.claude/skills/<nom>/SKILL.md` |

Les skills du projet sont versionnées avec le code et partagées automatiquement via git.

### Structure d'une skill

```
.claude/skills/
├── review/
│   └── SKILL.md
├── test/
│   └── SKILL.md
├── doc/
│   └── SKILL.md
└── new-feature/
    ├── SKILL.md
    └── template.md
```

### Anatomie d'un SKILL.md

Chaque skill est un fichier Markdown avec un en-tête YAML (frontmatter) :

```yaml
---
name: review
description: Review de code avec checklist de bonnes pratiques
user-invocable: true
allowed-tools: "Read Grep Glob"
---

# Instructions

[Le prompt que Claude exécutera]
```

#### Les champs du frontmatter

| Champ | Description | Valeurs |
|-------|-------------|---------|
| `name` | Nom de la skill (défaut : nom du dossier) | Texte libre |
| `description` | Description courte, utilisée pour l'auto-invocation | Texte libre |
| `argument-hint` | Indice affiché dans l'autocomplétion | Ex : `"[fichier]"` |
| `user-invocable` | L'utilisateur peut l'invoquer avec `/nom` | `true` / `false` |
| `disable-model-invocation` | Empêche Claude de l'invoquer automatiquement | `true` / `false` |
| `allowed-tools` | Outils pré-autorisés (pas de demande de permission) | Ex : `"Read Grep Bash(npm test)"` |
| `model` | Modèle spécifique pour cette skill | Ex : `claude-sonnet-4-6` |
| `context` | Exécuter dans un sous-agent isolé | `fork` |

### Exemples de skills utiles pour une équipe

#### Skill `/review` — Review de code structurée

**`.claude/skills/review/SKILL.md`** :
```yaml
---
name: review
description: Review de code avec checklist complète
argument-hint: "[fichier ou dossier]"
user-invocable: true
allowed-tools: "Read Grep Glob"
---

Effectue une review de code sur $ARGUMENTS.

## Procédure

1. Lis les fichiers modifiés (utilise `git diff` si aucun argument fourni).
2. Pour chaque fichier modifié, analyse selon cette grille :

### Sécurité
- [ ] Pas d'injection possible (SQL, XSS, commande)
- [ ] Entrées validées et assainies
- [ ] Pas de secrets en dur
- [ ] Autorisations vérifiées

### Qualité
- [ ] Le code est compréhensible sans commentaire excessif
- [ ] Pas de duplication avec du code existant
- [ ] Gestion des erreurs appropriée
- [ ] Cas limites couverts

### Conventions
- [ ] Nommage conforme aux conventions du projet
- [ ] Architecture respectée
- [ ] Pas de dépendance ajoutée sans justification

## Format de sortie

Pour chaque problème trouvé :
- **Sévérité** : Critique / Majeur / Mineur / Suggestion
- **Fichier:ligne** : emplacement
- **Problème** : description
- **Suggestion** : correction proposée

Trie par sévérité décroissante.
```

**Utilisation** : `/review src/services/PaymentService.ts`

#### Skill `/test` — Génération de tests guidée

**`.claude/skills/test/SKILL.md`** :
```yaml
---
name: test
description: Génère des tests pour un fichier ou une fonction
argument-hint: "[fichier:fonction]"
user-invocable: true
allowed-tools: "Read Grep Glob Bash"
---

Génère des tests pour $ARGUMENTS.

## Procédure

1. Lis le fichier cible et identifie les comportements publics à tester.
2. Identifie le framework de test utilisé dans le projet (regarde package.json,
   *.csproj, ou pyproject.toml).
3. Regarde les tests existants pour reproduire le style et les conventions.
4. Génère les tests en couvrant :
   - Le happy path (fonctionnement normal)
   - Les cas d'erreur (entrées invalides, exceptions)
   - Les cas limites (listes vides, null, valeurs extrêmes, concurrence)
5. Lance les tests pour vérifier qu'ils passent.

## Règles

- Teste le **comportement**, pas l'**implémentation**.
- Chaque test doit avoir un nom explicite qui décrit le scénario.
- Si le code n'est pas testable en l'état (dépendances cachées),
  signale-le au lieu de forcer un test fragile.
- Ne mocke que ce qui est nécessaire (services externes, I/O).
```

#### Skill `/doc` — Documentation automatique

**`.claude/skills/doc/SKILL.md`** :
```yaml
---
name: doc
description: Génère ou met à jour la documentation technique
argument-hint: "[fichier ou module]"
user-invocable: true
allowed-tools: "Read Grep Glob Write"
---

Documente $ARGUMENTS.

## Procédure

1. Lis le code source et identifie les éléments publics (API, fonctions,
   classes, interfaces).
2. Lis la documentation existante (si elle existe) pour maintenir
   la cohérence de style.
3. Génère la documentation en incluant :
   - Description du rôle et de la responsabilité
   - Paramètres d'entrée avec types et contraintes
   - Valeur de retour
   - Exceptions possibles
   - Exemple d'utilisation
4. Si c'est un module, génère un README.md avec vue d'ensemble,
   dépendances, et exemples.

## Règles

- Documente le **pourquoi**, pas le **quoi** (le code dit le quoi).
- Pas de documentation triviale ("Cette méthode retourne l'utilisateur"
  sur `getUser()`).
- Documente les cas limites et les pièges non évidents.
```

#### Skill `/new-feature` — Scaffolding guidé

**`.claude/skills/new-feature/SKILL.md`** :
```yaml
---
name: new-feature
description: Guide la création d'une nouvelle feature de bout en bout
argument-hint: "[description de la feature]"
user-invocable: true
---

L'utilisateur veut implémenter une nouvelle feature : $ARGUMENTS

## Procédure OBLIGATOIRE

### Étape 1 : Comprendre
Avant tout, pose ces questions si les réponses ne sont pas évidentes :
- Quel est le comportement attendu du point de vue utilisateur ?
- Quels modules existants sont impactés ?
- Y a-t-il des contraintes spécifiques (performance, sécurité, compatibilité) ?

### Étape 2 : Analyser
- Explore le code existant pour identifier où intégrer la feature.
- Identifie les patterns utilisés dans le projet pour les features similaires.
- Liste les fichiers qui devront être créés ou modifiés.

### Étape 3 : Planifier
Propose un plan d'implémentation en étapes, chacune testable indépendamment.
Attends la validation avant de commencer.

### Étape 4 : Implémenter
Procède étape par étape. Après chaque étape :
- Lance les tests existants pour vérifier l'absence de régression.
- Résume ce qui a été fait.
- Demande confirmation avant de passer à l'étape suivante.

### Étape 5 : Valider
- Lance la suite de tests complète.
- Fais une auto-review avec la checklist du projet.
- Résume les modifications effectuées et les points d'attention.
```

### Variables disponibles dans les skills

| Variable | Description |
|----------|-------------|
| `$ARGUMENTS` | Tous les arguments passés après le `/nom` |
| `$1`, `$2`... | Arguments individuels par position |
| `${CLAUDE_SKILL_DIR}` | Répertoire contenant le SKILL.md |
| `${CLAUDE_SESSION_ID}` | Identifiant de la session |

### Injection dynamique de contexte

Les skills peuvent exécuter des commandes shell pour injecter du contexte dynamique :

```yaml
---
name: status
description: État du projet
user-invocable: true
---

# État actuel du projet

Branche courante et fichiers modifiés :
```!
git branch --show-current
git status --short
```

Derniers commits :
```!
git log --oneline -5
```

Analyse la situation et résume l'état du travail en cours.
```

La sortie des blocs `` ```! `` est exécutée **avant** que Claude voie le prompt, et le résultat est injecté dans le contexte.

---

## 9.4 — La mémoire : capitaliser sur l'expérience

### Les deux systèmes de mémoire

| Aspect | CLAUDE.md | Mémoire automatique |
|--------|-----------|---------------------|
| **Qui écrit** | Vous (et l'équipe) | Claude, à votre demande ou de sa propre initiative |
| **Contenu** | Instructions, conventions, interdits | Apprentissages, préférences, retours d'expérience |
| **Portée** | Projet ou utilisateur | Par projet, par utilisateur |
| **Chargement** | Intégral, à chaque session | Index chargé (200 lignes max), fichiers lus à la demande |
| **Usage** | Règles à suivre | Contexte pour mieux travailler |

### Où est stockée la mémoire

```
~/.claude/projects/<identifiant-projet>/memory/
├── MEMORY.md              # Index (chargé automatiquement)
├── feedback_testing.md     # Fichier thématique
├── project_auth_refactor.md
└── user_preferences.md
```

Le `MEMORY.md` est un index de pointeurs vers les fichiers thématiques. Seul l'index est chargé automatiquement ; les fichiers détaillés sont lus à la demande.

### Utiliser la mémoire efficacement

#### Demander à Claude de retenir

```
« Retiens que dans ce projet, les migrations EF Core doivent toujours 
  être créées avec le flag --project Infrastructure et --startup-project API. »

« Retiens que le client préfère les noms de variables en français 
  dans le code métier (domaine bancaire, vocabulaire réglementé). »
```

#### Corriger et mémoriser

```
« Non, on ne mocker jamais la base dans les tests d'intégration — 
  on utilise une instance PostgreSQL Testcontainers. 
  Retiens ça pour les prochaines fois. »
```

#### Ce qu'il faut mémoriser vs ce qu'il ne faut pas

| À mémoriser | À ne pas mémoriser |
|------------|-------------------|
| Préférences non déductibles du code | Structure du projet (l'agent peut l'explorer) |
| Retours d'expérience (« ça a marché / pas marché ») | Contenu des fichiers (l'agent peut les lire) |
| Décisions d'équipe (« on a choisi X plutôt que Y parce que Z ») | Git history (l'agent peut consulter le log) |
| Informations sur les systèmes externes | Tâches en cours (utiliser les todos) |

### Maintenance de la mémoire

La mémoire peut devenir obsolète. Vérifiez-la périodiquement :

```
« Montre-moi ce que tu as en mémoire pour ce projet. 
  Y a-t-il des éléments obsolètes ? »
```

Accès direct via la commande `/memory` dans Claude Code.

---

## 9.5 — Les permissions : contrôler ce que l'agent peut faire

### Le système de permissions

En l'absence de fichier `.claudeignore` natif, Claude Code utilise un système de **règles de permission** pour contrôler l'accès aux fichiers et aux commandes.

### Configuration des restrictions

**`.claude/settings.json`** (partagé avec l'équipe) :

```json
{
  "permissions": {
    "deny": [
      "Read(.env*)",
      "Read(**/*.key)",
      "Read(**/*.pem)",
      "Read(**/*.pfx)",
      "Read(**/credentials/**)",
      "Read(**/secrets/**)",
      "Edit(.env*)",
      "Write(.env*)",
      "Bash(rm -rf *)",
      "Bash(git push --force*)",
      "Bash(git reset --hard*)",
      "Bash(drop table*)",
      "Bash(drop database*)"
    ],
    "allow": [
      "Read(src/**)",
      "Read(tests/**)",
      "Read(docs/**)",
      "Bash(npm test*)",
      "Bash(npm run lint*)",
      "Bash(dotnet test*)",
      "Bash(git status)",
      "Bash(git diff*)",
      "Bash(git log*)"
    ]
  }
}
```

### Patterns de restriction recommandés par type de projet

#### Projet avec base de données

```json
{
  "permissions": {
    "deny": [
      "Bash(*DROP*)",
      "Bash(*TRUNCATE*)",
      "Bash(*DELETE FROM*WHERE 1=1*)",
      "Bash(dotnet ef database drop*)",
      "Bash(prisma migrate reset*)"
    ]
  }
}
```

#### Projet avec infrastructure (Docker, Kubernetes)

```json
{
  "permissions": {
    "deny": [
      "Bash(docker system prune*)",
      "Bash(docker rm -f*)",
      "Bash(kubectl delete*)",
      "Edit(**/docker-compose.prod.yml)",
      "Edit(**/*Dockerfile.prod*)",
      "Edit(**/k8s/production/**)"
    ]
  }
}
```

---

## 9.6 — Assembler le tout : la configuration modèle

Voici une structure complète pour un projet d'équipe :

```
mon-projet/
├── CLAUDE.md                              # Instructions projet (bonnes pratiques + stack)
├── CLAUDE.local.md                        # Préférences personnelles (gitignored)
├── .claude/
│   ├── settings.json                      # Permissions et hooks (partagé)
│   ├── settings.local.json                # Overrides personnels (gitignored)
│   ├── hooks/
│   │   ├── block-destructive.sh           # Bloque les commandes dangereuses
│   │   └── auto-lint.sh                   # Lint automatique après modification
│   └── skills/
│       ├── review/
│       │   └── SKILL.md                   # /review — Review structurée
│       ├── test/
│       │   └── SKILL.md                   # /test — Génération de tests guidée
│       ├── doc/
│       │   └── SKILL.md                   # /doc — Documentation automatique
│       └── new-feature/
│           └── SKILL.md                   # /new-feature — Scaffolding guidé
├── .gitignore                             # Inclut CLAUDE.local.md et settings.local.json
└── ...
```

### Checklist de mise en place

- [ ] Copier le template CLAUDE.md du chapitre 8 et l'adapter à la stack du projet
- [ ] Créer un `CLAUDE.local.md` pour les préférences personnelles
- [ ] Configurer les permissions (deny) pour les fichiers sensibles et commandes dangereuses
- [ ] Ajouter au moins un hook de sécurité (blocage commandes destructives)
- [ ] Créer les skills `/review` et `/test` adaptées au projet
- [ ] Ajouter `CLAUDE.local.md` et `.claude/settings.local.json` au `.gitignore`
- [ ] Documenter la configuration dans le README ou le wiki du projet
- [ ] Former l'équipe aux commandes disponibles (`/review`, `/test`, etc.)

---

## 9.7 — Équivalents dans les autres outils

| Fonctionnalité | Claude Code | Cursor | GitHub Copilot | ChatGPT |
|---------------|-------------|--------|----------------|---------|
| Instructions projet | `CLAUDE.md` | `.cursorrules` | `.github/copilot-instructions.md` | Custom Instructions |
| Instructions perso | `~/.claude/CLAUDE.md` | Settings Cursor | Settings VS Code | Custom Instructions |
| Hooks | `settings.json` hooks | Non disponible | Non disponible | Non disponible |
| Skills / commandes | `.claude/skills/` | Non disponible | Non disponible | GPTs personnalisés |
| Mémoire persistante | Mémoire automatique | Notepad (bêta) | Non disponible | Memory |
| Exclusion de fichiers | Permissions deny | `.cursorignore` | Non disponible | N/A |

> **Constat** : à date, Claude Code est l'outil le plus configurable. Mais les principes (donner du contexte, poser des garde-fous, créer des workflows réutilisables) s'appliquent quel que soit l'outil, avec les moyens disponibles.

---

## À retenir

1. **Le CLAUDE.md est votre levier principal.** Partez du template fourni (chapitre 8) et enrichissez-le avec les spécificités de votre stack.
2. **Les hooks automatisent les garde-fous** que vous ne voulez pas laisser à la discipline individuelle.
3. **Les skills créent des workflows réutilisables** partagés avec l'équipe via git.
4. **La mémoire capitalise sur l'expérience** : corrections, préférences, décisions d'équipe.
5. **Les permissions protègent** les fichiers sensibles et bloquent les commandes dangereuses.
6. **Tout se versionne.** CLAUDE.md, hooks, skills, settings : le projet porte sa configuration IA comme il porte son `.eslintrc` ou son `Dockerfile`.

---

*Chapitre précédent : [Annexe — Mise en pratique automatisée](08_annexe_mise_en_pratique.md) | Retour au [sommaire](README.md)*
