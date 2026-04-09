# Cheat Sheet — IA & Développement : les bonnes pratiques

> À imprimer, afficher, garder sous la main.

---

## LA RÈGLE D'OR

```
L'IA est ton assistant. Tu es le développeur.
Ne merge jamais du code que tu ne pourrais pas réécrire toi-même.
```

---

## PROMPTER EFFICACEMENT — Méthode CROP

| | Composante | Question à se poser |
|-|------------|---------------------|
| **C** | Contexte | Quel projet, quelle stack, quelle architecture ? |
| **R** | Résultat | Qu'est-ce que je veux obtenir précisément ? |
| **O** | Objectif | Pourquoi j'en ai besoin (le besoin métier) ? |
| **P** | Précisions | Quelles contraintes, quel format, quel style ? |

```
❌  « Fais-moi un endpoint pour les users. »
✅  « Dans l'API .NET 8 (Clean Architecture, EF Core, PostgreSQL),
     crée un POST /api/users avec validation email + mot de passe 8 chars,
     gestion doublon 409, hash BCrypt, retour 201. Pattern CQRS avec MediatR. »
```

---

## LES 5 RÉFLEXES AVANT DE MERGER

- [ ] **Je comprends** chaque ligne — je pourrais l'expliquer
- [ ] **Ça fait** ce qui est demandé (pas juste "ça compile")
- [ ] **Les cas limites** sont gérés (null, vide, erreur, concurrence)
- [ ] **Pas de surprise** : dépendances ajoutées ? fichiers inattendus modifiés ?
- [ ] **Les tests passent** — existants ET nouveaux

---

## LES 8 PIÈGES À CONNAÎTRE

| Piège | Parade express |
|-------|---------------|
| **Hallucination** (API/lib/flag inventé) | Vérifier dans la doc officielle |
| **Over-engineering** | « Donne la solution la plus simple » |
| **Fuite de données** | Jamais de secrets ni de vraies données dans un prompt |
| **Dette invisible** | Vérifier imports, dépendances, doublons |
| **Cargo cult** | Comprendre *pourquoi* une technique marche |
| **Dépendance** | Coder régulièrement sans IA |
| **Propagation d'erreurs** | Ne pas valider l'IA par l'IA |
| **Contexte perdu** | Rappeler les contraintes, découper les sessions |

---

## STRUCTURER UNE SESSION AGENT

```
1. CADRER     →  « Je travaille sur [feature/bug]. Contexte : [description]. »
2. EXPLORER   →  « Montre-moi comment ça marche actuellement. »
3. PLANIFIER  →  « Propose un plan en étapes testables. »
4. EXÉCUTER   →  Étape par étape, valider chacune.
5. VALIDER    →  « Lance tous les tests. Fais une auto-review. »
```

---

## PROMPT VAGUE → QUESTIONS À POSER

| L'utilisateur dit... | Demander... |
|----------------------|-------------|
| « Optimise ça » | Performance ? Lisibilité ? Mémoire ? |
| « Fais les tests » | Quels scénarios ? Quel framework ? Quel fichier ? |
| « Corrige le bug » | Comportement attendu vs observé ? Message d'erreur ? |
| « Améliore ce code » | Qu'est-ce qui ne va pas dans l'état actuel ? |
| « Crée un service complet » | Découper : quelles sont les étapes ? |

---

## CONFIGURATION CLAUDE CODE — L'ESSENTIEL

| Fichier | Rôle | Partagé ? |
|---------|------|-----------|
| `CLAUDE.md` | Instructions projet (stack, conventions, interdits) | Oui (git) |
| `CLAUDE.local.md` | Préférences personnelles | Non |
| `.claude/settings.json` | Permissions, hooks | Oui (git) |
| `.claude/skills/*/SKILL.md` | Commandes réutilisables (`/review`, `/test`) | Oui (git) |

---

## SÉCURITÉ — LES 4 JAMAIS

```
1. JAMAIS de secrets dans un prompt     (clés API, tokens, mots de passe)
2. JAMAIS de vraies données clients     (anonymiser d'abord)
3. JAMAIS merger sans comprendre        (« ça compile » ≠ « c'est correct »)
4. JAMAIS valider l'IA par l'IA         (review humaine obligatoire)
```

---

## QUAND UTILISER L'IA vs QUAND RÉFLÉCHIR SOI-MÊME

| IA = forte valeur | IA = prudence requise |
|-------------------|-----------------------|
| Tests unitaires | Architecture système |
| Boilerplate / CRUD | Choix de technologie |
| Explorer une codebase | Code sécurité / crypto |
| Debug avec stack trace | Décisions irréversibles (migration, suppression) |
| Documentation | Optimisation perf sans données réelles |
| Apprendre un concept | Arbitrages humains (délais, priorités) |

---

## LA PHRASE À RETENIR

> **L'IA est un amplificateur. Elle amplifie la compétence du développeur compétent. Elle amplifie aussi l'incompétence. Le facteur déterminant, c'est vous.**

---

*Issu du guide complet : [Bonnes pratiques IA & Développement](README.md)*
