# Chapitre 5 — Travailler avec un agent IA

## Principe directeur

> Un agent IA n'est pas un chatbot amélioré. C'est un assistant autonome qui peut lire, écrire, exécuter et raisonner dans le contexte de votre projet. Cette autonomie exige une discipline de travail adaptée.

Ce chapitre explique comment structurer efficacement une session de travail avec un agent comme Claude Code, et comment configurer l'environnement pour en tirer le meilleur parti.

---

## 5.1 — Agent vs Chatbot : comprendre la différence

| Caractéristique | Chatbot (ChatGPT, Claude.ai) | Agent (Claude Code, Cursor, Aider) |
|----------------|------------------------------|--------------------------------------|
| **Accès au code** | Aucun (copier-coller) | Accès direct au système de fichiers |
| **Exécution** | Ne peut rien exécuter | Peut lancer des commandes, des tests, des builds |
| **Modification** | Génère du texte à copier | Modifie directement les fichiers |
| **Contexte** | Ce que vous collez dans la conversation | L'ensemble du projet (fichiers, arborescence, git) |
| **Autonomie** | Répond à une question | Peut planifier et exécuter une séquence d'actions |
| **Persistance** | Session de chat | Configuration projet (CLAUDE.md), mémoire, historique |

### Conséquence sur le prompting

Avec un chatbot, vous devez fournir tout le contexte. Avec un agent, vous devez **guider l'intention** et laisser l'agent explorer.

```
── Chatbot : vous devez tout fournir ──
« Voici mon fichier UserService.ts : [200 lignes]
  Voici mon UserRepository.ts : [150 lignes]
  Voici l'erreur : [stack trace]
  Trouve le bug. »

── Agent : vous guidez, il explore ──
« Le test d'inscription échoue quand l'email contient des caractères 
  accentués. Diagnostique et corrige le problème. »
```

L'agent lira lui-même les fichiers pertinents, en explorera d'autres si nécessaire, lancera les tests, et proposera un correctif.

---

## 5.2 — Structurer une session de travail

### Phase 1 : Poser le cadre (1-2 prompts)

Commencez par donner à l'agent une vision claire de ce que vous voulez accomplir.

```
« Aujourd'hui je travaille sur la feature d'export PDF des factures. 
  Le ticket métier est : [description]. 
  Le code existant de génération de factures est dans src/services/InvoiceService. 
  On utilise la librairie PDFKit pour la génération de PDF. »
```

### Phase 2 : Explorer et comprendre (2-3 prompts)

Avant de coder, faites explorer le terrain à l'agent.

```
« Montre-moi comment sont actuellement générées les factures dans le projet. 
  Quels sont les points d'entrée et les données utilisées ? »
```

### Phase 3 : Planifier (1 prompt)

Demandez un plan avant de lancer l'implémentation.

```
« Propose un plan d'implémentation pour l'export PDF. 
  Découpe en étapes, chaque étape devant être testable indépendamment. »
```

### Phase 4 : Implémenter par étapes

Procédez étape par étape, en validant chaque étape avant de passer à la suivante.

```
« Implémente l'étape 1 : [description]. Lance les tests après. »
[Vérification]
« OK c'est bon. Étape 2 : [description]. »
```

### Phase 5 : Valider et consolider

```
« Lance tous les tests du projet pour vérifier qu'il n'y a pas de régression. »
« Fais une review du code que tu as écrit. Identifie les points faibles. »
```

### Anti-pattern : le « méga-prompt »

```
❌ « Implémente l'export PDF des factures. Le PDF doit contenir le logo, 
    les coordonnées du client, le détail des lignes, la TVA, le total, 
    un QR code de vérification, et doit être envoyé par email au client 
    avec une copie au service comptable. Ajoute les tests unitaires 
    et d'intégration. Mets à jour la documentation API. »
```

Ce type de prompt produit un résultat massif, difficile à valider, et souvent incohérent dans les détails. **Découpez toujours.**

---

## 5.3 — Configurer l'environnement : le fichier CLAUDE.md

### Qu'est-ce que CLAUDE.md ?

Le fichier `CLAUDE.md` (spécifique à Claude Code) est un fichier de configuration en Markdown placé à la racine du projet. Il est lu automatiquement par l'agent au début de chaque session. Il joue le rôle de **briefing permanent**.

### Que mettre dans CLAUDE.md

```markdown
# Contexte du projet

Ce projet est une API REST en .NET 8 pour la gestion de commandes e-commerce.
Architecture : Clean Architecture (Domain, Application, Infrastructure, API).
Base de données : PostgreSQL via Entity Framework Core.

# Conventions de code

- Nommage : PascalCase pour les classes et méthodes, camelCase pour les variables locales.
- Chaque endpoint a son propre fichier dans Controllers/.
- Les règles métier sont dans les Domain Services, jamais dans les Controllers.
- Les tests suivent le pattern Arrange-Act-Assert avec nommage en français.

# Stack technique

- .NET 8, C# 12
- Entity Framework Core 8
- MediatR pour CQRS
- FluentValidation pour la validation
- xUnit + Moq pour les tests
- Docker pour l'environnement local

# Commandes utiles

- `dotnet test` : lancer tous les tests
- `docker-compose up -d` : démarrer l'environnement local
- `dotnet ef migrations add <name>` : créer une migration

# Règles importantes

- Ne jamais modifier les fichiers de migration existants.
- Toujours ajouter un test pour chaque nouveau endpoint.
- Les secrets sont dans les variables d'environnement, jamais dans le code.
```

### Bonnes pratiques pour CLAUDE.md

| Faire | Ne pas faire |
|-------|--------------|
| Documenter les conventions non évidentes | Documenter ce qui est évident dans le code |
| Lister les commandes utiles | Écrire un tutoriel complet du projet |
| Préciser les interdits (« ne jamais... ») | Lister toutes les fonctionnalités du projet |
| Maintenir le fichier à jour | Le rédiger une fois et l'oublier |

### Équivalents dans d'autres outils

| Outil | Fichier de configuration |
|-------|--------------------------|
| Claude Code | `CLAUDE.md` |
| Cursor | Règles dans `.cursor/rules/` (l'ancien `.cursorrules` est déprécié) |
| GitHub Copilot | Instructions dans les settings ou fichier `.github/copilot-instructions.md` |
| Aider | `.aider.conf.yml` |

---

## 5.4 — La mémoire de l'agent

### Types de mémoire dans Claude Code

Claude Code dispose d'un système de mémoire hiérarchique :

| Niveau | Portée | Cas d'usage |
|--------|--------|-------------|
| **CLAUDE.md projet** | Tout le projet, tous les utilisateurs | Conventions, architecture, commandes |
| **CLAUDE.md utilisateur** | Cet utilisateur, tous les projets | Préférences personnelles de style, de langue |
| **Mémoire de session** | La session en cours | Contexte de travail actuel |
| **Mémoire persistante** | Inter-sessions, cet utilisateur | Retours d'expérience, préférences apprises |

### Utiliser la mémoire efficacement

```
── Demander à l'agent de se souvenir ──
« Retiens que dans ce projet, on utilise toujours les migrations 
  EF Core en mode "code first", jamais de SQL brut. »

── L'agent stocke cette information pour les sessions futures ──
```

```
── Corriger un comportement ──
« Non, ne génère pas de commentaires XML sur les méthodes privées. 
  On ne fait ça que sur les méthodes publiques. Retiens-le. »

── L'agent ajuste son comportement pour cette session et les suivantes ──
```

### Point de vigilance

La mémoire est un outil, pas une garantie. Vérifiez périodiquement que les informations mémorisées sont toujours correctes, surtout après des changements majeurs dans le projet.

---

## 5.5 — Quand découper en sous-tâches vs tout donner d'un coup

### Donner tout d'un coup : quand c'est approprié

- Tâches simples et bien définies.
- Modifications isolées sans impact transverse.
- Boilerplate standard (CRUD, DTO, mapping).

```
✅ « Ajoute un endpoint GET /api/users/{id}/orders qui retourne 
    les commandes d'un utilisateur avec pagination. 
    Utilise le même pattern que les endpoints existants. »
```

### Découper en sous-tâches : quand c'est nécessaire

- Tâches complexes touchant plusieurs modules.
- Modifications avec des dépendances entre étapes.
- Fonctionnalités nouvelles nécessitant des choix de conception.

```
✅ Séquence :
  1. « Analyse le système de notifications actuel. 
      Comment sont envoyés les emails aujourd'hui ? »
  2. « Je veux ajouter les notifications par SMS. 
      Propose une architecture qui s'intègre au système existant. »
  3. « Implémente l'interface ISmsProvider et un adapter pour Twilio. »
  4. « Ajoute les tests unitaires pour le SmsNotificationService. »
  5. « Modifie le NotificationOrchestrator pour supporter le canal SMS. »
```

### Règle de décision

```
La tâche implique-t-elle des choix de conception ?
  → OUI : Découper. L'agent propose, vous validez, puis il exécute.
  → NON : Donner tout d'un coup. L'agent exécute, vous validez.
```

---

## 5.6 — Gérer les permissions et la sécurité

### Le modèle de permissions de Claude Code

Claude Code fonctionne avec un système de permissions qui contrôle ce que l'agent peut faire :

| Action | Comportement par défaut |
|--------|------------------------|
| Lire des fichiers | Autorisé |
| Modifier des fichiers | Demande confirmation |
| Exécuter des commandes | Demande confirmation |
| Accéder au réseau | Demande confirmation |
| Supprimer des fichiers | Demande confirmation |

### Bonnes pratiques de sécurité

1. **Ne pas accorder les permissions « tout accepter » sur un projet sensible.** Validez chaque modification sur les fichiers critiques.
2. **Exclure les fichiers sensibles** via des règles `deny` dans `.claude/settings.json` (Claude Code ne reconnaît pas de fichier `.claudeignore`) :
   ```json
   {
     "permissions": {
       "deny": [
         "Read(.env*)",
         "Read(**/credentials/**)",
         "Read(**/secrets/**)",
         "Read(**/*.pem)",
         "Read(**/*.key)"
       ]
     }
   }
   ```
   Le [chapitre 9](09_configurer_environnement_ia.md) détaille ce mécanisme et ses limites.
3. **Vérifier les commandes avant exécution.** L'agent peut proposer des commandes destructives (suppression de fichiers, reset de base de données). Lisez toujours ce qu'il s'apprête à exécuter.
4. **Travailler sur une branche** pour isoler les modifications de l'agent.

---

## 5.7 — Les sous-agents : déléguer intelligemment

### Concept

Certains agents, comme Claude Code, peuvent lancer des **sous-agents** pour traiter des tâches en parallèle. Par exemple :

```
── Prompt ──
« Analyse les trois services suivants et identifie les incohérences 
  dans la gestion des erreurs : UserService, OrderService, PaymentService. »
```

L'agent peut lancer trois analyses en parallèle et consolider les résultats.

### Quand utiliser les sous-agents

- Analyse de plusieurs fichiers indépendants.
- Recherche large dans une codebase.
- Tâches parallélisables sans dépendances.

### Quand ne pas les utiliser

- Tâches séquentielles où chaque étape dépend de la précédente.
- Modifications qui nécessitent une cohérence globale.
- Quand vous avez besoin de valider chaque étape.

---

## 5.8 — Patterns de collaboration efficace

### Pattern 1 : Le pilote et le copilote

Vous définissez la direction, l'agent exécute.

```
Vous : « On va refactorer le module de paiement. Étape 1 : extraire l'interface. »
Agent : [exécute]
Vous : « Bien. Étape 2 : créer l'adapter Stripe. »
Agent : [exécute]
```

### Pattern 2 : L'explorateur

L'agent explore, vous décidez.

```
Vous : « Quelles sont les options pour implémenter le cache dans ce projet ? »
Agent : [analyse le projet, propose des options avec avantages/inconvénients]
Vous : « On part sur l'option 2. Implémente-la. »
```

### Pattern 3 : Le reviewer

Vous écrivez, l'agent review.

```
Vous : [écrivez du code]
Vous : « Review le code que je viens d'écrire dans PaymentService. 
        Focus sur la sécurité et la gestion d'erreurs. »
Agent : [identifie les problèmes]
```

### Pattern 4 : Le pair programmer

Travail collaboratif en temps réel.

```
Vous : « Travaillons ensemble sur ce bug de concurrence. 
        Voici ce que j'observe : [description]. 
        Qu'est-ce que tu en penses ? »
Agent : [analyse, propose une hypothèse]
Vous : « Bonne piste. Vérifie si le lock est bien acquis dans [fichier]. »
Agent : [vérifie, confirme ou infirme]
Vous : « OK, alors écris le fix. Je vais écrire le test de mon côté. »
```

---

## 5.9 — Erreurs courantes avec les agents

| Erreur | Conséquence | Solution |
|--------|-------------|----------|
| Lancer un refactoring massif sans branche dédiée | Modifications difficiles à annuler | Toujours travailler sur une branche |
| Accepter toutes les modifications sans relire | Code non maîtrisé en production | Review systématique des diffs |
| Sessions trop longues (2h+) | Perte de contexte, incohérences | Découper en sessions de 30-45 min |
| Ne pas configurer CLAUDE.md | L'agent redécouvre le projet à chaque session | Investir 15 min dans la configuration initiale |
| Utiliser l'agent pour des tâches triviales | Overhead inutile, perte de compétence | Garder les tâches simples pour vous |

---

## À retenir

1. **Un agent n'est pas un chatbot.** Adaptez votre approche à ses capacités : accès au code, exécution, autonomie.
2. **Structurez vos sessions** : cadre, exploration, plan, implémentation par étapes, validation.
3. **Configurez l'environnement** : CLAUDE.md (ou équivalent) est un investissement qui paie à chaque session.
4. **Découpez les tâches complexes** en sous-tâches validables indépendamment.
5. **Contrôlez les permissions** : vérifiez ce que l'agent fait, surtout les commandes et les modifications de fichiers critiques.
6. **Choisissez votre pattern de collaboration** selon la tâche : pilote/copilote, explorateur, reviewer, pair programmer.

---

*Chapitre précédent : [Les pièges classiques](04_pieges_classiques.md) | Chapitre suivant : [Revue et validation](06_revue_et_validation.md)*
