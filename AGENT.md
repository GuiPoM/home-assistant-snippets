# Agent - Instructions de Travail

Ce document décrit comment les agents spécialisés doivent aider Claude sur ce projet.

## 🤖 Rôle de l'Agent

L'agent aide Claude à:
- **Explorer rapidement** la structure du codebase
- **Chercher efficacement** dans les fichiers
- **Exécuter des tâches parallèles** de recherche/analyse
- **Fournir du contexte** avant que Claude n'agisse

## 📋 Tâches Typiques pour l'Agent

### 1. Exploration Codebase
**Quand**: Avant d'ajouter une nouvelle vidéo ou de modifier la structure
```
"Explore la structure actuelle du repository. 
Retourne: 
- Tous les fichiers sur chaque branche
- La structure des répertoires
- Résumé de ce qui existe déjà"
```

### 2. Audit de Sécurité
**Quand**: Avant tout push à GitHub
```
"Fais un audit de sécurité complet:
- Vérifie qu'il n'y a aucune credential/token/password
- Vérifie qu'il n'y a pas d'infos personnelles ou sensibles
- Vérifie les auteurs git
- Vérifie les noms de fichiers et chemins
Retourne les résultats avec fichiers et lignes exactes si problèmes trouvés"
```

### 3. Nettoyage de Code
**Quand**: Avant de créer une branche video
```
"Nettoie ce code pour le rendre générique et réutilisable:
- Remplace les détails spécifiques par des placeholders
- Remplace les tokens/credentials par YOUR_XXX
- Simplifie la logique pour être un exemple générique
- Marque les sections à personnaliser avec des commentaires
Retourne le code nettoyé"
```

### 4. Audit Git Complet
**Quand**: Avant de pousser ou après une opération git compliquée
```
"Fais un audit complet du git:
- Liste tous les commits avec auteurs et emails
- Cherche les anciennes références (reflog)
- Vérifie les branches orphan correctement créées
- Identifie les commits potentiellement à risque
Retourne un rapport structuré"
```

## 🎯 Paramètres de Recherche Génériques

### Types de Données à Détecter
```
- Credentials: token, api_key, password, secret, bearer, auth
- Emails: Tout email non-générique (xxx@users.noreply.github.com est ok)
- Chemins personnels: /home/*, /Users/*, C:\Users\*, chemins absolus spécifiques
- URLs hardcodées: dashboard URLs spécifiques, domaines personnels
- IDs spécifiques: entity IDs, device IDs, account IDs avec valeurs réelles
- Données sensibles: numéros de téléphone, adresses, données bancaires
```

### Fichiers à Analyser
- `*.md` (documentation)
- `*.yaml`, `*.yml` (configuration)
- `*.jinja`, `*.jinja2` (templates)
- `*.json`, `*.xml` (configuration)
- Ignorer: `.git/`, `.env` (ne doit pas être committable), fichiers binaires

## 🔄 Workflow Agent

### Avant une Nouvelle Vidéo
1. Explorer la structure actuelle
2. Vérifier quelles branches existent
3. Analyser les patterns utilisés dans les autres vidéos
4. Retourner à Claude avec le contexte

### Avant un Push
1. Audit git complet (auteurs, commits)
2. Audit de sécurité (credentials, infos sensibles)
3. Vérifier la structure des fichiers
4. Créer un rapport avec recommandations

### Après Modifications
1. Vérifier que rien n'a été cassé
2. Confirmer que tous les changements sont intentionnels
3. Alerter sur tout risque identifié

## 📊 Format de Rapport Standard

```markdown
## Audit Sécurité

**Résultat**: ✅ SAFE / ⚠️ WARNINGS / ❌ CRITICAL

**Credentials/Tokens Détectés:**
- Aucun / Listés si trouvés

**Infos Sensibles Détectées:**
- Aucune / Listés si trouvées

**État Git:**
- Commits: X
- Auteurs: [liste des auteurs uniques]
- Status: ✅ Propre

**Fichiers Analysés:**
- X fichiers scannés
- Y problèmes trouvés

**Recommandations:**
- [ ] Safe to push
- Ou liste des actions requises
```

## 🛠️ Spécifications Techniques

### Agent Type à Utiliser
- **explore**: Pour explorer la codebase rapidement et chercher des patterns
- **general**: Pour des tâches complexes multi-étapes (audits complets, nettoyage)

### Outils à Disposition
- Glob: Trouver des fichiers par pattern
- Grep: Chercher du contenu spécifique
- Read: Lire des fichiers complets
- Bash: Exécuter des commandes git

### Limites
- L'agent ne doit **jamais** pousser directement vers GitHub
- Toujours retourner un rapport à Claude qui décidera
- Demander confirmation avant opérations destructives

## 📌 Règles Critiques

1. **Privacy First** - Tout problème de sécurité doit être flaggé immédiatement
2. **Pas d'Assomptions** - Si incertain, demander à Claude ou retourner les données brutes
3. **Verbosité** - Retourner assez de détails pour que Claude prenne une décision informée
4. **Format Standard** - Utiliser les templates de rapports pour la cohérence
5. **Double-Check** - Les vérifications de sécurité doivent être exhaustives

## ✅ Checklist pour l'Agent

Avant de retourner un rapport:
- [ ] Toutes les branches ont été vérifiées
- [ ] Tous les types de données sensibles ont été recherchés
- [ ] Les résultats sont présentés clairement et structurés
- [ ] Les recommandations sont précises et actionnables
- [ ] Les fichiers/lignes critiques sont référencés quand applicable
- [ ] Le rapport n'expose pas lui-même les données sensibles trouvées
