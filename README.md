# 🏠 Home Assistant Snippets

[![MIT License](https://img.shields.io/badge/License-MIT-green.svg)](./LICENSE)
[![Home Assistant](https://img.shields.io/badge/Home%20Assistant-2024.1%2B-blue)](https://www.home-assistant.io/)
[![Python](https://img.shields.io/badge/Python-3.11%2B-blue)](https://python.org)

> 🎬 Une collection de **blueprints Home Assistant** et **scripts Jinja** conçus pour accompagner mes tutoriels YouTube. Code simplifié, prêt à être adapté à votre installation personnelle.

---

## 📺 Tutoriels Vidéo

### 🌊 IKEA Klippbok + Home Assistant
**Sécurisez votre logement contre les inondations !**

[![Watch on YouTube](https://img.shields.io/badge/YouTube-FF0000?style=flat&logo=youtube&logoColor=white)](https://youtu.be/vnOwSnuNEPI)
[![Code on GitHub](https://img.shields.io/badge/Code-video/ikea--klippbok-blue)](https://github.com/GuiPoM/home-assistant-snippets/tree/video/ikea-klippbok)

**Ce que vous apprendrez :**
- ✅ Intégrer le détecteur IKEA Klippbok via Matter/Thread
- ✅ Construire une logique d'automatisation complète
- ✅ Gérer les sirènes et alertes intelligentes
- ✅ Créer des scripts d'alerte personnalisés
- ✅ Monitorer votre sécurité domotique

**Fichiers fournis :** 3 blueprints + 3 scripts + macros Jinja

---

## 🚀 Démarrage Rapide

### Pour un tutoriel spécifique

```bash
# Clonez la branche vidéo
git clone --branch video/ikea-klippbok https://github.com/GuiPoM/home-assistant-snippets.git
cd home-assistant-snippets

# Consultez le README pour les instructions
cat README.md
```

### Structure du Repository

```
📦 home-assistant-snippets
├── 📄 README.md                    # Cette page
├── 📄 CONTRIBUTING.md              # Politique d'utilisation
├── 📄 LICENSE                      # MIT License
├── 📄 CLAUDE.md                    # Instructions de travail
├── 📄 AGENT.md                     # Directives d'audit
└── 🎬 Branches vidéo (video/*)
    └── video/ikea-klippbok/        # Code complet + documentation
```

**Chaque branche vidéo est indépendante** - Vous pouvez clone une branche spécifique sans les autres.

---

## 💡 Comment Ça Marche

### 1️⃣ Choisissez un Tutoriel
Parcourez la liste des tutoriels ci-dessus et sélectionnez celui qui vous intéresse.

### 2️⃣ Consultez la Branche Vidéo
Chaque tutoriel a sa propre branche avec tout le code source et la documentation.

### 3️⃣ Adaptez à Votre Installation
Suivez le README de la branche pour adapter le code à votre configuration Home Assistant.

### 4️⃣ Testez et Déployez
Testez chaque composant et adaptez-le à vos besoins spécifiques.

---

## 📋 Configuration Requise

- **Home Assistant** 2024.1 ou plus récent
- **Python** 3.11 ou plus récent
- Les dépendances spécifiques à chaque tutoriel (voir README de la branche)

---

## 🔗 Ressources Utiles

| Ressource | Lien |
|-----------|------|
| 🏠 Home Assistant | https://www.home-assistant.io/ |
| 📱 Mon Hub | https://lhub.to/GuiPoM |
| 🔐 Alarmo (HACS) | https://github.com/nielsfaber/alarmo |
| 🧵 Thread | https://www.home-assistant.io/integrations/thread/ |
| 🔄 Matter | https://www.home-assistant.io/integrations/matter/ |

---

## 📜 Licence et Utilisation

### ⚠️ Important
Ce code est fourni **à titre informatif** pour accompagner mes tutoriels YouTube.

### 🚫 Ce Qui N'est PAS Autorisé
- ❌ Redistribuer le code
- ❌ Publier sans attribution
- ❌ Utiliser à titre commercial
- ❌ Soumettre des pull requests (contributions non acceptées)

### ✅ Ce Qui EST Autorisé
- ✅ Utiliser personnellement pour votre installation Home Assistant
- ✅ Adapter et modifier pour vos besoins
- ✅ Apprendre et comprendre le code
- ✅ Partager des améliorations via issues (discussions seulement)

### Licence

Code sous **[MIT License](./LICENSE)** - Usage personnel uniquement, compatible avec Home Assistant (Apache 2.0).

Pour plus de détails, consultez [CONTRIBUTING.md](./CONTRIBUTING.md).

---

## 🎓 Documentation

- **[CLAUDE.md](./CLAUDE.md)** - Instructions de travail pour Claude (IA)
- **[AGENT.md](./AGENT.md)** - Directives d'audit pour les agents
- **[CONTRIBUTING.md](./CONTRIBUTING.md)** - Politique de contribution

---

## 💬 Support

### Questions sur un Tutoriel?
1. 📺 Regardez la vidéo complète pour le contexte
2. 🔍 Consultez le README de la branche vidéo
3. 🐛 Vérifiez les logs Home Assistant

### Problèmes Rencontrés?
- Consultez la vidéo pour une explication détaillée
- Testez chaque composant indépendamment
- Vérifiez `Settings → System → Logs` dans Home Assistant

---

## 🎯 À Venir

D'autres tutoriels vidéo seront ajoutés régulièrement. Restez à l'écoute!

---

**Dernière mise à jour :** Mars 2026 | **Créé avec ❤️ pour la communauté Home Assistant**
