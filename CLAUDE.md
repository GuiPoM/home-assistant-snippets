# Claude - Instructions de Travail

Ce document décrit la façon exacte dont Claude doit travailler sur ce projet.

## 🎯 Principes Généraux

### Sécurité et Confidentialité
- **Zéro trace personnelle/professionnelle** - Aucune information personnelle, email professionnel, ou credentials ne doit être visible publiquement
- **Vérification systématique** - Avant chaque push, vérifier que l'historique git est propre
  ```bash
  git log --all --pretty=format:"%H %an <%ae>"
  git log --all --name-status  # Vérifier les fichiers modifiés
  ```
- **Attribution correcte** - Tous les commits doivent être attribués à un compte GitHub neutre (ex: `GuiPoM <guipoM@users.noreply.github.com>`)
- **Pas de credentials** - Ne jamais committer de tokens, API keys, passwords, ou secrets

### Gestion Git
- **Squash les commits** - Un seul commit par branche (ou par feature logique)
- **Messages clairs** - Format: `[branche]: Description courte et significative`
- **Pas de force push inutile** - Utiliser `--force-with-lease` seulement si nécessaire
- **Vérifier avant de pousser** - Toujours faire `git log` et `git status` avant le push final

### Organisation du Repo
- **Main branch (master)** = Hub documentation uniquement (README.md, CONTRIBUTING.md, LICENSE, CLAUDE.md, AGENT.md)
- **Video branches** = Orphan branches nommées `video/[slug]` (ex: `video/mon-projet`)
- **Structure files** = À la racine de chaque branche video (pas de nested directories)

### Implémentation Sécurisée
- **Ne pas exposer le code personnel** - Remplacer la logique réelle par des implémentations stub/génériques
- **Marquer les sections personnalisables** - Ajouter des commentaires "À personnaliser par l'utilisateur"
- **Supprimer les détails sensibles** - URLs hardcodées, entity IDs spécifiques, chemins d'accès personnels, etc.
- **Utiliser des placeholders** - `YOUR_TOKEN`, `YOUR_ID`, `YOUR_CONFIG`, etc.

## 📋 Workflow Standard pour une Nouvelle Vidéo

### 1. Créer la Branche Orphan
```bash
git checkout --orphan video/[slug]
git rm -rf .
# Ajouter les fichiers
git add .
git commit -m "video/[slug]: [Description du contenu]"
```

### 2. Nettoyer et Préparer les Fichiers
- Simplifier le code réel en implémentation générique
- Remplacer tous les détails spécifiques par des placeholders
- Ajouter commentaires de customisation pour l'utilisateur

### 3. Vérifier la Sécurité
```bash
# Afficher tous les commits et auteurs
git log --all --pretty=format:"%H %an <%ae>"

# Vérifier qu'il n'y a pas d'infos sensibles dans les fichiers
git log --all -p | grep -i "token\|password\|secret\|api" || echo "Aucune credential détectée"
```

### 4. Pousser
```bash
git push -u origin video/[slug]
```

## 🔍 Checklist Avant Chaque Push

- [ ] Historique git propre (auteur correct, messages clairs)
- [ ] Pas de trace personnelle/professionnelle dans les fichiers
- [ ] Pas de credentials/tokens/secrets exposés
- [ ] Messages de commit structurés et significatifs
- [ ] Un seul auteur (compte GitHub neutre)
- [ ] Fichiers à la racine, pas de nested directories
- [ ] README.md sur chaque branche video avec documentation complète
- [ ] Tous les détails spécifiques remplacés par des placeholders ou des instructions de customisation

## 📝 Structure des Fichiers Vidéo

```
video/[slug]/
├── README.md                    # Documentation complète avec instructions de customisation
├── [content-files]/
│   ├── file1.yaml
│   ├── file2.yaml
│   └── ...
└── [optional-subdirs]/
    └── ...
```

## 🛠️ Outils à Utiliser

- **git**: Pour tout ce qui est versionning et vérification d'historique
- **grep**: Pour vérifier les traces sensibles
- **gh**: Pour créer/supprimer repos GitHub si nécessaire
- **TodoWrite**: Pour tracker les étapes d'une tâche complexe

## ⚡ Résolution de Problèmes

### Contributeur fantôme GitHub
Si GitHub affiche un ancien contributeur après nettoyage:
1. Force push: `git push --force-with-lease`
2. Si persiste: Supprimer et recréer le repo (GitHub cache les données)

### Ancien commit avec mauvaises credentials
```bash
# Vérifier
git log --all --pretty=format:"%an <%ae>" | sort | uniq

# Si problème détecté, recréer le repo proprement
```

### Historique reflog pollué
- Le reflog local ne sera jamais poussé (ok d'ignorer)
- Nettoyer si nécessaire: `git gc --prune=now`

## 📌 Notes Importantes

- Ne **JAMAIS** utiliser d'email professionnel dans les commits
- Ne **JAMAIS** commit des fichiers `.env` ou contenant des credentials
- Ne **JAMAIS** inclure de code production réel sans le simplifier
- **TOUJOURS** vérifier `git log` avant de pousser
- **TOUJOURS** créer une branche orphan pour chaque vidéo (isoler les histoires)
- **TOUJOURS** utiliser des placeholders pour les infos spécifiques à l'utilisateur

## 🔒 Privacy First

Le principe fondamental est: **Si tu ne veux pas que ça soit public, ne le commit pas.**
Quand en doute, remplace par un placeholder ou une instruction de customisation.
