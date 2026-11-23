<!-- good-practice-ia-management — Auteur : Aurélien Moote - Moo - 2026 — Licence MIT -->

# Chapitre 0 — Introduction

## Pourquoi ce guide existe

L'intelligence artificielle générative a transformé le quotidien du développeur en l'espace de quelques années. Des outils comme Claude Code, GitHub Copilot, ChatGPT ou Cursor sont devenus des compagnons de travail pour des millions de professionnels. Cette adoption rapide a créé un fossé entre ceux qui utilisent ces outils avec discernement et ceux qui les subissent — ou pire, leur font une confiance aveugle.

Ce guide est né d'un constat simple : **la qualité du travail assisté par IA dépend avant tout de la compétence du développeur qui pilote l'outil**. Un marteau ne fait pas un charpentier. Un agent IA ne fait pas un ingénieur logiciel.

## Ce que ce guide est

- Un référentiel de bonnes pratiques, ancré dans la réalité du terrain.
- Un outil de réflexion sur la posture professionnelle face à l'IA.
- Un recueil d'exemples concrets, issus de situations réelles de développement.
- Un support de formation utilisable en autonomie ou en session collective.

## Ce que ce guide n'est pas

- Un tutoriel d'installation d'outils. Seuls les chapitres 8 et 9 entrent dans la configuration concrète (celle de Claude Code), parce qu'elle sert à automatiser les bonnes pratiques décrites dans les chapitres précédents.
- Un argumentaire pour ou contre l'utilisation de l'IA.
- Une liste figée de règles. Les pratiques évoluent rapidement ; ce guide doit être mis à jour régulièrement.

## Le problème que nous adressons

L'utilisation naïve de l'IA dans le développement logiciel engendre des risques concrets :

| Risque | Conséquence |
|--------|-------------|
| Accepter du code sans le comprendre | Dette technique invisible, bugs en production |
| Coller des données sensibles dans un prompt | Fuite de données, violation de conformité |
| Faire confiance aux suggestions de librairies | Dépendances inexistantes, vulnérabilités |
| Déléguer la réflexion architecturale | Solutions inadaptées, over-engineering |
| Ne pas vérifier les affirmations de l'IA | Propagation d'erreurs factuelles dans le code et la documentation |

Ces risques ne sont pas théoriques. Ils se produisent quotidiennement dans les équipes qui n'ont pas structuré leur approche.

## Le bénéfice d'une utilisation maîtrisée

À l'inverse, un développeur qui maîtrise l'interaction avec l'IA gagne en :

- **Productivité** : automatisation des tâches répétitives, exploration rapide de solutions.
- **Qualité** : génération de tests, détection d'anomalies, revue de code augmentée.
- **Apprentissage** : compréhension accélérée de nouvelles technologies, exploration de codebase.
- **Concentration** : réduction de la charge cognitive sur les tâches à faible valeur ajoutée.

Mais ces bénéfices ne se matérialisent que sous une condition : **l'esprit critique reste activé en permanence**.

## Comment utiliser ce guide

Chaque chapitre suit une structure cohérente :

1. **Principes** — Les idées fondamentales du chapitre.
2. **Mise en pratique** — Des exemples concrets, souvent sous forme de comparaisons avant/après.
3. **Points de vigilance** — Les erreurs courantes liées au sujet.
4. **À retenir** — Une synthèse des éléments essentiels.

Les exemples s'appuient principalement sur **Claude Code** (l'agent en ligne de commande et IDE d'Anthropic), mais les principes sont transposables à tout outil d'assistance au développement par IA : GitHub Copilot, Cursor, ChatGPT, Gemini, ou tout autre assistant.

---

> **Principe fondateur de ce guide** : L'IA est un amplificateur. Elle amplifie la compétence du développeur compétent. Elle amplifie aussi l'incompétence. Le facteur déterminant, c'est vous.

---

*Chapitre suivant : [Mindset — L'esprit critique avant tout](01_mindset_esprit_critique.md)*
