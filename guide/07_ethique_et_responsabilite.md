# Chapitre 7 — Éthique et responsabilité

## Principe directeur

> L'IA ne décharge pas le développeur de sa responsabilité. Elle l'augmente. Plus l'outil est puissant, plus la responsabilité de celui qui l'utilise est grande.

Ce chapitre aborde les dimensions éthiques, juridiques et professionnelles de l'utilisation de l'IA dans le développement logiciel.

---

## 7.1 — La responsabilité du code

### Le principe fondamental

**Le code qui entre en production est votre code.** Quelle que soit la proportion générée par IA, c'est le développeur qui le soumet, le reviewer qui l'approuve, et l'équipe qui le déploie qui en portent la responsabilité.

### Les implications concrètes

| Situation | Responsabilité |
|-----------|---------------|
| Un bug en production causé par du code généré par IA | Le développeur qui a intégré le code |
| Une faille de sécurité dans du code IA | L'équipe qui l'a validé et déployé |
| Une régression introduite par un refactoring IA | Le développeur qui a lancé le refactoring |
| Des données client exposées à cause d'un logging IA | L'organisation (RGPD, responsabilité légale) |

### Ce que cela signifie en pratique

- « L'IA l'a écrit comme ça » n'est jamais une justification acceptable.
- Si vous ne comprenez pas le code, vous ne pouvez pas en assumer la responsabilité. Donc vous ne devez pas le merger.
- La vitesse de production ne justifie pas un relâchement de la qualité.

---

## 7.2 — Propriété intellectuelle et licences

### Les questions ouvertes

Le cadre juridique autour du code généré par IA est encore en évolution. Plusieurs questions restent partiellement tranchées :

| Question | État actuel (2026) |
|----------|-------------------|
| Qui est l'auteur du code généré par IA ? | Juridiquement flou. La plupart des juridictions considèrent qu'un humain doit être impliqué dans la création pour revendiquer un droit d'auteur. |
| Le code IA peut-il être protégé par le droit d'auteur ? | Incertain. Des décisions récentes suggèrent que le code purement généré sans intervention humaine significative n'est pas protégeable. |
| Le code IA peut-il violer des licences existantes ? | Oui. Si l'IA reproduit du code sous licence (GPL, AGPL), les obligations de licence s'appliquent. |
| Mon entreprise peut-elle breveter du code IA ? | Dépend de la juridiction et du degré d'intervention humaine. |

### Les risques concrets

#### Risque de contamination de licence

L'IA a été entraînée sur d'immenses quantités de code open source, y compris du code sous licences contraignantes (GPL, AGPL). Il existe un risque que le code généré reproduise des portions de code sous ces licences, ce qui pourrait imposer des obligations de licence à votre projet.

**Mitigation** :
- Vérifiez que le code généré n'est pas une reproduction directe de code open source existant (outils comme `licensee`, `scancode`).
- Pour les projets commerciaux sensibles, consultez votre service juridique.
- Privilégiez les fournisseurs d'IA qui offrent des garanties de propriété intellectuelle (comme la protection IP d'Anthropic pour Claude ou la protection IP de GitHub pour Copilot Business).

#### Risque de divulgation de code propriétaire

En envoyant votre code à un service IA, vous le transmettez à un tiers. Selon les conditions d'utilisation du service :
- Le code pourrait être utilisé pour l'entraînement du modèle (vérifiez les CGU).
- Le code transite par des serveurs tiers (même s'il n'est pas stocké).

**Mitigation** :
- Utilisez des versions entreprise des outils (Claude Team/Enterprise, Copilot Business).
- Vérifiez les politiques de rétention et d'entraînement de chaque fournisseur.
- Pour le code hautement confidentiel, envisagez des solutions on-premise.

---

## 7.3 — Transparence avec l'équipe

### Pourquoi la transparence est importante

L'utilisation de l'IA dans le développement n'est ni honteuse ni problématique en soi. Ce qui est problématique, c'est le manque de transparence qui peut :

- Induire l'équipe en erreur sur vos compétences réelles.
- Masquer des risques (code non compris intégré en production).
- Créer des attentes irréalistes sur la productivité.
- Empêcher l'équipe de mettre en place les garde-fous appropriés.

### Bonnes pratiques de transparence

| Pratique | Pourquoi |
|----------|----------|
| Mentionner l'utilisation de l'IA en standup quand c'est pertinent | Permet à l'équipe d'ajuster le niveau de review |
| Indiquer dans les PR quand du code a été significativement assisté par IA | Alerte les reviewers sur les biais spécifiques à vérifier |
| Partager les retours d'expérience (positifs et négatifs) | L'équipe apprend collectivement |
| Documenter les patterns de prompting efficaces | Évite que chacun réinvente la roue |

### Ce qui n'est pas nécessaire

- Déclarer chaque autocomplétion de Copilot. L'assistance de type « complétion » est comparable à l'autocomplétion classique d'un IDE.
- S'excuser d'utiliser l'IA. C'est un outil professionnel légitime.
- Détailler le pourcentage exact de code IA vs humain. La granularité n'est ni mesurable ni utile.

### La règle de la proportionnalité

Plus l'IA a contribué significativement au résultat, plus la transparence est importante :

```
Complétion de 2-3 lignes → Pas nécessaire de signaler
Génération d'une méthode entière → Mention optionnelle
Génération d'un module complet → Mention recommandée dans la PR
Architecture proposée par IA → Discussion d'équipe recommandée
```

---

## 7.4 — L'impact sur les compétences de l'équipe

### Le risque d'érosion des compétences

L'utilisation systématique de l'IA peut éroder progressivement les compétences fondamentales d'une équipe :

- Les juniors qui n'apprennent jamais à résoudre seuls.
- Les seniors qui perdent le réflexe de raisonner sans assistance.
- L'équipe qui ne sait plus faire tourner le projet si l'IA est indisponible.

### Stratégies de maintien des compétences

1. **Sessions de code sans IA** : réserver des moments (kata, mob programming) où l'IA est volontairement désactivée.
2. **Rotation des outils** : alterner entre périodes avec et sans IA pour maintenir les deux modes de travail.
3. **Mentorat renforcé pour les juniors** : s'assurer que les juniors comprennent les fondamentaux avant de dépendre de l'IA.
4. **Code reviews approfondies** : utiliser la review comme moment d'apprentissage, en demandant au développeur d'expliquer le code IA qu'il soumet.
5. **Entretiens techniques réalistes** : évaluer les candidats sur leurs compétences propres, pas sur leur capacité à prompter.

### Politique d'équipe suggérée

```
L'IA est un outil de productivité, pas un substitut à la compétence.

1. Chaque développeur doit pouvoir travailler sans IA si nécessaire.
2. Les juniors doivent maîtriser les fondamentaux avant d'utiliser 
   l'IA comme accélérateur.
3. La formation continue reste obligatoire, avec ou sans IA.
4. L'évaluation de performance porte sur la qualité du travail livré, 
   pas sur la méthode de production.
```

---

## 7.5 — La confidentialité et le RGPD

### Les données personnelles dans les prompts

Lorsqu'un développeur copie-colle du code contenant des données personnelles (noms, emails, adresses, données médicales, financières) dans un outil IA, il effectue un **transfert de données personnelles à un tiers**. Ce transfert est soumis au RGPD si les données concernent des résidents de l'Union européenne.

### Les obligations

| Obligation RGPD | Application à l'IA |
|-----------------|---------------------|
| **Base légale** | Le transfert de données personnelles à un fournisseur IA doit reposer sur une base légale (contrat, intérêt légitime, consentement) |
| **Minimisation** | Ne transmettre que les données strictement nécessaires |
| **Information** | Les personnes concernées doivent être informées du traitement |
| **Sous-traitance** | Le fournisseur IA est un sous-traitant au sens du RGPD — un contrat de sous-traitance (DPA) est nécessaire |
| **Transfert hors UE** | Si les serveurs IA sont hors UE, les garanties appropriées doivent être en place |

### Règle pratique

**Ne jamais soumettre de vraies données personnelles à un outil IA.** Utilisez des données de test, des données anonymisées ou des données fictives.

```
── Ce qu'il ne faut JAMAIS faire ──
« Voici les logs de l'erreur :
  User jean.dupont@entreprise.fr (ID: 12345) a tenté de... »

── Ce qu'il faut faire ──
« Voici les logs de l'erreur (données anonymisées) :
  User test@example.com (ID: XXXXX) a tenté de... »
```

---

## 7.6 — L'utilisation responsable des ressources

### L'empreinte environnementale

L'inférence des modèles d'IA consomme des ressources significatives (énergie, eau pour le refroidissement des datacenters). Sans tomber dans le catastrophisme, une utilisation raisonnée est de mise.

### Bonnes pratiques

- **Éviter les prompts inutiles** : ne demandez pas à l'IA de régénérer la même réponse cinq fois en espérant un meilleur résultat. Affinez votre prompt.
- **Utiliser le bon outil pour la bonne tâche** : un `grep` dans le terminal est plus approprié qu'un prompt IA pour chercher une chaîne dans un fichier.
- **Choisir le bon modèle** : pour des tâches simples (complétion, formatage), un modèle léger suffit. Réservez les modèles avancés pour les tâches complexes.

---

## 7.7 — Cadre décisionnel éthique

Pour les situations ambiguës, utilisez ce cadre de questions :

```
┌────────────────────────────────────────────────────────────┐
│              CADRE DÉCISIONNEL ÉTHIQUE                      │
│                                                             │
│  1. Suis-je en train de comprendre ce que l'IA produit,     │
│     ou simplement de le copier ?                            │
│     → Si je copie sans comprendre : STOP                    │
│                                                             │
│  2. Si ce code cause un problème en production,             │
│     serais-je capable de l'expliquer et de le corriger ?    │
│     → Si non : ne pas merger                                │
│                                                             │
│  3. Suis-je en train de transmettre des données             │
│     sensibles à un tiers ?                                  │
│     → Si oui : anonymiser d'abord                           │
│                                                             │
│  4. Un collègue qui lirait cette PR saurait-il              │
│     que de l'IA a été utilisée significativement ?           │
│     → Si c'est important et invisible : le signaler         │
│                                                             │
│  5. Est-ce que j'apprends quelque chose dans le processus,  │
│     ou est-ce que je deviens plus dépendant ?               │
│     → Si dépendant : varier les approches                   │
│                                                             │
│  6. Mon utilisation de l'IA serait-elle défendable           │
│     si elle était rendue publique ?                          │
│     → Si non : reconsidérer                                 │
└────────────────────────────────────────────────────────────┘
```

---

## 7.8 — Vers une politique d'entreprise

### Les éléments d'une politique IA pour les équipes de développement

Une politique d'utilisation de l'IA au sein d'une organisation devrait couvrir :

1. **Outils autorisés** : liste des outils IA approuvés par l'entreprise, avec les versions et les plans (gratuit, team, enterprise).
2. **Périmètre d'utilisation** : quels projets, quels types de code, quels environnements.
3. **Données** : quelles données peuvent être soumises à l'IA, sous quelles conditions.
4. **Propriété intellectuelle** : position de l'entreprise sur la propriété du code généré.
5. **Transparence** : attentes en matière de déclaration de l'utilisation de l'IA.
6. **Formation** : programme de formation et de montée en compétence.
7. **Review** : processus de validation adapté au code assisté par IA.
8. **Révision** : fréquence de mise à jour de la politique (le domaine évolue rapidement).

### Modèle de charte en une page

```
CHARTE D'UTILISATION DE L'IA EN DÉVELOPPEMENT

1. L'IA est un outil d'assistance. Le développeur reste responsable 
   de tout le code qu'il soumet.

2. Les outils suivants sont autorisés : [liste].

3. Ne jamais soumettre de données personnelles, de secrets, 
   ou de code soumis à des clauses de confidentialité renforcée.

4. Tout code assisté par IA suit le même processus de review 
   que le code écrit manuellement.

5. L'utilisation de l'IA ne dispense pas de la compréhension 
   du code produit.

6. En cas de doute sur l'utilisation appropriée de l'IA, 
   consulter [responsable technique / DPO / manager].

Date : ____________     Signature : ____________
```

---

## À retenir

1. **Vous êtes responsable** du code que vous soumettez, quelle que soit son origine.
2. **La propriété intellectuelle est un terrain mouvant.** Restez informé et consultez votre service juridique en cas de doute.
3. **La transparence est une vertu professionnelle**, pas un aveu de faiblesse.
4. **Protégez les données** : anonymisez systématiquement avant de soumettre à l'IA.
5. **Investissez dans les compétences** : l'IA ne remplace pas l'apprentissage.
6. **Structurez l'utilisation** avec une politique d'équipe claire et évolutive.

---

*Chapitre précédent : [Revue et validation](06_revue_et_validation.md)*

---

## Conclusion du guide

Ce guide a posé les bases d'une utilisation professionnelle, réfléchie et responsable de l'intelligence artificielle dans le développement logiciel. Les principes fondamentaux sont simples :

- **L'esprit critique** est votre meilleur outil.
- **La communication** avec l'IA est une compétence qui se travaille.
- **La validation** du code IA est non négociable.
- **La responsabilité** reste toujours humaine.

L'IA ne fera pas de vous un meilleur développeur automatiquement. Mais utilisée avec méthode, discernement et éthique, elle peut considérablement amplifier votre impact.

> L'avenir n'appartient pas aux développeurs qui utilisent l'IA. Il appartient aux développeurs qui savent **bien** utiliser l'IA.

---

*Retour au [sommaire](../README.md)*
