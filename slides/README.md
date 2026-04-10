# Slides — Guide des bonnes pratiques IA

## Format

Les slides sont au format **Marp** (Markdown Presentation Ecosystem).

### Comment les utiliser

1. **VS Code** : Installer l'extension [Marp for VS Code](https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode), ouvrir un fichier `.md`, cliquer sur l'icône de preview.
2. **Export PDF/PPTX** : Via l'extension VS Code → Commande `Marp: Export Slide Deck`.
3. **CLI** : `npx @marp-team/marp-cli slides/01_mindset.md --pdf --theme slides/theme.css`

### Thème

Les slides utilisent un thème custom **corporate-ia** ([theme.css](theme.css)) avec :
- Palette bleu marine / bleu vif / ambre
- Slides de titre avec fond dégradé (classe `lead`)
- Slides de fin avec fond dégradé (classe `end`)
- Tableaux stylisés avec header coloré et alternance de lignes
- Blocs de code avec fond sombre et coins arrondis
- Citations avec bordure ambrée sur fond clair

Le fichier `.vscode/settings.json` est configuré pour que Marp charge automatiquement le thème dans VS Code.

## Sommaire

| Fichier | Chapitre | Durée estimée |
|---------|----------|---------------|
| [00_introduction.md](00_introduction.md) | Introduction | 5 min |
| [01_mindset.md](01_mindset.md) | Mindset — L'esprit critique | 10 min |
| [02_prompting.md](02_prompting.md) | L'art du prompt | 15 min |
| [03_workflows.md](03_workflows.md) | Workflows développeurs | 15 min |
| [04_pieges.md](04_pieges.md) | Les pièges classiques | 10 min |
| [05_agents.md](05_agents.md) | Travailler avec un agent | 15 min |
| [06_validation.md](06_validation.md) | Revue et validation | 10 min |
| [07_ethique.md](07_ethique.md) | Éthique et responsabilité | 10 min |
| [08_configuration.md](08_configuration.md) | Configurer son environnement | 10 min |

**Durée totale estimée : ~1h40**

Chaque module est autonome et peut être présenté indépendamment.
