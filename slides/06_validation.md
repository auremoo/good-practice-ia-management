---
marp: true
theme: corporate-ia
paginate: true
header: "Guide IA & Développement"
footer: "Chapitre 6 — Revue et validation"
---

<!-- _class: lead -->

# Chapitre 6 — Revue et validation

### Le code IA est du code non vérifié

---

# Les biais spécifiques au code IA

| Biais | Mécanisme |
|-------|-----------|
| **Complaisance** | Le code est propre → on baisse la garde |
| **Vitesse** | Produit vite → validé vite |
| **Autorité** | « L'IA l'a écrit » → on ne challenge pas |
| **Volume** | Beaucoup de code → on ne lit pas tout |
| **Confirmation** | Ça compile + tests passent → c'est bon |

---

# La checklist — 5 niveaux

### Niveau 1 — Compréhension (obligatoire)
- [ ] Je comprends **chaque ligne**
- [ ] Je comprends **pourquoi cette approche**
- [ ] J'ai identifié les **hypothèses implicites**

### Niveau 2 — Correction fonctionnelle (obligatoire)
- [ ] Le code **fait ce qui est demandé** (pas juste « compile »)
- [ ] Les **cas limites** sont gérés
- [ ] La **gestion d'erreurs** est appropriée

---

# La checklist (suite)

### Niveau 3 — Qualité technique
- [ ] Suit les **conventions du projet**
- [ ] Pas de **dépendance inutile** ajoutée
- [ ] Pas de **duplication** avec l'existant

### Niveau 4 — Sécurité (code exposé)
- [ ] Pas d'**injection** possible
- [ ] Entrées **validées et assainies**
- [ ] Pas de **secrets en dur**

### Niveau 5 — Intégration
- [ ] Tests existants **passent**
- [ ] Nouveaux tests **ajoutés**

---

# Les red flags

Signaux qui doivent déclencher une inspection approfondie :

| Red flag | Pourquoi c'est suspect |
|----------|----------------------|
| Fichier modifié **non mentionné** | Modification inattendue |
| `try/catch` qui **avale** l'exception | Problème masqué |
| `// TODO` ou `// FIXME` | Code incomplet |
| Utilisation de `any` ou `object` | Perte de typage |
| Linter ou check de sécurité **désactivé** | Garde-fou contourné |
| Code existant **réécrit** sans demande | Risque de régression |
| Tests sur le **happy path** uniquement | Fausse couverture |

---

# Technique : le test contradictoire

Écrire des tests qui **doivent faire échouer** le code :

```
Écris des tests qui essaient de CASSER processRefund() :
- Remboursement > total commande
- Remboursement en double
- Commande déjà annulée
- Montant négatif

Pour chaque test, indique le comportement attendu.
```

Si le code ne gère pas ces cas → problème identifié.

---

# Le flux de validation en équipe

```
  Développeur + IA → Code
         ↓
  Auto-review (checklist Niv.1-3)
         ↓
  Tests automatisés (CI/CD)
         ↓
  Review IA (2e passe, optionnelle)
         ↓
  Review humaine (obligatoire)
         ↓
  Merge
```

**Pas de voie rapide pour le code IA.**

---

# Automatiser les vérifications

| Outil | Ce qu'il attrape |
|-------|-----------------|
| **Linter** (ESLint, Pylint) | Style, conventions |
| **Analyseur statique** (SonarQube) | Bugs, vulnérabilités |
| **Tests auto** (Jest, xUnit) | Comportement fonctionnel |
| **Dependency checker** (Snyk) | Vulnérabilités dans les packages |
| **Type checker** (tsc, mypy) | Cohérence des types |

Ces outils forment un **filet de sécurité** qui attrape mécaniquement les erreurs les plus courantes du code IA.

---

# À retenir

1. Le code IA = **PR d'un inconnu** — même niveau de scrutin
2. **Checklist systématique** à 5 niveaux
3. Connaître les **red flags**
4. **Pas de voie rapide** — même processus que le code humain
5. **Automatiser** ce qui peut l'être (linter, CI, analyse statique)
6. La **review humaine reste indispensable**


---

<!-- _class: end -->

# Merci

> **L'IA est un amplificateur. Le facteur déterminant, c'est vous.**

Guide complet disponible sur le dépôt du projet
