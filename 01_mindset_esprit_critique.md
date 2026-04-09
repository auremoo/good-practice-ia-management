# Chapitre 1 — Mindset : l'esprit critique avant tout

## Principe directeur

> Vous êtes le développeur. L'IA est votre assistant. Jamais l'inverse.

Cette affirmation peut sembler évidente. Elle ne l'est pas dans la pratique. Lorsqu'un agent IA produit une réponse fluide, structurée et confiante, le réflexe naturel est de l'accepter. C'est précisément ce réflexe qu'il faut apprendre à maîtriser.

---

## 1.1 — Le biais de confiance

### Le phénomène

L'IA générative produit du texte (et du code) avec une assurance constante. Elle ne marque jamais d'hésitation. Elle ne dit jamais « je ne sais pas » de manière spontanée. Cette fluidité crée un **biais de confiance** : plus une réponse est bien formulée, plus elle paraît correcte.

### Le danger

Un code syntaxiquement parfait, bien commenté, avec des noms de variables explicites peut être **fonctionnellement faux**. L'IA peut :

- Inventer une API qui n'existe pas dans la version de la librairie utilisée.
- Proposer une logique métier qui semble cohérente mais ne correspond pas au besoin réel.
- Générer une requête SQL performante... sur un schéma de données qui ne correspond pas au vôtre.

### La parade

**Appliquer le même niveau d'exigence au code généré par IA qu'au code d'un collègue en revue.** Si vous ne valideriez pas un merge request sans la lire, ne validez pas une suggestion IA sans la comprendre.

---

## 1.2 — Savoir quand utiliser l'IA... et quand ne pas l'utiliser

L'IA n'est pas la réponse universelle. Son utilisation doit être un choix délibéré, pas un automatisme.

### Situations où l'IA apporte une forte valeur ajoutée

| Situation | Pourquoi l'IA est pertinente |
|-----------|------------------------------|
| Écrire des tests unitaires pour du code existant | Tâche répétitive, patron clair, le développeur valide la pertinence |
| Explorer une codebase inconnue | L'IA peut synthétiser, expliquer, naviguer rapidement |
| Générer du boilerplate | Code à faible valeur intellectuelle mais nécessaire |
| Traduire une logique d'un langage à un autre | L'IA connaît les idiomes des deux langages |
| Rédiger de la documentation technique | L'IA structure bien l'information factuelle |
| Déboguer une erreur avec un stack trace | L'IA peut identifier rapidement des patterns d'erreur courants |

### Situations où l'IA doit être utilisée avec précaution

| Situation | Pourquoi la prudence s'impose |
|-----------|-------------------------------|
| Architecture d'un nouveau système | L'IA n'a pas le contexte métier, les contraintes d'équipe, l'historique |
| Choix de technologie | L'IA peut avoir un biais vers les technologies les plus documentées, pas les plus adaptées |
| Code lié à la sécurité | Cryptographie, authentification, autorisation : le risque d'erreur subtile est trop élevé |
| Optimisation de performance | L'IA ne connaît pas votre profil de charge, vos bottlenecks réels |
| Décisions irréversibles | Migration de base de données, suppression de données : l'IA ne mesure pas l'impact |

### Situations où l'IA ne devrait pas décider

- Toute décision qui engage la responsabilité juridique ou contractuelle.
- Les arbitrages qui impliquent des compromis humains (délais, priorités, charge d'équipe).
- La validation finale de conformité réglementaire.

---

## 1.3 — Le piège du « ça marche, donc c'est bon »

### Le scénario

Un développeur demande à l'IA de générer une fonction. Le code compile. Les tests passent. Il est mergé.

Trois mois plus tard, un bug apparaît dans un cas limite. Le développeur ouvre le fichier et réalise qu'il ne comprend pas la logique. Il ne peut pas le déboguer efficacement parce qu'il n'a jamais compris le code qu'il a validé.

### La règle

> **Ne jamais merger du code que l'on ne serait pas capable de réécrire soi-même.**

Cette règle ne signifie pas qu'il faut réécrire le code. Elle signifie qu'il faut être en mesure de le faire. Si vous ne pouvez pas expliquer chaque ligne à un collègue, vous n'avez pas terminé votre travail.

### Application pratique

Après avoir reçu du code de l'IA, posez-vous ces questions :

1. **Qu'est-ce que ce code fait exactement ?** (pas « en gros », mais précisément)
2. **Pourquoi cette approche plutôt qu'une autre ?**
3. **Quels sont les cas limites non couverts ?**
4. **Quelles sont les hypothèses implicites ?** (format des données, état de la base, droits d'accès...)
5. **Que se passe-t-il en cas d'erreur ?** (timeout, données manquantes, entrée invalide...)

---

## 1.4 — L'IA comme miroir de votre niveau

Un phénomène souvent sous-estimé : **la qualité des résultats obtenus avec l'IA est directement proportionnelle à votre expertise technique**.

### Pourquoi

- Un développeur expérimenté sait quelles questions poser.
- Il sait détecter quand la réponse est fausse ou incomplète.
- Il sait donner du contexte pertinent.
- Il sait découper un problème complexe en sous-problèmes que l'IA peut traiter.

### Conséquence

L'IA ne remplace pas l'apprentissage. Un junior qui utilise l'IA sans monter en compétence restera un junior — avec un faux sentiment de productivité. L'investissement dans les fondamentaux (algorithmique, design patterns, architecture, tests) reste indispensable.

> **L'IA accélère le développeur compétent. Elle ralentit le développeur qui ne cherche pas à comprendre.**

---

## 1.5 — La posture professionnelle

### Ce que l'on attend d'un développeur utilisant l'IA

| Comportement attendu | Comportement à éviter |
|----------------------|----------------------|
| Utilise l'IA pour accélérer un travail qu'il sait faire | Utilise l'IA pour faire un travail qu'il ne comprend pas |
| Relit et challenge chaque suggestion | Accepte en bloc sans relecture |
| Documente quand l'IA a assisté significativement | Fait passer du code IA pour du code entièrement personnel |
| Signale les limites de l'IA à son équipe | Survend les capacités de l'IA |
| Utilise l'IA comme outil d'apprentissage | Utilise l'IA comme substitut à l'apprentissage |

### L'analogie du GPS

L'IA est comparable à un GPS pour un chauffeur de taxi :

- Un bon chauffeur utilise le GPS pour optimiser ses trajets, découvrir de nouvelles routes, gagner du temps.
- Il sait quand le GPS se trompe (travaux, sens interdits récents, raccourcis locaux).
- Il peut conduire sans le GPS si nécessaire.
- Un chauffeur qui ne connaît pas sa ville et dépend entièrement du GPS est vulnérable à la moindre défaillance.

---

## À retenir

1. **Le biais de confiance est votre premier ennemi.** Un code bien présenté n'est pas nécessairement un code correct.
2. **L'IA est un outil, pas un oracle.** Choisissez délibérément quand l'utiliser.
3. **Compréhension avant validation.** Ne mergez jamais du code que vous ne comprenez pas.
4. **L'IA amplifie votre niveau.** Investissez dans vos fondamentaux.
5. **Vous êtes responsable.** Le code qui entre en production est votre code, quelle que soit son origine.

---

*Chapitre précédent : [Introduction](00_introduction.md) | Chapitre suivant : [L'art du prompt](02_art_du_prompt.md)*
