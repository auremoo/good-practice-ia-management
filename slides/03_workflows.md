---
marp: true
theme: corporate-ia
paginate: true
header: "Guide IA & Développement"
footer: "Chapitre 3 — Workflows"
---

<!-- _class: lead -->

# Chapitre 3 — Workflows concrets

### L'IA au quotidien du développeur

---

# 7 workflows où l'IA fait la différence

1. **Debugging** assisté
2. **Code review** augmentée
3. **Refactoring** guidé
4. **Tests** automatisés
5. **Exploration** de codebase
6. **Documentation** technique
7. **Apprentissage** et montée en compétence

---

# 1. Debugging assisté

### L'IA excelle à
- Analyser un stack trace rapidement
- Proposer plusieurs hypothèses classées par probabilité
- Explorer un flux de données à travers plusieurs fichiers

### L'IA est mauvaise pour
- Comprendre l'intention métier derrière le code
- Diagnostiquer des problèmes d'environnement
- Reproduire des bugs de concurrence subtils

---

# Debugging — Bon prompt

```
Le test OrderServiceTests.CreateOrder_WithDiscount échoue :

  Expected: 85.50
  Actual: 90.00

Analyse le calcul du prix dans OrderService et
identifie pourquoi la remise n'est pas appliquée.
```

### Anti-pattern

```
❌  « Ça ne marche pas, corrige. »
```

**Donnez** : comportement attendu vs observé, conditions, ce que vous avez déjà tenté.

---

# 2. Code review augmentée

> La review IA **complète** la review humaine. Elle ne la **remplace** pas.

### L'IA détecte bien
Incohérences de style, cas limites, failles de sécurité courantes, code mort

### La review humaine reste indispensable pour
Pertinence métier, cohérence architecturale, décisions de design

---

# Code review — Bon prompt

```
Review le fichier PaymentService.ts. Focus sur :
1. Sécurité (données carte bancaire)
2. Gestion d'erreurs (provider qui ne répond pas)
3. Cas limites (montant négatif, devise non supportée)

Ne commente pas le style, on a un linter pour ça.

Format : Sévérité / Fichier:ligne / Problème / Suggestion
```

---

# 3. Refactoring guidé

### La bonne séquence

```
Prompt 1 : « Analyse ce fichier. Liste les problèmes
            de conception. Ne corrige rien. »

Prompt 2 : « Propose un plan de refactoring en étapes.
            Chaque étape doit être autonome. »

Prompt 3 : « Applique l'étape 1. Lance les tests. »
```

### Règle absolue
> **Jamais de refactoring sans filet de tests.**

---

# 4. Tests — Ce que l'IA fait bien (et mal)

| Fait bien | Fait mal |
|-----------|----------|
| Structure de tests rapide | Comprendre le comportement **métier** à tester |
| Identifier des cas limites | Juger si un mock est approprié |
| Données de test réalistes | Écrire des tests qui survivent au refactoring |
| Couvrir les chemins d'erreur | Distinguer implémentation vs comportement |

---

# Tests — Bon prompt

```
Tests unitaires pour calculateShippingCost()
dans ShippingService.ts.

Scénarios :
- Standard France métropolitaine
- Express
- DOM-TOM (surcoût)
- Poids > 30kg (tarif spécial)
- Commande > 100€ (livraison gratuite)
- Adresse invalide (exception)

Framework : Jest. Mocker PricingApiClient.
Nommer en français : "devrait [résultat] quand [condition]"
```

---

# 5. Explorer une codebase inconnue

L'un des **meilleurs cas d'usage** de l'IA.

```
Prompt 1 : « Architecture globale, technos, modules,
             point d'entrée. »

Prompt 2 : « Trace le flux d'une commande e-commerce,
             de la requête HTTP à l'insertion en BDD. »

Prompt 3 : « Services externes : APIs, BDD, cache,
             queues. Où sont les connexions configurées ? »
```

100 000 lignes explorées en **minutes** au lieu de **jours**.

---

# 6. Documentation & 7. Apprentissage

### Documentation
L'IA structure bien l'info technique mais reste **fonctionnellement superficielle**.
Enrichissez toujours avec le **contexte métier**.

### Apprentissage
L'usage le plus sous-estimé. L'IA est un **tuteur patient et adaptable**.

```
Astuce : reformulez avec vos mots et demandez
à l'IA de valider votre compréhension.
Cela force l'assimilation.
```

---

# Résumé

| Workflow | Valeur IA | Vigilance |
|----------|-----------|-----------|
| Debug | Analyse rapide, hypothèses | Vérifier le fix |
| Review | Détection systématique | Review humaine aussi |
| Refacto | Propositions structurées | Tests obligatoires |
| Tests | Couverture rapide | Comportement, pas implémentation |
| Exploration | Synthèse d'une codebase | Vérifier les affirmations |
| Documentation | Structure claire | Ajouter le contexte métier |
| Apprentissage | Tuteur patient | Reformuler pour assimiler |


---

<!-- _class: end -->

# Merci

> **L'IA est un amplificateur. Le facteur déterminant, c'est vous.**

Guide complet disponible sur le dépôt du projet
