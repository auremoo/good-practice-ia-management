# Chapitre 3 — Workflows concrets pour développeurs

## Principe directeur

> L'IA ne change pas votre métier. Elle change la vitesse à laquelle vous l'exercez — à condition de l'intégrer aux bons endroits de votre flux de travail.

Ce chapitre présente des cas d'usage concrets, avec des exemples de prompts et des points de vigilance pour chacun.

---

## 3.1 — Debugging assisté par IA

### Le workflow

```
1. Reproduire le bug → 2. Collecter les informations → 3. Soumettre à l'IA → 4. Analyser la suggestion → 5. Vérifier le fix
```

### Ce que l'IA fait bien

- Analyser un stack trace et identifier la cause probable.
- Proposer des hypothèses multiples, classées par probabilité.
- Détecter des erreurs courantes (off-by-one, null reference, race condition).
- Explorer un flux de données à travers plusieurs fichiers.

### Ce que l'IA fait mal

- Comprendre l'intention métier derrière le code (elle voit le « comment », pas le « pourquoi »).
- Reproduire un bug lié à l'environnement (configuration, réseau, timing).
- Diagnostiquer des problèmes de concurrence subtils sans exécuter le code.

### Exemple — Debugging avec Claude Code

```
── Prompt ──
« Le test OrderServiceTests.CreateOrder_WithDiscount_ShouldApplyCorrectTotal 
  échoue avec l'erreur suivante :

  Expected: 85.50
  Actual: 90.00

  Analyse le code de calcul du prix dans OrderService et identifie 
  pourquoi la remise n'est pas correctement appliquée. »
```

L'agent va :
1. Lire le fichier de test pour comprendre le scénario attendu.
2. Lire le `OrderService` pour suivre la logique de calcul.
3. Identifier l'écart entre le comportement attendu et le comportement réel.
4. Proposer un correctif.

### Point de vigilance

**Ne donnez pas uniquement l'erreur.** Donnez aussi :
- Le comportement attendu vs le comportement observé.
- Les conditions de reproduction.
- Ce que vous avez déjà vérifié ou tenté.

Plus l'IA dispose d'informations, moins elle proposera de fausses pistes.

### Anti-pattern à éviter

```
❌ « Ça ne marche pas, corrige. »
```

Ce prompt ne donne aucune information exploitable. L'IA va deviner, et elle devinera probablement mal.

---

## 3.2 — Code review augmentée par IA

### Le workflow

```
1. Code écrit → 2. Auto-review personnelle → 3. Review IA → 4. Intégrer les retours pertinents → 5. Review humaine (pair)
```

### Point essentiel

> La review IA complète la review humaine. Elle ne la remplace pas.

L'IA est particulièrement efficace pour détecter :
- Les incohérences de style et de nommage.
- Les cas limites non gérés.
- Les problèmes de performance évidents.
- Les failles de sécurité communes (injection SQL, XSS, gestion des secrets).
- Le code mort ou les imports inutilisés.

La review humaine reste indispensable pour :
- La pertinence métier de l'implémentation.
- La cohérence architecturale avec le reste du projet.
- La lisibilité et la maintenabilité dans le contexte de l'équipe.
- Les décisions de design qui impliquent des compromis.

### Exemple — Review avec Claude Code

```
── Prompt ──
« Fais une review du code que je viens d'écrire dans le fichier 
  src/services/PaymentService.ts. Concentre-toi sur :
  1. Les problèmes de sécurité (gestion des données de carte bancaire)
  2. La gestion des erreurs (que se passe-t-il si le provider de paiement 
     ne répond pas ?)
  3. Les cas limites (montant négatif, devise non supportée)
  
  Ne commente pas le style ou le formatage, on a un linter pour ça. »
```

### Exemple — Review avec GitHub Copilot Chat

```
── Prompt ──
« @workspace /review Identifie les problèmes potentiels dans le dernier 
  commit. Focus sur la sécurité et la robustesse. »
```

### Modèle de review structurée

Pour obtenir une review exploitable, demandez un format précis :

```
« Pour chaque problème identifié, indique :
  - Sévérité : Critique / Majeur / Mineur / Suggestion
  - Fichier et ligne concernés
  - Description du problème
  - Suggestion de correction
  
  Classe les problèmes par sévérité décroissante. »
```

---

## 3.3 — Refactoring guidé par IA

### Le workflow

```
1. Identifier la dette technique → 2. Définir l'objectif du refactoring → 3. Demander à l'IA de proposer → 4. Valider l'approche → 5. Appliquer par étapes → 6. Vérifier que les tests passent à chaque étape
```

### Quand l'IA excelle en refactoring

- **Extraction de méthodes** : identifier les blocs de code qui méritent d'être isolés.
- **Renommage sémantique** : proposer des noms de variables et fonctions plus explicites.
- **Simplification conditionnelle** : réduire la complexité cyclomatique.
- **Conversion de patterns** : transformer des callbacks en async/await, des boucles en opérations fonctionnelles, etc.
- **Mise en conformité** : appliquer un coding standard sur du code legacy.

### Exemple — Refactoring avec Claude Code

```
── Prompt 1 : Diagnostic ──
« Analyse le fichier src/controllers/ReportController.ts. 
  Identifie les problèmes de conception :
  - Méthodes trop longues (> 30 lignes)
  - Responsabilités multiples
  - Code dupliqué
  - Couplage fort avec d'autres modules
  
  Liste les problèmes sans les corriger. »

── Prompt 2 : Plan d'action ──
« Sur la base de ton analyse, propose un plan de refactoring 
  en étapes ordonnées. Chaque étape doit être autonome (les tests 
  doivent passer après chaque étape). »

── Prompt 3 : Exécution ──
« Applique l'étape 1 du refactoring. Après modification, 
  lance les tests pour confirmer qu'ils passent toujours. »
```

### Point de vigilance

**Ne refactorez jamais sans filet de tests.** Avant de lancer un refactoring assisté par IA :

1. Vérifiez que les tests existants couvrent les comportements que vous allez toucher.
2. Si la couverture est insuffisante, commencez par ajouter des tests (l'IA peut vous y aider).
3. Exécutez les tests après **chaque** modification, pas seulement à la fin.

---

## 3.4 — Écriture de tests

### Le workflow

```
1. Identifier le code à tester → 2. Définir les scénarios → 3. Générer les tests → 4. Relire et ajuster → 5. Exécuter et valider
```

### Ce que l'IA fait bien

- Générer la structure de tests rapidement (boilerplate, setup, teardown).
- Identifier des cas limites auxquels vous n'aviez pas pensé.
- Produire des données de test réalistes.
- Couvrir les chemins d'erreur (exceptions, timeouts, données invalides).

### Ce que l'IA fait mal

- Comprendre quel **comportement métier** doit être testé (vs quel détail d'implémentation).
- Écrire des tests qui restent pertinents quand l'implémentation change.
- Juger si un mock est approprié ou si un test d'intégration serait préférable.

### Exemple — Génération de tests avec Claude Code

```
── Prompt ──
« Écris des tests unitaires pour la méthode calculateShippingCost() 
  dans src/services/ShippingService.ts.

  Scénarios à couvrir :
  - Livraison standard France métropolitaine
  - Livraison express
  - Livraison DOM-TOM (surcoût)
  - Commande avec poids supérieur à 30kg (tarif spécial)
  - Montant de commande > 100€ (livraison gratuite en standard)
  - Adresse invalide (doit lever une exception)
  
  Utilise le framework Jest. Mocker le service de tarification externe 
  (PricingApiClient). Nomme les tests en français avec le pattern 
  "devrait [comportement attendu] quand [condition]". »
```

### Bonne pratique : la validation des tests générés

Après génération, posez-vous ces questions :

| Question | Si la réponse est non... |
|----------|--------------------------|
| Les tests vérifient-ils un **comportement** et non une **implémentation** ? | Réécrire en termes de résultat attendu |
| Les tests survivraient-ils à un refactoring interne ? | Réduire le couplage avec l'implémentation |
| Chaque test a-t-il un nom explicite ? | Renommer pour que le nom serve de documentation |
| Les tests sont-ils indépendants les uns des autres ? | Supprimer les dépendances inter-tests |
| Les données de test sont-elles réalistes ? | Remplacer les « foo/bar » par des valeurs métier |

---

## 3.5 — Exploration d'une codebase inconnue

### Le workflow

```
1. Vue d'ensemble → 2. Identifier les points d'entrée → 3. Suivre un flux métier → 4. Comprendre les dépendances → 5. Documenter les découvertes
```

C'est l'un des cas d'usage où l'IA apporte le plus de valeur. Explorer une codebase de 100 000 lignes prend des jours à un humain. Un agent le fait en minutes.

### Exemple — Exploration avec Claude Code

```
── Prompt 1 : Vue d'ensemble ──
« Explore la structure de ce projet. Donne-moi :
  1. L'architecture globale (monolithe, microservices, layers...)
  2. Les technologies et frameworks utilisés
  3. Les principaux modules/domaines fonctionnels
  4. Le point d'entrée de l'application »

── Prompt 2 : Flux métier ──
« Trace le flux complet d'une commande e-commerce dans ce projet, 
  depuis la requête HTTP du client jusqu'à l'insertion en base de données. 
  Liste chaque fichier traversé et sa responsabilité dans le flux. »

── Prompt 3 : Dépendances ──
« Identifie les services externes dont ce projet dépend 
  (APIs tierces, bases de données, files de messages, caches). 
  Pour chacun, indique où dans le code la connexion est établie 
  et configurée. »
```

### Point de vigilance

L'IA peut se tromper sur l'architecture, surtout sur les projets qui s'écartent des conventions standard. **Vérifiez toujours ses affirmations en ouvrant les fichiers qu'elle mentionne.**

---

## 3.6 — Documentation technique

### Le workflow

```
1. Identifier ce qui nécessite documentation → 2. Générer un premier jet → 3. Relire et corriger → 4. Faire valider par un pair
```

### Ce que l'IA fait bien

- Structurer de la documentation claire à partir de code existant.
- Générer des exemples d'utilisation pour une API.
- Produire des diagrammes en syntaxe Mermaid ou PlantUML.
- Maintenir la cohérence de style dans une documentation existante.

### Exemple — Documentation d'API avec Claude Code

```
── Prompt ──
« Génère la documentation OpenAPI (format YAML) pour tous les endpoints 
  du contrôleur OrderController.ts. Pour chaque endpoint, inclus :
  - Description fonctionnelle
  - Paramètres d'entrée avec types et contraintes
  - Codes de retour possibles avec exemples de body
  - Un exemple de requête curl »
```

### Point de vigilance

La documentation générée par IA tend à être **techniquement correcte mais fonctionnellement superficielle**. Elle décrit ce que le code fait, rarement pourquoi il le fait. Enrichissez-la toujours avec le contexte métier que vous seul possédez.

---

## 3.7 — Apprentissage et montée en compétence

### L'IA comme tuteur technique

L'un des usages les plus sous-estimés de l'IA est l'apprentissage. Un agent IA peut servir de tuteur patient, disponible, et capable de s'adapter à votre niveau.

### Exemples d'utilisation pour l'apprentissage

```
── Comprendre un concept ──
« Explique-moi le pattern CQRS comme si j'étais un développeur junior 
  qui connaît bien le MVC classique. Utilise une analogie concrète, 
  puis montre un exemple de code simple en TypeScript. »

── Comprendre du code existant ──
« Explique-moi cette regex ligne par ligne :
  ^(?=.*[A-Z])(?=.*[a-z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]{8,}$
  Donne un exemple de chaîne qui match et un exemple qui ne match pas. »

── Comparer des approches ──
« Quelles sont les différences entre RabbitMQ et Kafka pour un système 
  de messaging événementiel ? J'ai un besoin de traitement séquentiel 
  avec garantie de livraison, environ 1000 messages par seconde. 
  Lequel est le plus adapté et pourquoi ? »
```

### Bonne pratique

Quand l'IA vous explique un concept, **reformulez avec vos propres mots** et demandez-lui de valider votre compréhension. Cela force l'assimilation plutôt que la lecture passive.

```
« Si je comprends bien, CQRS sépare les opérations de lecture et d'écriture 
  en deux modèles distincts, ce qui permet d'optimiser chacun indépendamment. 
  Le read model peut être dénormalisé pour la performance, tandis que le 
  write model reste normalisé pour la cohérence. 
  
  Est-ce correct ? Qu'est-ce que j'ai oublié ou simplifié ? »
```

---

## 3.8 — Résumé des workflows

| Workflow | Valeur ajoutée IA | Vigilance requise |
|----------|-------------------|-------------------|
| Debugging | Analyse rapide, hypothèses multiples | Vérifier le fix, comprendre la cause racine |
| Code review | Détection systématique, couverture large | Ne remplace pas la review humaine |
| Refactoring | Propositions structurées, exécution rapide | Tests obligatoires, étape par étape |
| Tests | Couverture rapide, cas limites | Tester le comportement, pas l'implémentation |
| Exploration | Synthèse rapide d'une grande codebase | Vérifier les affirmations |
| Documentation | Structure claire, exemples | Ajouter le contexte métier |
| Apprentissage | Tuteur patient et adaptable | Reformuler pour assimiler |

---

## À retenir

1. **L'IA s'intègre dans vos workflows existants**, elle ne les remplace pas.
2. **Donnez du contexte riche** : l'IA avec du contexte bat l'IA sans contexte.
3. **Procédez par étapes** : diagnostic, puis plan, puis exécution.
4. **Vérifiez toujours** : l'IA propose, vous disposez.
5. **L'IA est un excellent professeur** : utilisez-la pour apprendre, pas seulement pour produire.

---

*Chapitre précédent : [L'art du prompt](02_art_du_prompt.md) | Chapitre suivant : [Les pièges classiques](04_pieges_classiques.md)*
