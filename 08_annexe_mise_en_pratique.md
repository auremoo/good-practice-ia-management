# Chapitre 8 — Annexe : mise en pratique automatisée

## Objectif

Les chapitres précédents décrivent les bonnes pratiques. Ce chapitre montre comment **les automatiser** pour qu'elles s'appliquent sans effort, directement dans l'outil.

L'idée est simple : plutôt que de compter sur la discipline individuelle, on configure l'agent IA pour qu'il applique lui-même les bonnes pratiques — en posant les bonnes questions, en reformulant les demandes floues, et en refusant de foncer tête baissée sur un prompt vague.

---

## 8.1 — Le problème : l'utilisateur ne sait pas qu'il prompte mal

Un développeur qui écrit :

```
Fais-moi un endpoint pour les commandes.
```

ne réalise pas que cette demande est insuffisante. Il obtient un résultat, l'intègre, et ne se rend pas compte qu'il aurait pu obtenir un résultat **dix fois plus adapté** avec une demande structurée.

Le résultat : du temps perdu en allers-retours, du code à retravailler, et parfois du code inadapté qui part en production.

### La solution : rendre l'agent proactif

Au lieu de compter sur l'utilisateur pour formuler un bon prompt, on configure l'agent pour qu'il **détecte les demandes insuffisantes et réagisse** :

| Demande de l'utilisateur | Sans garde-fou | Avec garde-fou |
|--------------------------|----------------|----------------|
| « Fais les tests » | Génère des tests génériques, souvent inutiles | Demande : quels scénarios ? quel framework ? quel fichier ? |
| « Optimise ça » | Applique des optimisations arbitraires | Demande : optimiser quoi ? perf, lisibilité, mémoire ? |
| « Crée un service de paiement avec Stripe, les webhooks, le retry, les remboursements et les emails de confirmation » | Produit 500 lignes de code approximatif | Propose un découpage en 5 étapes, demande par laquelle commencer |
| « Corrige le bug » | Devine quel bug et applique un fix hasardeux | Demande : quel comportement attendu ? quelle erreur ? déjà investigué ? |

---

## 8.2 — Le CLAUDE.md comme garde-fou

### Qu'est-ce que ça change concrètement ?

Le fichier `CLAUDE.md` est lu par Claude Code au début de chaque session. En y inscrivant des règles comportementales, on transforme le comportement par défaut de l'agent.

**Sans CLAUDE.md** :

```
Utilisateur : « Optimise le service de commandes. »

Claude : « J'ai refactoré OrderService.ts. Voici les changements :
          - Extraction de 4 méthodes privées
          - Ajout du pattern Strategy pour les remises
          - Migration des callbacks vers async/await
          - Ajout d'un cache Redis pour les requêtes fréquentes
          [500 lignes de diff] »
```

L'agent a fait des choix sans demander, ajouté une dépendance (Redis) non sollicitée, et produit un diff massif difficile à valider.

**Avec le CLAUDE.md template** :

```
Utilisateur : « Optimise le service de commandes. »

Claude : « Avant d'optimiser, j'ai besoin de précisions :

          **Ce que je comprends :** tu veux améliorer OrderService.
          
          Questions :
          1. Tu cibles la performance (vitesse de réponse) ou 
             la qualité du code (lisibilité, maintenabilité) ?
          2. Y a-t-il un problème spécifique observé 
             (lenteur, erreurs, difficulté de maintenance) ?
          3. Des contraintes ? (pas de nouvelle dépendance, 
             ne pas toucher à la logique de remise, etc.)
          
          Une fois que j'ai ces éléments, je te propose un plan 
          avant de modifier quoi que ce soit. »
```

Le résultat sera plus ciblé, plus utile, et le développeur gardera le contrôle.

### Comment l'installer

1. Copier le fichier `templates/CLAUDE_TEMPLATE_BONNES_PRATIQUES.md` fourni avec ce guide.
2. Le placer à la racine du projet sous le nom `CLAUDE.md`.
3. Adapter les sections selon le contexte du projet (stack technique, conventions, commandes).
4. Commiter le fichier dans le dépôt pour que toute l'équipe en bénéficie.

```bash
cp templates/CLAUDE_TEMPLATE_BONNES_PRATIQUES.md /chemin/vers/mon-projet/CLAUDE.md
```

### Niveaux d'application

Le template peut être adapté selon le niveau de contrôle souhaité :

| Niveau | Comportement | Pour qui |
|--------|-------------|----------|
| **Léger** | Reformulation uniquement sur les demandes très vagues | Équipes seniors, peu de friction |
| **Standard** | Reformulation + questions sur les demandes ambiguës | Recommandé pour la plupart des équipes |
| **Strict** | Reformulation systématique + plan obligatoire avant toute modification | Projets critiques, équipes junior, onboarding |

Pour ajuster le niveau, modifiez la section « Analyse de la demande avant exécution » dans le template.

---

## 8.3 — Équivalents pour les autres outils

### Cursor — `.cursorrules`

Cursor utilise un fichier `.cursorrules` à la racine du projet. Le même principe s'applique :

```markdown
# .cursorrules

## Comportement

Avant toute modification de code, reformule la demande de l'utilisateur 
pour confirmer la compréhension. Si la demande est vague, pose des questions 
ciblées avant de commencer.

Ne jamais :
- Ajouter de dépendance sans le signaler
- Modifier des fichiers hors du périmètre demandé
- Produire plus de code que nécessaire

Toujours :
- Lire le fichier avant de le modifier
- Vérifier les conventions du projet existant
- Signaler les cas limites non gérés
```

### GitHub Copilot — Instructions

GitHub Copilot permet de configurer des instructions personnalisées dans les settings VS Code ou via un fichier `.github/copilot-instructions.md` :

```markdown
# copilot-instructions.md

Quand tu génères du code, respecte les conventions du projet.
Privilégie la simplicité. Ne propose pas de patterns d'architecture 
non demandés. Si le contexte est insuffisant, génère un commentaire 
TODO plutôt qu'une implémentation devinée.
```

> **Limitation** : Copilot étant un outil de complétion inline, le contrôle comportemental est moins fin qu'avec un agent conversationnel. Les instructions influencent la complétion mais ne permettent pas de poser des questions à l'utilisateur.

### ChatGPT — Custom Instructions ou GPTs

Pour les équipes qui utilisent ChatGPT, les Custom Instructions ou la création d'un GPT dédié permettent d'intégrer les mêmes principes :

```
Quand un développeur te soumet du code ou une demande :
1. Reformule ta compréhension de la demande
2. Identifie les informations manquantes
3. Pose les questions nécessaires AVANT de produire du code
4. Si la demande est trop large, propose un découpage
```

---

## 8.4 — Mécanismes complémentaires dans Claude Code

### La mémoire comme filet de sécurité

Au-delà du CLAUDE.md, la mémoire de Claude Code permet de capitaliser sur les corrections :

```
Utilisateur : « Non, ici on n'utilise jamais de raw SQL, 
               toujours l'ORM. Retiens-le. »

→ Claude mémorise. À la prochaine session, si l'utilisateur demande 
  une requête de données, Claude utilisera l'ORM spontanément.
```

Ce mécanisme transforme chaque correction en amélioration durable. L'agent apprend les préférences de l'utilisateur et du projet au fil du temps.

### Les hooks : garde-fous techniques

Claude Code permet de configurer des hooks — des scripts exécutés automatiquement à certains moments. Bien que limités à des scripts shell (pas d'analyse IA), ils peuvent servir de garde-fous techniques :

**Exemple : hook pre-commit pour vérifier les fichiers sensibles**

Dans le fichier `.claude/settings.json` :

```json
{
  "hooks": {
    "pre-tool-use": [
      {
        "matcher": "Edit|Write",
        "command": "bash scripts/check-no-secrets.sh $FILE_PATH"
      }
    ]
  }
}
```

Avec un script `check-no-secrets.sh` qui vérifie qu'un fichier modifié ne contient pas de patterns de secrets (clés API, tokens, mots de passe).

### Le fichier `.claudeignore`

Pour empêcher l'agent d'accéder à des fichiers sensibles :

```
# .claudeignore
.env
.env.*
**/secrets/**
**/credentials/**
*.pem
*.key
*.pfx
docker-compose.override.yml
```

---

## 8.5 — Mesurer l'impact

### Comment savoir si le garde-fou fonctionne ?

Après mise en place du template CLAUDE.md, observez ces indicateurs :

| Indicateur | Avant | Après (attendu) |
|-----------|-------|------------------|
| Nombre d'allers-retours pour une tâche | 5-10 | 2-4 |
| Code rejeté en review | Fréquent | Rare |
| Régressions introduites par du code IA | Occasionnelles | Exceptionnelles |
| Sentiment de contrôle du développeur | « L'IA fait ce qu'elle veut » | « L'IA me demande avant d'agir » |
| Temps moyen de validation du code IA | Long (relecture complète) | Court (code ciblé et compris) |

### Feedback loop

Encouragez l'équipe à faire remonter les cas où :
- L'agent a posé une question utile qui a évité une erreur → renforcer ce comportement.
- L'agent a posé une question inutile ou bloquante → ajuster le template.
- L'agent n'a pas détecté un prompt problématique → ajouter une règle.

Le CLAUDE.md est un document vivant. Il s'améliore avec l'usage.

---

## 8.6 — Limites de l'approche

### Ce que le CLAUDE.md peut faire

- Pousser l'agent à poser des questions avant d'agir.
- Imposer une reformulation de la demande.
- Définir des règles de production de code.
- Établir des garde-fous sur les commandes destructives.

### Ce que le CLAUDE.md ne peut pas faire

- **Garantir** que l'agent respectera toujours les règles. Les instructions du CLAUDE.md sont des directives fortes, mais pas des contraintes absolues. Sur des prompts très directs et insistants, l'agent peut passer outre.
- **Empêcher** un utilisateur déterminé à ignorer les bonnes pratiques. Si quelqu'un veut coller 500 lignes de code sans contexte et dire « corrige ça », il le peut.
- **Remplacer** la formation. Le template est un filet de sécurité, pas un substitut à la compréhension des principes.
- **S'adapter** automatiquement aux spécificités de chaque projet. Il doit être personnalisé.

### L'analogie

Le CLAUDE.md est comparable à un `.editorconfig` ou un `eslintrc` : il automatise les conventions, réduit les frictions, mais il ne remplace pas la compréhension de pourquoi ces conventions existent.

---

## À retenir

1. **Le CLAUDE.md est le levier le plus efficace** pour automatiser les bonnes pratiques avec Claude Code.
2. **Le template fourni est un point de départ**, à adapter au contexte de chaque projet et équipe.
3. **Des équivalents existent** pour Cursor (`.cursorrules`), Copilot (`copilot-instructions.md`), et ChatGPT (Custom Instructions).
4. **Les mécanismes sont complémentaires** : CLAUDE.md + mémoire + .claudeignore forment un ensemble cohérent.
5. **Mesurez et itérez** : le template est un document vivant qui s'améliore avec l'usage.
6. **Aucun outil ne remplace la formation.** Le template est un filet de sécurité, pas un substitut.

---

*Chapitre précédent : [Éthique et responsabilité](07_ethique_et_responsabilite.md) | Retour au [sommaire](README.md)*
