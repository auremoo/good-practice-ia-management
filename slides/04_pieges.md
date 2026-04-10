---
marp: true
theme: corporate-ia
paginate: true
header: "Guide IA & Développement"
footer: "Chapitre 4 — Les pièges classiques"
---

<!-- _class: lead -->

# Chapitre 4 — Les pièges classiques

### Connaître les limites pour mieux les éviter

---

# 8 pièges à connaître

1. Hallucinations
2. Over-engineering
3. Fuite de données
4. Dette technique invisible
5. Cargo cult du prompt
6. Dépendance à l'outil
7. Propagation d'erreurs
8. Contexte perdu

---

# Piège 1 — Les hallucinations

L'IA peut **inventer** des informations et les présenter comme des faits.

| Type | Exemple |
|------|---------|
| API inexistante | `response.getStatusMessage()` — n'existe pas |
| Librairie fictive | « Utilisez `express-jwt-validator` » — n'existe pas |
| Version inventée | « Depuis NestJS 4.2... » — cette feature n'existe pas |
| Comportement faux | « `Array.sort()` trie numériquement » — non, lexicographiquement |

---

# Hallucinations — Se protéger

```
L'IA dit : « Utilisez express.rateLimit() »

Réalité : express.rateLimit() n'existe pas.
Il faut le package tiers express-rate-limit.
```

### Parades
1. **Vérifier les API/méthodes** dans la doc officielle
2. **Vérifier les packages** sur npm/NuGet/PyPI avant installation
3. **Tester le code** — ne pas se fier à « ça a l'air correct »
4. **Croiser les sources** quand c'est surprenant

---

# Piège 2 — L'over-engineering

L'IA propose systématiquement des solutions **plus complexes que nécessaire**.

| Demande simple | Réponse IA |
|----------------|------------|
| Script de migration | Framework avec plugins, rollback, retry |
| Endpoint CRUD | Repository, Unit of Work, DTO, validation pipeline |
| Parser un CSV | Bibliothèque générique multi-format |

### Parade
```
« Donne la solution la plus simple qui fonctionne.
  Pas de pattern supplémentaire. »
```

---

# Piège 3 — Fuite de données sensibles

### Ce qu'on oublie de vérifier avant de coller dans un prompt

- Clés d'API, tokens, secrets dans le code
- Données personnelles de vrais clients
- Code propriétaire sous clause de confidentialité

### Règle

```
❌  const apiKey = "sk-proj-abc123def456...";
✅  const apiKey = process.env.API_KEY;

❌  { "email": "jean.dupont@client-reel.fr" }
✅  { "email": "test@example.com" }
```

**Anonymisez toujours.**

---

# Piège 4 — La dette technique invisible

Le code IA fonctionne mais accumule de la dette que vous ne voyez pas :

- **Dépendances inutiles** ajoutées pour une tâche triviale
- **Patterns inappropriés** pour le contexte
- **Code non idiomatique** (C# écrit avec des patterns Java)
- **Logique dupliquée** (l'IA ne connaît pas vos utilitaires existants)

### Parade
Vérifiez les **imports**, comparez avec le **style du projet**, cherchez les **doublons**.

---

# Piège 5 — Le cargo cult du prompt

| Technique aveugle | Réalité |
|-------------------|---------|
| « Tu es un expert senior » | Cadre le niveau, mais ne rend pas l'IA plus compétente |
| « Pense étape par étape » | Utile pour le raisonnement, inutile pour du code simple |
| « Respire profondément » | Aucun effet |
| IMPORTANT en majuscules partout | Dilue l'emphase |

**Comprendre pourquoi une technique marche avant de l'utiliser.**

---

# Piège 6 — La dépendance

### Symptômes
- Impossible de commencer sans « demander à l'IA »
- Difficulté croissante à écrire du code de zéro
- Abandon de l'apprentissage des fondamentaux

### Conséquences
- **Entretien technique** : incapable de résoudre sans aide
- **Situation de crise** : paralysé sans l'outil
- **Progression** : stagnation masquée

### Parade
Coder régulièrement **sans IA**. Kata, mob programming, lecture de code.

---

# Piège 7 — La propagation d'erreurs

```
Étape 1 : L'IA génère un calcul de TVA avec erreur d'arrondi
Étape 2 : L'IA génère les tests → valident le comportement incorrect
Étape 3 : L'IA documente → décrit le comportement incorrect
Étape 4 : Un collègue reproduit l'erreur ailleurs
```

### Parade

> **Ne pas valider l'IA par l'IA.**

- Écrire les tests en premier (TDD)
- Faire relire par un humain
- Vérifier les calculs critiques manuellement

---

# Résumé express

| Piège | Parade en un mot |
|-------|-----------------|
| Hallucinations | **Vérifier** |
| Over-engineering | **Simplifier** |
| Fuite de données | **Anonymiser** |
| Dette invisible | **Comprendre** |
| Cargo cult | **Raisonner** |
| Dépendance | **Pratiquer** |
| Propagation | **Croiser** |
| Contexte perdu | **Rappeler** |


---

<!-- _class: end -->

# Merci

> **L'IA est un amplificateur. Le facteur déterminant, c'est vous.**

Guide complet disponible sur le dépôt du projet
