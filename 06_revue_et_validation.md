# Chapitre 6 — Revue et validation du code assisté par IA

## Principe directeur

> Le code généré par IA est du code non vérifié jusqu'à preuve du contraire. Il mérite le même niveau de scrutin qu'un pull request d'un contributeur externe dont on ne connaît pas le niveau.

Ce chapitre fournit un cadre méthodique pour valider le code produit avec l'assistance de l'IA, qu'il soit entièrement généré ou co-écrit.

---

## 6.1 — Pourquoi la validation du code IA est différente

### Les biais spécifiques au code généré par IA

| Biais | Description |
|-------|-------------|
| **Biais de complaisance** | Le code semble propre et bien structuré → on baisse la garde |
| **Biais de vitesse** | Le code a été produit rapidement → on le valide rapidement |
| **Biais d'autorité** | « L'IA l'a écrit, elle doit savoir » → on ne challenge pas |
| **Biais de volume** | L'IA a produit beaucoup de code → on ne lit pas tout |
| **Biais de confirmation** | Le code compile et les tests passent → il doit être correct |

### La différence avec une review classique

Dans une review classique, le code a été écrit par un humain qui :
- Comprend le contexte métier.
- Connaît les conventions du projet (en général).
- A fait des choix conscients.
- Peut expliquer son raisonnement.

Le code IA n'a aucune de ces garanties. Il a été produit par un modèle statistique qui génère la suite la plus probable. Il peut être techniquement correct et fonctionnellement inadapté.

---

## 6.2 — La checklist de validation

### Avant d'intégrer du code généré par IA, vérifiez systématiquement :

#### Niveau 1 — Compréhension (obligatoire)

- [ ] **Je comprends chaque ligne de ce code.** Je pourrais l'expliquer à un collègue.
- [ ] **Je comprends pourquoi cette approche a été choisie** plutôt qu'une autre.
- [ ] **J'ai identifié les hypothèses implicites** du code (format des données, état attendu, droits).

#### Niveau 2 — Correction fonctionnelle (obligatoire)

- [ ] **Le code fait ce qui est demandé.** Pas seulement « il compile » — il répond au besoin métier.
- [ ] **Les cas limites sont gérés** : entrées nulles, listes vides, valeurs extrêmes, concurrence.
- [ ] **La gestion des erreurs est appropriée** : pas de catch silencieux, pas d'exception avalée.
- [ ] **Les types et les contrats sont respectés** : les interfaces, les types de retour, les validations.

#### Niveau 3 — Qualité technique (recommandé)

- [ ] **Le code suit les conventions du projet** : nommage, structure, patterns.
- [ ] **Aucune dépendance inutile n'a été ajoutée.**
- [ ] **Le code ne duplique pas une fonctionnalité existante.**
- [ ] **La performance est acceptable** pour le contexte d'utilisation.
- [ ] **Le code est testable** : pas de dépendances cachées, injection de dépendances respectée.

#### Niveau 4 — Sécurité (critique pour le code exposé)

- [ ] **Pas d'injection possible** : SQL, commandes, XSS.
- [ ] **Les entrées utilisateur sont validées et assainies.**
- [ ] **Les secrets ne sont pas en dur dans le code.**
- [ ] **Les autorisations sont vérifiées** avant chaque opération sensible.
- [ ] **Les données sensibles ne sont pas loguées.**

#### Niveau 5 — Intégration (avant merge)

- [ ] **Les tests existants passent toujours.**
- [ ] **De nouveaux tests couvrent le code ajouté.**
- [ ] **La documentation est à jour** si le comportement public a changé.
- [ ] **Le code est cohérent avec l'architecture existante.**

---

## 6.3 — Techniques de validation

### Technique 1 : La lecture ligne par ligne

La méthode la plus simple et la plus efficace. Lisez le code comme si c'était un pull request d'un inconnu.

**Astuce** : utilisez `git diff` ou le diff de votre IDE plutôt que de relire le fichier entier. Concentrez-vous sur ce qui a changé.

### Technique 2 : Le « rubber duck » inversé

Au lieu d'expliquer votre code à un canard en plastique, demandez à l'IA d'expliquer le code qu'elle a écrit — puis vérifiez que son explication correspond à votre compréhension.

```
── Prompt ──
« Explique-moi la logique de la méthode processRefund() que tu viens 
  d'écrire. Pour chaque étape, dis-moi pourquoi tu as fait ce choix 
  plutôt qu'un autre. »
```

Si l'explication révèle un choix que vous n'aviez pas vu ou compris, creusez.

### Technique 3 : Le test contradictoire

Demandez à l'IA (ou écrivez vous-même) des tests qui devraient faire **échouer** le code, puis vérifiez qu'il échoue bien.

```
── Prompt ──
« Écris des tests qui essaient de casser la méthode processRefund() :
  - Remboursement d'un montant supérieur au total de la commande
  - Remboursement en double (même commande, deux appels consécutifs)
  - Remboursement sur une commande déjà annulée
  - Montant négatif
  
  Indique pour chaque test le comportement attendu (exception, rejet, etc.). »
```

### Technique 4 : La review croisée humaine

Intégrez systématiquement un reviewer humain dans le processus, même (surtout) quand le code a été assisté par IA.

**Point important** : informez le reviewer que le code a été assisté par IA. Cela l'alerte sur les biais spécifiques à vérifier (hallucinations, over-engineering, dépendances fantômes).

### Technique 5 : L'audit des dépendances

Quand l'IA a ajouté des imports ou des packages :

```
── Prompt ──
« Liste toutes les nouvelles dépendances que tu as ajoutées. 
  Pour chacune :
  1. Pourquoi est-elle nécessaire ?
  2. Peut-on faire sans (avec du code natif) ?
  3. Est-elle activement maintenue ?
  4. Quelle est sa taille (impact sur le bundle) ? »
```

---

## 6.4 — Les red flags : quand redoubler de vigilance

Certains signaux doivent déclencher une inspection approfondie :

| Red flag | Pourquoi c'est suspect |
|----------|----------------------|
| L'IA a modifié un fichier que vous n'aviez pas mentionné | Elle a peut-être touché quelque chose d'inattendu |
| Le code contient un `try/catch` qui avale l'exception | L'IA masque un problème au lieu de le traiter |
| L'IA a ajouté un `// TODO` ou un `// FIXME` | Le code est incomplet |
| Le code contient des valeurs magiques (nombres sans explication) | Logique opaque |
| L'IA utilise `any` (TypeScript) ou `object` (C#) | Perte de typage, risque runtime |
| Le code désactive un linter ou un check de sécurité | L'IA contourne un garde-fou |
| L'IA a réécrit du code existant « pour améliorer » sans qu'on le demande | Risque de régression sur du code stable |
| Les tests ne testent que le happy path | Fausse impression de couverture |

---

## 6.5 — Intégrer la validation dans le flux de travail de l'équipe

### Proposition de processus

```
┌─────────────────────────────────────────────────────────┐
│                   FLUX DE VALIDATION                     │
│                                                          │
│  1. Développeur + IA → Code produit                      │
│                ↓                                         │
│  2. Auto-review (checklist Niv.1-3)                      │
│                ↓                                         │
│  3. Tests automatisés (CI/CD)                            │
│                ↓                                         │
│  4. Review IA (optionnelle, 2e passe)                    │
│                ↓                                         │
│  5. Review humaine (pair review, obligatoire)            │
│                ↓                                         │
│  6. Merge                                                │
└─────────────────────────────────────────────────────────┘
```

### Règles d'équipe suggérées

1. **Tout code assisté par IA passe par le même processus de review** que le code écrit manuellement. Pas de voie rapide.
2. **Le développeur qui soumet le code en est responsable**, quelle que soit la proportion générée par IA.
3. **Les modifications sur du code critique** (sécurité, paiement, données personnelles) nécessitent une review renforcée, avec ou sans IA.
4. **L'utilisation de l'IA est transparente.** Si le code a été significativement assisté par IA, le mentionner dans le PR est une bonne pratique (pas une obligation systématique, mais une courtoisie envers les reviewers).

---

## 6.6 — Automatiser les vérifications

### Outils complémentaires à l'IA

| Outil | Ce qu'il vérifie | Complémentarité avec l'IA |
|-------|-------------------|---------------------------|
| **Linter** (ESLint, Pylint, Roslyn) | Style, conventions, erreurs statiques | L'IA peut produire du code non conforme |
| **Analyseur statique** (SonarQube, CodeQL) | Bugs, vulnérabilités, code smells | Détecte ce que la review manuelle rate |
| **Tests automatisés** | Comportement fonctionnel | Vérifie que le code fait ce qu'on attend |
| **Dependency checker** (Dependabot, Snyk) | Vulnérabilités dans les dépendances | Vérifie les packages ajoutés par l'IA |
| **Type checker** (tsc, mypy) | Cohérence des types | Attrape les incohérences de typage |

### Configuration recommandée

- **Pre-commit hooks** : linter + type checker → bloque le commit si le code n'est pas conforme.
- **CI/CD** : tests + analyseur statique + dependency check → bloque le merge si des problèmes sont détectés.
- **Review obligatoire** : au moins un reviewer humain avant merge.

Ces outils forment un filet de sécurité qui attrape mécaniquement les erreurs les plus courantes du code IA.

---

## À retenir

1. **Le code IA est du code non vérifié.** Traitez-le comme un pull request d'un inconnu.
2. **Utilisez la checklist systématiquement.** Compréhension → Correction → Qualité → Sécurité → Intégration.
3. **Connaissez les red flags** qui indiquent un code à inspecter de plus près.
4. **Intégrez la validation dans le processus d'équipe** : pas de voie rapide pour le code IA.
5. **Automatisez ce qui peut l'être** : linter, tests, analyse statique sont vos alliés.
6. **La review humaine reste indispensable.** L'IA ne remplace pas le jugement d'un pair.

---

*Chapitre précédent : [Travailler avec un agent IA](05_travailler_avec_un_agent.md) | Chapitre suivant : [Éthique et responsabilité](07_ethique_et_responsabilite.md)*
