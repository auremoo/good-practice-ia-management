# Chapitre 4 — Les pièges classiques

## Principe directeur

> Connaître les limites d'un outil est aussi important que connaître ses capacités. Un développeur qui ignore les pièges de l'IA est un développeur qui les subira.

Ce chapitre recense les erreurs les plus fréquentes observées dans l'utilisation de l'IA par les équipes de développement. Pour chaque piège, nous décrivons le mécanisme, les conséquences, et les stratégies de prévention.

---

## 4.1 — Les hallucinations

### Le phénomène

Une hallucination, dans le contexte de l'IA générative, est une information présentée comme factuelle mais qui est **inventée**. L'IA ne « ment » pas — elle génère la suite de texte la plus probable, même quand la réponse correcte n'existe pas dans ses données d'entraînement.

### Manifestations courantes en développement

| Type d'hallucination | Exemple |
|---------------------|---------|
| **API inexistante** | L'IA recommande `response.getStatusMessage()` sur un objet qui n'a pas cette méthode |
| **Librairie fictive** | « Utilisez le package `express-jwt-validator` pour simplifier... » — le package n'existe pas ou il existe sous un autre nom |
| **Version inventée** | « Depuis la version 4.2 de NestJS, vous pouvez utiliser... » — cette fonctionnalité n'existe pas dans cette version |
| **Comportement incorrect** | « La méthode `Array.sort()` en JavaScript trie par ordre numérique par défaut. » — faux, elle trie par ordre lexicographique |
| **Flag ou option inexistant** | « Utilisez `docker run --memory-limit=512m` » — le flag correct est `--memory` |

### Pourquoi c'est dangereux

Une hallucination bien formulée est indiscernable d'une information correcte à la première lecture. Le code compile parfois (si l'hallucination concerne un détail sémantique, pas syntaxique), ce qui donne une fausse impression de validité.

### Comment se protéger

1. **Vérifier les API et méthodes** : consultez la documentation officielle pour tout appel que vous ne connaissez pas.
2. **Vérifier les packages** : avant d'installer un package recommandé par l'IA, vérifiez son existence sur npm, NuGet, PyPI, etc.
3. **Tester le code** : ne vous fiez pas au fait que « ça a l'air correct ». Exécutez-le.
4. **Croiser les sources** : si l'IA affirme quelque chose de surprenant, vérifiez dans la documentation officielle.
5. **Demander des références** : « Quelle est la source de cette information ? » — l'IA inventera parfois la source aussi, mais cela vous donne quelque chose à vérifier.

### Exemple concret

```
── Prompt ──
« Comment configurer le rate limiting dans Express.js ? »

── Réponse de l'IA (contient une hallucination) ──
« Utilisez le middleware intégré express.rateLimit() :

  app.use(express.rateLimit({
    windowMs: 15 * 60 * 1000,
    max: 100
  }));
»
```

**Le problème** : `express.rateLimit()` n'existe pas comme middleware intégré. Il faut utiliser le package tiers `express-rate-limit`. L'IA a fusionné deux informations (Express a des middlewares intégrés + il existe un middleware de rate limiting) pour produire une réponse plausible mais fausse.

---

## 4.2 — L'over-engineering induit par l'IA

### Le phénomène

L'IA a tendance à proposer des solutions plus complexes que nécessaire. Ce biais a plusieurs origines :
- Elle a été entraînée sur du code qui inclut beaucoup de patterns d'architecture « propre ».
- Elle cherche à produire des réponses « complètes », ce qui l'amène à ajouter des couches.
- Elle n'a pas le recul sur la taille et la maturité de votre projet.

### Manifestations courantes

| Demande simple | Réponse over-engineered |
|----------------|------------------------|
| « Fais un script de migration de données » | Propose un framework de migration avec plugins, rollback, dry-run, logging structuré, retry policy |
| « Crée un endpoint CRUD » | Génère une architecture complète avec Repository pattern, Unit of Work, DTO mapping, validation pipeline |
| « Parse ce fichier CSV » | Suggère de créer une bibliothèque de parsing générique avec support multi-format |
| « Envoie un email de notification » | Propose un système de templating, queue de messages, retry avec backoff exponentiel |

### Les conséquences

- **Complexité accidentelle** : le code est plus difficile à maintenir que nécessaire.
- **Temps perdu** : développer et tester une architecture surdimensionnée.
- **Barrière à l'entrée** : les collègues ont plus de mal à comprendre le code.
- **YAGNI violé** : des fonctionnalités sont implémentées « au cas où » mais jamais utilisées.

### Comment se protéger

1. **Appliquer le principe YAGNI** (You Ain't Gonna Need It) : ne demandez que ce dont vous avez besoin maintenant.
2. **Poser une contrainte de simplicité dans le prompt** :
   ```
   « Propose la solution la plus simple qui fonctionne. 
     Pas de pattern supplémentaire, pas de couche d'abstraction. 
     On est sur un projet de petite taille. »
   ```
3. **Challenger systématiquement** : « Est-ce que cette complexité est nécessaire pour mon cas d'usage ? Quelle serait la version minimale ? »
4. **Se méfier des abstractions prématurées** : si l'IA propose une interface pour une seule implémentation, c'est probablement inutile.

---

## 4.3 — La fuite de données sensibles

### Le phénomène

Dans le flux du travail, il est tentant de coller du code tel quel dans un chatbot IA. Ce code peut contenir :
- Des clés d'API, tokens d'accès, secrets.
- Des chaînes de connexion à des bases de données.
- Des données personnelles de clients (emails, adresses, données médicales, financières).
- Du code propriétaire soumis à des clauses de confidentialité.

### Niveaux de risque selon l'outil

| Outil | Risque de fuite | Raison |
|-------|----------------|--------|
| **ChatGPT (version gratuite)** | Élevé | Les données peuvent être utilisées pour l'entraînement |
| **ChatGPT Team / Enterprise** | Modéré | Pas d'entraînement, mais stockage sur serveurs tiers |
| **Claude Code (CLI local)** | Faible | Le code reste sur votre machine, l'IA lit localement |
| **GitHub Copilot Business** | Faible | Politique de non-rétention, pas d'entraînement sur votre code |
| **Solution on-premise / self-hosted** | Très faible | Rien ne quitte votre infrastructure |

> **Note** : même avec un outil à faible risque, les prompts sont transmis aux serveurs du fournisseur pour le traitement. Vérifiez toujours les conditions d'utilisation et les politiques de données de votre fournisseur.

### Règles de sécurité

1. **Ne jamais coller de secrets dans un prompt.** Remplacez-les par des placeholders :
   ```
   ❌ const apiKey = "sk-proj-abc123def456...";
   ✅ const apiKey = process.env.API_KEY; // remplacé pour le prompt
   ```

2. **Ne jamais coller de vraies données clients.** Utilisez des données fictives :
   ```
   ❌ { "email": "jean.dupont@client-reel.fr", "iban": "FR76..." }
   ✅ { "email": "test@example.com", "iban": "FR76XXXXXXXXXXXX" }
   ```

3. **Vérifier les fichiers avant de les soumettre à un agent.** Un agent comme Claude Code accède à tout le répertoire de travail. Assurez-vous que les fichiers `.env`, les certificats et les credentials ne sont pas dans le périmètre.

4. **Utiliser un `.gitignore` et un `.claudeignore`** (ou équivalent) pour exclure les fichiers sensibles du périmètre de l'agent.

5. **En cas de doute, demander au responsable sécurité** de votre organisation avant d'utiliser un outil IA sur un projet sensible.

### Cas particulier : le code propriétaire

Certaines organisations interdisent de soumettre du code propriétaire à des services IA tiers. Vérifiez la politique de votre entreprise. En l'absence de politique claire, appliquez le principe de précaution.

---

## 4.4 — La dette technique invisible

### Le phénomène

Le code généré par IA fonctionne souvent immédiatement, mais il accumule de la dette technique que le développeur ne voit pas parce qu'il n'a pas écrit le code lui-même.

### Formes de dette technique invisible

| Forme | Exemple |
|-------|---------|
| **Dépendances inutiles** | L'IA ajoute une librairie pour une tâche réalisable en quelques lignes de code natif |
| **Patterns inappropriés** | Application d'un design pattern complexe dans un contexte simple |
| **Code non idiomatique** | L'IA écrit du C# avec des patterns Java, ou du TypeScript avec des patterns JavaScript ancien |
| **Absence de gestion d'erreurs** | Le happy path fonctionne, les cas d'erreur sont ignorés |
| **Logique dupliquée** | L'IA ne connaît pas les utilitaires existants dans le projet et réécrit une logique déjà implémentée |
| **Couplage caché** | Le code généré crée des dépendances implicites entre modules |

### Comment se protéger

1. **Comprendre le code avant de l'intégrer.** La dette invisible devient visible quand on lit le code avec attention.
2. **Vérifier les imports.** L'IA a-t-elle ajouté de nouvelles dépendances ? Sont-elles justifiées ?
3. **Comparer avec le style du projet.** Le code généré suit-il les conventions existantes ?
4. **Chercher les doublons.** L'IA a-t-elle réimplémenté une fonctionnalité qui existe déjà dans le projet ou dans une dépendance déjà installée ?
5. **Demander une version minimale.** « Peux-tu réécrire cela sans ajouter de dépendance supplémentaire ? »

---

## 4.5 — Le « cargo cult » du prompt

### Le phénomène

Par imitation, certains développeurs adoptent des techniques de prompting vues sur Internet sans comprendre pourquoi elles fonctionnent (ou ne fonctionnent pas).

### Exemples de cargo cult

| Technique aveugle | Réalité |
|-------------------|---------|
| « Tu es un expert senior en [technologie] » | Utile pour cadrer le niveau de réponse, mais ne rend pas l'IA plus compétente |
| « Pense étape par étape » | Efficace sur des problèmes de raisonnement, inutile pour du code simple |
| « Respire profondément et concentre-toi » | N'a aucun effet. L'IA n'a pas de processus cognitif à améliorer |
| Ajouter « IMPORTANT » en majuscules partout | Dilue l'emphase au lieu de la renforcer |
| Menacer l'IA (« sinon je te donne une mauvaise note ») | N'a aucun effet sur la qualité de la réponse |

### La bonne approche

Les techniques de prompting sont des outils. Comme tout outil :
- Comprenez **pourquoi** elles fonctionnent avant de les utiliser.
- Utilisez-les **quand elles sont pertinentes**, pas systématiquement.
- Testez leur impact sur **votre** cas d'usage spécifique.

---

## 4.6 — La dépendance à l'outil

### Le phénomène

L'utilisation régulière de l'IA peut créer une dépendance progressive. Les symptômes :

- Incapacité à commencer une tâche sans d'abord « demander à l'IA ».
- Difficulté croissante à écrire du code de zéro.
- Sentiment de perte de productivité sans l'outil.
- Abandon de l'apprentissage des fondamentaux (« l'IA le fera pour moi »).

### Les conséquences professionnelles

- **En entretien technique** : incapacité à résoudre un problème sans assistance.
- **En situation de crise** : quand l'outil n'est pas disponible (panne, restriction réseau, environnement sécurisé), le développeur est paralysé.
- **En progression de carrière** : stagnation technique masquée par une productivité artificielle.

### Comment maintenir ses compétences

1. **Pratiquer régulièrement sans IA.** Consacrez du temps à coder « à la main », notamment pour les algorithmes et les structures de données.
2. **Comprendre avant d'automatiser.** Utilisez l'IA après avoir réfléchi au problème, pas avant.
3. **Faire des kata de code** sans assistance : exercices courts ciblant un concept précis.
4. **Lire du code** sans demander à l'IA de l'expliquer. La lecture de code est une compétence qui se travaille.
5. **Participer à des code reviews** pour maintenir votre capacité d'analyse critique.

---

## 4.7 — La propagation d'erreurs

### Le phénomène

Quand un développeur intègre une erreur de l'IA sans la détecter, cette erreur peut se propager :
- Dans les tests (qui valident un comportement incorrect).
- Dans la documentation (qui documente un fonctionnement faux).
- Dans d'autres modules (qui se basent sur le code erroné).
- Dans les connaissances de l'équipe (qui croit que l'implémentation est correcte).

### Exemple de propagation

```
Étape 1 : L'IA génère une fonction de calcul de TVA avec une erreur d'arrondi.
Étape 2 : Le développeur demande à l'IA de générer les tests → les tests valident 
          le comportement incorrect.
Étape 3 : Le développeur demande à l'IA de documenter → la documentation décrit 
          le comportement incorrect.
Étape 4 : Un autre développeur lit la documentation et implémente le même calcul 
          incorrect ailleurs.
```

### Comment briser la chaîne

1. **Ne pas utiliser l'IA pour valider le code de l'IA.** Si l'IA a écrit le code et les tests, l'erreur est dans les deux.
2. **Écrire les tests en premier** (TDD) puis demander à l'IA d'implémenter. Les tests définissent le comportement attendu indépendamment de l'implémentation.
3. **Faire relire par un humain.** La review humaine casse le circuit fermé IA → IA.
4. **Vérifier les calculs critiques manuellement.** Montants, conversions, arrondis, dates : vérifiez avec une calculatrice ou un tableur.

---

## 4.8 — Le piège du « contexte perdu »

### Le phénomène

Dans une session de travail longue, l'IA peut « oublier » des éléments de contexte mentionnés plus tôt, surtout si la conversation est longue et que les premiers messages sortent de la fenêtre de contexte.

### Conséquences

- L'IA contredit ses propres recommandations précédentes.
- Elle réintroduit un pattern que vous aviez explicitement écarté.
- Elle oublie une contrainte technique mentionnée en début de session.

### Comment se protéger

1. **Rappeler les contraintes importantes** dans les prompts ultérieurs si la session est longue.
2. **Utiliser la configuration persistante** : dans Claude Code, le fichier `CLAUDE.md` maintient le contexte entre les sessions.
3. **Découper les sessions longues** en sous-sessions thématiques.
4. **Résumer les décisions prises** régulièrement pour les ancrer dans le contexte.

---

## À retenir

| Piège | Prévention clé |
|-------|----------------|
| Hallucinations | Vérifier dans la documentation officielle |
| Over-engineering | Exiger la simplicité, appliquer YAGNI |
| Fuite de données | Ne jamais coller de secrets, vérifier les politiques |
| Dette technique invisible | Comprendre le code, vérifier les dépendances |
| Cargo cult du prompt | Comprendre pourquoi une technique fonctionne |
| Dépendance à l'outil | Pratiquer régulièrement sans IA |
| Propagation d'erreurs | Ne pas valider l'IA par l'IA |
| Contexte perdu | Rappeler les contraintes, utiliser la config persistante |

---

*Chapitre précédent : [Workflows concrets pour développeurs](03_workflows_developpeurs.md) | Chapitre suivant : [Travailler avec un agent IA](05_travailler_avec_un_agent.md)*
