---
marp: true
theme: corporate-ia
paginate: true
header: "Guide IA & Développement"
footer: "Chapitre 1 — Mindset"
---

<!-- _class: lead -->

# Chapitre 1 — Mindset

### L'esprit critique avant tout

---

# La règle n°1

> Vous êtes le développeur. L'IA est votre assistant.
> **Jamais l'inverse.**

---

# Le biais de confiance

L'IA produit du texte avec une **assurance constante**.

Elle ne dit jamais « je ne sais pas » spontanément.

Plus une réponse est bien formulée, plus elle **paraît** correcte.

### Le danger
Un code syntaxiquement parfait peut être **fonctionnellement faux**.

---

# Ce que l'IA peut inventer

- Une API qui **n'existe pas** dans votre version de librairie
- Une logique métier **plausible mais incorrecte**
- Un flag de commande qui **n'a jamais existé**
- Un package npm **fictif**

**La parade** : appliquer le même niveau d'exigence qu'à une code review d'un collègue.

---

# Quand utiliser l'IA

| Forte valeur ajoutée | Prudence requise |
|---------------------|------------------|
| Tests unitaires | Architecture système |
| Explorer une codebase | Choix de techno |
| Boilerplate / CRUD | Code sécurité / crypto |
| Debug avec stack trace | Décisions irréversibles |
| Documentation | Optimisation perf |
| Apprendre un concept | Arbitrages humains |

---

# Le piège du « ça marche, donc c'est bon »

Le code compile. Les tests passent. On merge.

3 mois plus tard : un bug. Personne ne comprend le code.

> **Règle : ne jamais merger du code que l'on ne serait pas capable de réécrire soi-même.**

---

# 5 questions à se poser

1. **Qu'est-ce que ce code fait exactement ?** (pas « en gros »)
2. **Pourquoi cette approche plutôt qu'une autre ?**
3. **Quels sont les cas limites non couverts ?**
4. **Quelles sont les hypothèses implicites ?**
5. **Que se passe-t-il en cas d'erreur ?**

---

# L'IA = miroir de votre niveau

Un développeur expérimenté :
- Sait **quelles questions** poser
- Sait **détecter** quand la réponse est fausse
- Sait **donner du contexte** pertinent
- Sait **découper** un problème complexe

> L'IA accélère le développeur compétent.
> Elle ralentit celui qui ne cherche pas à comprendre.

---

# L'analogie du GPS

Un bon chauffeur de taxi :
- Utilise le GPS pour **optimiser**, pas pour **remplacer** sa connaissance
- Sait quand le GPS **se trompe**
- Peut conduire **sans** le GPS

Un chauffeur qui dépend entièrement du GPS est vulnérable à la moindre défaillance.

**Vous êtes le chauffeur. L'IA est le GPS.**

---

# À retenir

1. Le **biais de confiance** est votre premier ennemi
2. L'IA est un **outil**, pas un oracle
3. **Compréhension** avant validation
4. L'IA **amplifie** votre niveau — investissez dans vos fondamentaux
5. **Vous êtes responsable** du code, quelle que soit son origine


---

<!-- _class: end -->

# Merci

> **L'IA est un amplificateur. Le facteur déterminant, c'est vous.**

Guide complet disponible sur le dépôt du projet
