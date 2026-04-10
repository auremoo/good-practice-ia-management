---
marp: true
theme: corporate-ia
paginate: true
header: "Guide IA & Développement"
footer: "Chapitre 8 — Configuration"
---

<!-- _class: lead -->

# Chapitre 8 — Configurer son environnement IA

### CLAUDE.md, hooks, skills, mémoire

---

# Pourquoi configurer ?

Un agent bien configuré = un collègue bien onboardé.

| Sans config | Avec config |
|-------------|-------------|
| Redécouvre le projet à chaque session | Connaît les conventions dès le départ |
| Propose des patterns inadaptés | Suit l'architecture existante |
| Aucun garde-fou | Commandes dangereuses bloquées |
| Réponses génériques | Réponses adaptées au contexte |

**15 minutes de setup = des heures gagnées.**

---

# CLAUDE.md — La hiérarchie

| Niveau | Fichier | Portée |
|--------|---------|--------|
| **Projet** | `CLAUDE.md` | L'équipe (git) |
| **Local** | `CLAUDE.local.md` | Vous seul (gitignored) |
| **Utilisateur** | `~/.claude/CLAUDE.md` | Tous vos projets |
| **Sous-dossier** | `modules/api/CLAUDE.md` | Chargé à la demande |

Tous les niveaux sont **concaténés**, pas écrasés.

---

# CLAUDE.md — Que mettre dedans

```markdown
# Stack : .NET 8, EF Core 8, PostgreSQL
# Architecture : Clean Architecture
# Tests : xUnit + Moq

# Conventions
- PascalCase classes/méthodes, camelCase variables
- Règles métier dans Domain, jamais dans Controllers
- Tests nommés : Devrait_[résultat]_Quand_[condition]

# Commandes
- dotnet test → lancer les tests
- dotnet ef migrations add <Name> → migration

# Interdits
- Ne jamais modifier une migration existante
- Ne jamais utiliser DateTime.Now (→ IDateTimeProvider)
- Ne jamais retourner d'entité Domain depuis un Controller
```

---

# CLAUDE.md — Adapter à votre stack

Le guide fournit des **exemples complets** pour :

- **.NET / C#** — Clean Architecture, EF Core, MediatR
- **TypeScript / Node.js** — Express, Prisma, Jest
- **Python / Django** — DRF, Celery, pytest
- **React / Frontend** — Vite, TanStack Query, Tailwind

Chaque exemple couvre : stack, conventions, commandes, interdits.

→ Voir le **template prêt à l'emploi** fourni avec le guide.

---

# Hooks — Automatiser les garde-fous

Un hook = un script qui s'exécute **automatiquement** sur un événement.

| Événement | Cas d'usage |
|-----------|-------------|
| `PreToolUse` | Bloquer des commandes dangereuses |
| `PostToolUse` | Lint automatique après modification |
| `UserPromptSubmit` | Valider le prompt avant traitement |
| `Stop` | Vérification finale |

---

# Hooks — Exemple : bloquer les commandes destructives

```json
// .claude/settings.json
{
  "hooks": {
    "PreToolUse": [{
      "matcher": "Bash",
      "hooks": [{
        "type": "command",
        "command": "bash .claude/hooks/block-destructive.sh"
      }]
    }]
  }
}
```

Le script vérifie si la commande contient `rm -rf`, `DROP TABLE`, `git push --force`...
→ Si oui : **bloqué automatiquement**.

---

# Skills — Workflows réutilisables

Une skill = une commande personnalisée invocable avec `/nom`.

```
.claude/skills/
├── review/SKILL.md      →  /review
├── test/SKILL.md         →  /test
├── doc/SKILL.md          →  /doc
└── new-feature/SKILL.md  →  /new-feature
```

Versionnées avec le code. Partagées via git.
**Toute l'équipe utilise les mêmes workflows.**

---

# Skills — Exemple : `/review`

```yaml
---
name: review
description: Review de code avec checklist
allowed-tools: "Read Grep Glob"
---

Effectue une review sur $ARGUMENTS.

Checklist :
- Sécurité (injection, secrets, autorisations)
- Qualité (lisibilité, duplication, gestion d'erreurs)
- Conventions (nommage, architecture)

Format : Sévérité / Fichier:ligne / Problème / Suggestion
```

Utilisation : `/review src/services/PaymentService.ts`

---

# La mémoire

| CLAUDE.md | Mémoire automatique |
|-----------|---------------------|
| Vous écrivez | Claude écrit |
| Instructions, conventions | Apprentissages, préférences |
| Chargé intégralement | Index chargé, détails à la demande |
| Règles à suivre | Contexte pour mieux travailler |

```
« Retiens que dans ce projet, on utilise toujours
  Testcontainers pour les tests d'intégration,
  jamais de mocks sur la BDD. »
```

---

# Permissions — Protéger les fichiers sensibles

```json
{
  "permissions": {
    "deny": [
      "Read(.env*)",
      "Read(**/*.key)",
      "Read(**/*.pem)",
      "Bash(rm -rf *)",
      "Bash(git push --force*)",
      "Bash(DROP TABLE*)"
    ]
  }
}
```

L'agent ne pourra **ni lire** les secrets, **ni exécuter** de commandes destructives.

---

# La structure complète

```
mon-projet/
├── CLAUDE.md                    # Instructions projet
├── CLAUDE.local.md              # Préférences perso (gitignored)
├── .claude/
│   ├── settings.json            # Permissions + hooks
│   ├── hooks/
│   │   ├── block-destructive.sh
│   │   └── auto-lint.sh
│   └── skills/
│       ├── review/SKILL.md
│       ├── test/SKILL.md
│       └── doc/SKILL.md
└── ...
```

**Tout se versionne.** Le projet porte sa configuration IA
comme il porte son `.eslintrc`.

---

# Équivalents dans les autres outils

| Fonctionnalité | Claude Code | Cursor | Copilot |
|---------------|-------------|--------|---------|
| Instructions projet | `CLAUDE.md` | `.cursorrules` | `.github/copilot-instructions.md` |
| Hooks | `settings.json` | — | — |
| Skills | `.claude/skills/` | — | — |
| Mémoire | Auto memory | Notepad (bêta) | — |
| Exclusion fichiers | Permissions deny | `.cursorignore` | — |

---

# À retenir

1. **CLAUDE.md** = votre levier principal — partez du template fourni
2. **Hooks** = garde-fous automatiques (commandes destructives, lint)
3. **Skills** = workflows partagés via git (`/review`, `/test`)
4. **Mémoire** = capitaliser sur l'expérience
5. **Permissions** = protéger fichiers sensibles et commandes dangereuses
6. **Tout se versionne** — la config IA fait partie du projet


---

<!-- _class: end -->

# Merci

> **L'IA est un amplificateur. Le facteur déterminant, c'est vous.**

Guide complet disponible sur le dépôt du projet
