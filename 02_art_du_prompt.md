# Chapitre 2 — L'art du prompt : communiquer efficacement avec une IA

## Principe directeur

> La qualité de la réponse est le reflet direct de la qualité de la demande.

Un prompt n'est pas une commande tapée dans un terminal. C'est une communication structurée avec un système qui n'a aucun contexte implicite. Tout ce que vous ne dites pas, l'IA le devinera — et elle se trompera souvent.

---

## 2.1 — Anatomie d'un bon prompt

Un prompt efficace repose sur quatre composantes, mémorisables par l'acronyme **CROP** :

| Composante | Description | Exemple |
|------------|-------------|---------|
| **C**ontexte | L'environnement, les contraintes, le projet | « Dans une API .NET 8 qui utilise Entity Framework Core et PostgreSQL... » |
| **R**ôle / Résultat attendu | Ce que l'on veut obtenir précisément | « ...génère un endpoint POST qui crée un utilisateur... » |
| **O**bjectif métier | Pourquoi on le fait, le besoin fonctionnel | « ...pour permettre l'inscription depuis l'application mobile... » |
| **P**récisions | Contraintes techniques, style, format | « ...avec validation des champs, retour 201 en cas de succès, et gestion des doublons d'email. » |

### Exemple complet — Bon prompt

```
Contexte : Je travaille sur une API REST en .NET 8 avec Entity Framework Core, 
connectée à PostgreSQL. Le projet suit une architecture Clean Architecture 
avec les couches Domain, Application, Infrastructure et API.

Besoin : Je dois créer un endpoint POST /api/users qui permet de créer 
un nouvel utilisateur.

Contraintes :
- Validation : email au format valide, mot de passe minimum 8 caractères
- Si l'email existe déjà, retourner une erreur 409
- Le mot de passe doit être hashé avant stockage (on utilise BCrypt)
- Retourner 201 avec l'URI de la ressource créée
- Le projet utilise MediatR pour le pattern CQRS

Merci de générer la commande (command + handler) et le controller.
```

### Exemple complet — Mauvais prompt

```
Fais-moi un endpoint pour créer un utilisateur.
```

La différence de résultat entre ces deux prompts sera considérable. Le premier donne à l'IA tout ce dont elle a besoin pour produire un code directement utilisable. Le second l'oblige à deviner le langage, le framework, l'architecture, les contraintes — et elle se trompera sur la plupart.

---

## 2.2 — Les erreurs de prompting les plus fréquentes

### Erreur 1 : Le prompt trop vague

```
❌ « Optimise ce code. »
```

L'IA ne sait pas ce que « optimiser » signifie pour vous. Vitesse d'exécution ? Lisibilité ? Consommation mémoire ? Maintenabilité ?

```
✅ « Ce endpoint met 3 secondes à répondre pour 10 000 enregistrements. 
    La requête SQL sous-jacente est [coller la requête]. 
    Identifie les causes probables de lenteur et propose des optimisations 
    en priorisant la performance de lecture. »
```

### Erreur 2 : Le prompt sans contexte technique

```
❌ « Ajoute des tests. »
```

```
✅ « Ajoute des tests unitaires pour la classe UserService (fichier 
    src/services/UserService.ts). Le projet utilise Jest avec ts-jest. 
    Couvre les cas : création réussie, email en doublon, validation échouée. 
    Mocker le repository avec jest.mock(). »
```

### Erreur 3 : Tout demander en une seule fois

```
❌ « Crée-moi une application complète de gestion de tâches avec 
    authentification, base de données, API REST et front React. »
```

Ce type de demande produit du code superficiel, incohérent et inutilisable en production. Il est toujours préférable de découper.

```
✅ Séquence de prompts :
   1. « Définis le modèle de données pour une application de gestion de tâches. 
       Entités : User, Task, Project. Relations : un User a plusieurs Tasks, 
       une Task appartient à un Project. »
   2. « À partir de ce modèle, génère les endpoints CRUD pour l'entité Task. »
   3. « Ajoute la logique d'authentification JWT sur ces endpoints. »
   4. [etc.]
```

### Erreur 4 : Ne pas préciser le format de sortie

```
❌ « Explique-moi comment fonctionne le middleware d'authentification. »
```

```
✅ « Explique le flux d'authentification du middleware JWT dans ce projet. 
    Structure ta réponse en : 
    1) le flux requête → réponse,
    2) où le token est validé, 
    3) comment les claims sont extraits et utilisés. »
```

---

## 2.3 — Le prompting itératif : la clé de la maîtrise

### Le premier résultat n'est (presque) jamais le bon

Il est rare qu'un seul prompt produise exactement le résultat attendu. Le prompting efficace est un **processus itératif** :

```
Étape 1 : Prompt initial → Résultat approximatif
Étape 2 : Affiner → « Le résultat est proche, mais j'ai besoin que... »
Étape 3 : Corriger → « L'approche est bonne, mais cette partie est incorrecte parce que... »
Étape 4 : Finaliser → « Parfait, ajoute juste la gestion de [cas limite]. »
```

### Exemple concret d'itération avec Claude Code

```
── Prompt 1 ──
« Lis le fichier src/services/OrderService.ts et explique-moi 
  la logique de calcul du prix total. »

── Réponse de l'IA ──
[Explication de la logique]

── Prompt 2 ──
« Merci. Je vois que le calcul ne prend pas en compte les réductions 
  par volume. Ajoute une logique de remise : -5% au-delà de 10 unités, 
  -10% au-delà de 50 unités. Applique la remise avant la TVA. »

── Réponse de l'IA ──
[Code modifié]

── Prompt 3 ──
« Le code fonctionne mais la méthode fait maintenant 60 lignes. 
  Extrais la logique de remise dans une méthode privée calculateDiscount(). »
```

Cette approche itérative est naturelle et productive. Elle permet de :
- Garder le contrôle à chaque étape.
- Valider la compréhension de l'IA avant qu'elle n'aille plus loin.
- Corriger les dérives tôt plutôt que tard.

---

## 2.4 — Techniques avancées de prompting

### Fournir des exemples (few-shot prompting)

Quand le format ou le style attendu est spécifique, donner un exemple vaut mieux que mille explications.

```
« Génère des messages de log pour les opérations du OrderService 
  en suivant ce format existant :

  logger.Information("Payment processed for Order {OrderId}. 
  Amount: {Amount}, Method: {PaymentMethod}", order.Id, amount, method);

  Applique le même style (structured logging, noms de propriétés en PascalCase, 
  message descriptif) pour les opérations : création de commande, 
  annulation, mise à jour du statut. »
```

### Donner un contre-exemple

Parfois, montrer ce qu'on ne veut **pas** est plus efficace que décrire ce qu'on veut.

```
« Refactore cette fonction. 

  Ce que je ne veux PAS :
  - Des commentaires sur chaque ligne
  - Un découpage en 10 micro-fonctions
  - L'ajout de design patterns superflus
  
  Ce que je veux :
  - Noms de variables explicites
  - Extraction des magic numbers en constantes
  - Gestion propre du cas null »
```

### Demander à l'IA de raisonner avant de coder

Pour les problèmes complexes, demander à l'IA de structurer sa réflexion avant de produire du code donne de meilleurs résultats.

```
« Avant de coder, analyse la situation :
  1. Quelles sont les contraintes du problème ?
  2. Quelles approches sont possibles ?
  3. Quels sont les avantages et inconvénients de chacune ?
  4. Quelle approche recommandes-tu et pourquoi ?
  
  Ensuite seulement, implémente la solution retenue. »
```

### Définir un persona technique

```
« Agis comme un développeur senior spécialisé en performance .NET. 
  Analyse cette méthode et identifie les problèmes de performance 
  potentiels. Sois spécifique : mentionne la complexité algorithmique, 
  les allocations mémoire inutiles, et les appels réseau non optimisés. »
```

> **Note sur les outils spécifiques** : Dans Claude Code, le persona peut être défini de manière persistante via le fichier `CLAUDE.md` à la racine du projet. Cette approche évite de répéter le contexte à chaque session. D'autres outils comme Cursor utilisent des fichiers `.cursorrules` pour un effet similaire.

---

## 2.5 — Les spécificités du prompting dans un agent (Claude Code, Cursor, etc.)

Le prompting dans un agent diffère du prompting dans un chatbot classique sur plusieurs points :

### L'agent a accès au contexte du projet

Contrairement à un chatbot dans un navigateur, un agent comme Claude Code peut lire vos fichiers, exécuter des commandes, et voir l'arborescence du projet. Profitez-en.

```
── Dans un chatbot (pas d'accès au code) ──
« Voici mon fichier UserService.ts : [coller 200 lignes]. 
  Et voici mon UserRepository.ts : [coller 150 lignes]. 
  Trouve le bug. »

── Dans un agent (accès au code) ──
« Il y a un bug dans la création d'utilisateur : le mail de confirmation 
  n'est pas envoyé quand l'utilisateur s'inscrit via l'API mobile. 
  Regarde UserService et le flux d'inscription pour identifier la cause. »
```

L'agent ira lire les fichiers lui-même, en explorera d'autres si nécessaire, et aura une vision plus complète que ce que vous auriez pu coller dans un prompt.

### L'agent peut agir

Un agent peut modifier des fichiers, exécuter des tests, lancer des commandes. Cela change la nature du prompt :

```
── Prompt passif (chatbot) ──
« Montre-moi à quoi ressemblerait ce code avec des tests. »

── Prompt actif (agent) ──
« Écris les tests unitaires pour UserService et lance-les pour vérifier 
  qu'ils passent. Si des tests échouent, corrige-les. »
```

### L'agent garde le contexte dans une session

Au sein d'une session de travail, l'agent se souvient des échanges précédents. Pas besoin de tout ré-expliquer :

```
── Prompt 1 ──
« Explore la structure du projet et explique-moi l'architecture. »

── Prompt 2 (dans la même session) ──
« Bien. Maintenant ajoute un nouveau module Payment en suivant 
  la même architecture que tu viens de décrire. »
```

---

## 2.6 — Comparatif des approches selon les outils

| Aspect | Claude Code / Agent CLI | GitHub Copilot | ChatGPT / Chat web |
|--------|------------------------|----------------|---------------------|
| Contexte | Accès complet au projet | Fichier ouvert + voisins | Aucun (copier-coller) |
| Action | Peut modifier les fichiers, lancer des commandes | Suggestion inline | Génère du texte à copier |
| Mémoire de session | Oui, dans la session | Limitée au fichier courant | Oui, dans la conversation |
| Mémoire inter-session | Oui (CLAUDE.md, mémoire) | Non | Limitée |
| Prompting optimal | Intentions de haut niveau, contexte métier | Court, inline, complétion | Détaillé, auto-suffisant |

### Adapter le prompt à l'outil

**Claude Code** — Privilégier les intentions de haut niveau. L'agent explorera le code lui-même.
```
« Le test d'intégration sur le panier e-commerce échoue. 
  Diagnostique et corrige le problème. »
```

**GitHub Copilot** — Privilégier les commentaires descriptifs dans le code qui guident la complétion.
```typescript
// Calcule le prix total avec remise progressive :
// - 5% au-delà de 10 unités
// - 10% au-delà de 50 unités
// Applique la remise avant TVA (20%)
function calculateTotalPrice(items: CartItem[]): number {
```

**ChatGPT / Chat web** — Fournir tout le contexte dans le prompt, puisque l'outil n'a pas accès au code.
```
« J'ai une fonction TypeScript qui calcule un prix. 
  Voici le code : [coller]. 
  Voici le test qui échoue : [coller]. 
  Voici l'erreur : [coller]. 
  Identifie le bug. »
```

---

## À retenir

1. **Structurez vos prompts** avec la méthode CROP : Contexte, Résultat, Objectif, Précisions.
2. **Soyez spécifique.** Plus votre demande est précise, plus la réponse est exploitable.
3. **Itérez.** Le prompting est un dialogue, pas une commande unique.
4. **Adaptez-vous à l'outil.** Un agent n'a pas besoin du même prompt qu'un chatbot.
5. **Montrez plutôt que décrivez.** Exemples et contre-exemples valent mieux que de longues explications.
6. **Découpez les problèmes complexes.** Une séquence de prompts ciblés bat toujours un méga-prompt.

---

*Chapitre précédent : [Mindset — L'esprit critique avant tout](01_mindset_esprit_critique.md) | Chapitre suivant : [Workflows concrets pour développeurs](03_workflows_developpeurs.md)*
