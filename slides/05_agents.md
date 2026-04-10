---
marp: true
theme: corporate-ia
paginate: true
header: "Guide IA & Développement"
footer: "Chapitre 5 — Travailler avec un agent"
---

<!-- _class: lead -->

# Chapitre 5 — Travailler avec un agent IA

### Claude Code, Cursor, et au-delà

---

# Agent vs Chatbot

| | Chatbot | Agent |
|-|---------|-------|
| **Accès au code** | Copier-coller | Accès direct au projet |
| **Exécution** | Rien | Commandes, tests, builds |
| **Modification** | Texte à copier | Modifie directement les fichiers |
| **Contexte** | Ce que vous collez | L'ensemble du projet |
| **Autonomie** | Répond | Planifie et exécute |

---

# Ce que ça change

```
── Chatbot ──
« Voici mon fichier [200 lignes].
  Voici l'erreur [stack trace].
  Trouve le bug. »

── Agent ──
« Le test d'inscription échoue quand l'email
  contient des accents. Diagnostique et corrige. »
```

L'agent lira les fichiers lui-même et en explorera d'autres si nécessaire.

---

# Structurer une session de travail

```
1. CADRER      « Je travaille sur [feature]. Contexte : ... »
      ↓
2. EXPLORER    « Comment ça marche actuellement ? »
      ↓
3. PLANIFIER   « Propose un plan en étapes testables. »
      ↓
4. EXÉCUTER    Étape par étape, valider chacune.
      ↓
5. VALIDER     « Lance les tests. Auto-review. »
```

---

# L'anti-pattern : le méga-prompt

```
❌  « Implémente l'export PDF des factures avec logo,
     coordonnées client, lignes de détail, TVA, total,
     QR code, envoi par email au client et copie compta.
     Ajoute les tests et mets à jour la doc API. »
```

Résultat : massif, difficile à valider, incohérent.

**Découpez toujours.**

---

# Le fichier CLAUDE.md

Un fichier Markdown à la racine du projet, lu automatiquement à chaque session.

```markdown
# Stack : .NET 8, EF Core, PostgreSQL
# Architecture : Clean Architecture
# Tests : xUnit + Moq, pattern Arrange-Act-Assert

# Commandes
- dotnet test → lancer les tests
- dotnet ef migrations add <Name> → migration

# Interdits
- Ne jamais modifier une migration existante
- Ne jamais utiliser DateTime.Now
```

**15 minutes de configuration = des heures gagnées.**

---

# 4 patterns de collaboration

| Pattern | Principe |
|---------|----------|
| **Pilote / Copilote** | Vous décidez la direction, l'agent exécute |
| **Explorateur** | L'agent explore, vous décidez |
| **Reviewer** | Vous écrivez, l'agent review |
| **Pair programmer** | Travail collaboratif en temps réel |

Choisissez le pattern selon la tâche.

---

# Pilote / Copilote

```
Vous : « On refactore le module paiement.
         Étape 1 : extraire l'interface. »
Agent : [exécute]
Vous : « Bien. Étape 2 : créer l'adapter Stripe. »
Agent : [exécute]
```

Vous gardez le contrôle. L'agent fait le travail mécanique.

---

# Explorateur

```
Vous : « Quelles options pour le cache
         dans ce projet ? »
Agent : [analyse, propose 3 options
         avec avantages/inconvénients]
Vous : « Option 2. Implémente. »
```

L'agent fait la recherche. Vous prenez la décision.

---

# Erreurs courantes avec les agents

| Erreur | Solution |
|--------|----------|
| Refactoring sans branche | Toujours travailler sur une branche |
| Accepter tout sans relire | Review systématique des diffs |
| Sessions trop longues (2h+) | Découper en 30-45 min |
| Pas de CLAUDE.md | 15 min d'investissement initial |
| Agent pour tâches triviales | Garder les tâches simples pour vous |

---

# À retenir

1. **Agent ≠ chatbot** — adaptez votre approche
2. **Structurez** : cadrer → explorer → planifier → exécuter → valider
3. **CLAUDE.md** est un investissement qui paie à chaque session
4. **Découpez** les tâches complexes
5. **Choisissez** votre pattern de collaboration selon la tâche


---

<!-- _class: end -->

# Merci

> **L'IA est un amplificateur. Le facteur déterminant, c'est vous.**

Guide complet disponible sur le dépôt du projet
