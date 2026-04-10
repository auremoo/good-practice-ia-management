---
marp: true
theme: corporate-ia
paginate: true
header: "Guide IA & Développement"
footer: "Chapitre 7 — Éthique et responsabilité"
---

<!-- _class: lead -->

# Chapitre 7 — Éthique et responsabilité

### L'IA ne vous décharge pas de votre responsabilité

---

# La règle fondamentale

> **Le code qui entre en production est votre code.**
> Quelle que soit la proportion générée par IA.

« L'IA l'a écrit comme ça » n'est **jamais** une justification acceptable.

---

# Qui est responsable ?

| Situation | Responsable |
|-----------|-------------|
| Bug en prod causé par du code IA | Le développeur qui l'a intégré |
| Faille de sécurité dans du code IA | L'équipe qui l'a validé et déployé |
| Données client exposées par un log IA | L'organisation (RGPD) |

---

# Propriété intellectuelle — Terrain mouvant

| Question | État actuel (2026) |
|----------|-------------------|
| Qui est l'auteur du code IA ? | Flou juridique |
| Le code IA est-il protégeable ? | Incertain sans intervention humaine significative |
| Le code IA peut-il violer des licences ? | **Oui** — risque réel (GPL, AGPL) |

### Risque concret
L'IA a été entraînée sur du code open source sous licences contraignantes.
Le code généré peut en reproduire des portions.

→ **Vérifiez** avec des outils de scan de licence pour les projets commerciaux.

---

# La transparence avec l'équipe

### Bonnes pratiques

| Quand | Action |
|-------|--------|
| Standup | Mentionner l'utilisation IA quand c'est pertinent |
| Pull request | Signaler quand le code est significativement assisté |
| Retours d'expérience | Partager ce qui marche et ce qui ne marche pas |

### Ce qui n'est pas nécessaire
- Déclarer chaque autocomplétion Copilot
- S'excuser d'utiliser l'IA
- Détailler le % IA vs humain

---

# La règle de proportionnalité

```
Complétion de 2-3 lignes     → Pas nécessaire de signaler
Génération d'une méthode      → Mention optionnelle
Génération d'un module entier → Mention recommandée dans la PR
Architecture proposée par IA  → Discussion d'équipe
```

---

# RGPD et données personnelles

Un prompt contenant des données personnelles = **transfert de données à un tiers**.

### La règle

> **Ne jamais soumettre de vraies données personnelles à un outil IA.**

```
❌  User jean.dupont@entreprise.fr (ID: 12345)
✅  User test@example.com (ID: XXXXX)
```

Utilisez des données fictives ou anonymisées. **Toujours.**

---

# Les 4 « JAMAIS »

```
┌──────────────────────────────────────────────────┐
│                                                   │
│  1. JAMAIS de secrets dans un prompt              │
│     (clés API, tokens, mots de passe)             │
│                                                   │
│  2. JAMAIS de vraies données clients              │
│     (anonymiser d'abord)                          │
│                                                   │
│  3. JAMAIS merger sans comprendre                 │
│     (« ça compile » ≠ « c'est correct »)          │
│                                                   │
│  4. JAMAIS valider l'IA par l'IA                  │
│     (review humaine obligatoire)                  │
│                                                   │
└──────────────────────────────────────────────────┘
```

---

# Le cadre décisionnel éthique

Avant chaque utilisation, 3 questions :

1. **Est-ce que je comprends** ce que l'IA produit,
   ou est-ce que je copie sans comprendre ?

2. **Si ça casse en prod**, serais-je capable
   d'expliquer et de corriger ?

3. **Est-ce que je transmets** des données sensibles
   à un tiers ?

Si une réponse vous met mal à l'aise → **reconsidérer**.

---

# Vers une politique d'entreprise

### Les éléments à couvrir

1. **Outils autorisés** et versions/plans approuvés
2. **Données** : quoi peut être soumis à l'IA, sous quelles conditions
3. **Transparence** : attentes de l'organisation
4. **Review** : processus adapté au code IA
5. **Formation** : programme de montée en compétence
6. **Révision** : mise à jour régulière de la politique

---

# À retenir

1. **Vous êtes responsable** — pas l'IA
2. **PI = terrain mouvant** — restez informé
3. **Transparence** = vertu professionnelle
4. **Protégez les données** — anonymisez toujours
5. **Investissez dans les compétences** — l'IA ne remplace pas l'apprentissage
6. **Structurez** avec une politique d'équipe claire


---

<!-- _class: end -->

# Merci

> **L'IA est un amplificateur. Le facteur déterminant, c'est vous.**

Guide complet disponible sur le dépôt du projet
