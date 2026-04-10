---
marp: true
theme: corporate-ia
paginate: true
header: "Guide IA & Développement"
footer: "Chapitre 2 — L'art du prompt"
---

<!-- _class: lead -->

# Chapitre 2 — L'art du prompt

### Communiquer efficacement avec une IA

---

# Le principe

> La qualité de la réponse est le reflet direct de la qualité de la demande.

Un prompt n'est pas une commande.
C'est une **communication structurée** avec un système qui n'a **aucun contexte implicite**.

---

# La méthode CROP

| | Composante | Question |
|-|------------|----------|
| **C** | Contexte | Quel projet, quelle stack, quelle architecture ? |
| **R** | Résultat | Qu'est-ce que je veux obtenir précisément ? |
| **O** | Objectif | Pourquoi j'en ai besoin (besoin métier) ? |
| **P** | Précisions | Contraintes, format, style ? |

---

# Avant / Après

### Mauvais prompt
```
Fais-moi un endpoint pour créer un utilisateur.
```

### Bon prompt
```
Dans une API .NET 8 (Clean Architecture, EF Core, PostgreSQL),
crée un POST /api/users :
- Validation email + mot de passe 8 chars min
- Doublon email → 409
- Hash BCrypt avant stockage
- Retour 201 avec URI
- Pattern CQRS avec MediatR
```

---

# Les 4 erreurs les plus fréquentes

| Erreur | Exemple |
|--------|---------|
| **Trop vague** | « Optimise ce code » |
| **Sans contexte** | « Ajoute des tests » |
| **Tout d'un coup** | « Crée une app complète avec auth, BDD, API et front » |
| **Sans format** | « Explique-moi le middleware » |

---

# Erreur 1 — Trop vague

```
❌  « Optimise ce code. »
```

Optimiser **quoi** ? Vitesse ? Lisibilité ? Mémoire ?

```
✅  « Ce endpoint met 3s pour 10k enregistrements.
     Voici la requête SQL. Identifie les causes
     de lenteur et propose des optimisations
     en priorisant la performance de lecture. »
```

---

# Erreur 2 — Tout d'un coup

```
❌  « Crée une app complète de gestion de tâches
     avec auth, BDD, API REST et front React. »
```

Résultat : code superficiel, incohérent, inutilisable.

```
✅  Séquence de prompts :
    1. Modèle de données (User, Task, Project)
    2. Endpoints CRUD pour Task
    3. Authentification JWT
    4. ...
```

**Découpez toujours.**

---

# Le prompting itératif

Le premier résultat n'est (presque) jamais le bon.

```
Prompt 1 → Résultat approximatif
Prompt 2 → « Proche, mais j'ai besoin que... »
Prompt 3 → « Bonne approche, mais cette partie... »
Prompt 4 → « Parfait, ajoute juste [cas limite]. »
```

Le prompting est un **dialogue**, pas une commande unique.

---

# Techniques avancées

### Donner des exemples (few-shot)
```
Génère des logs en suivant CE format existant :
logger.Information("Payment processed for Order {OrderId}...");
```

### Donner un contre-exemple
```
Ce que je ne veux PAS :
- Commentaires sur chaque ligne
- 10 micro-fonctions
- Design patterns superflus
```

### Demander de raisonner d'abord
```
Avant de coder : quelles approches possibles ?
Avantages/inconvénients de chacune ?
Puis implémente la meilleure.
```

---

# Adapter le prompt à l'outil

| Outil | Stratégie |
|-------|-----------|
| **Claude Code / Agent** | Intentions de haut niveau. L'agent explore le code lui-même. |
| **GitHub Copilot** | Commentaires descriptifs dans le code pour guider la complétion. |
| **ChatGPT / Chat web** | Tout fournir dans le prompt (code, erreur, contexte). |

---

# Agent vs Chatbot : la différence

```
── Chatbot ──
« Voici mon fichier [200 lignes]. Voici l'erreur.
  Trouve le bug. »

── Agent ──
« Le test d'inscription échoue quand l'email
  contient des accents. Diagnostique et corrige. »
```

L'agent lira les fichiers **lui-même**.
Il en explorera d'autres si nécessaire.

---

# À retenir

1. **CROP** : Contexte, Résultat, Objectif, Précisions
2. **Soyez spécifique** — plus c'est précis, plus c'est exploitable
3. **Itérez** — le prompting est un dialogue
4. **Adaptez-vous à l'outil** — agent ≠ chatbot
5. **Montrez** plutôt que décrivez — exemples > explications
6. **Découpez** — une séquence ciblée bat un méga-prompt


---

<!-- _class: end -->

# Merci

> **L'IA est un amplificateur. Le facteur déterminant, c'est vous.**

Guide complet disponible sur le dépôt du projet
