# Éclairage Zigbee fiable — Binding, Scènes et Détection de mouvement

> Vidéo associée : [Rendre son éclairage Zigbee FIABLE 💡 Binding Zigbee, Scènes et Mouvements dans Home Assistant !](https://youtu.be/t9jAJRpR4i8)
> Branche : `video/eclairage-zigbee-binding`

Deux blueprints Home Assistant présentés dans la vidéo pour rendre son éclairage Zigbee à la fois fiable et intelligent.

---

## Fichiers fournis

| Fichier | Description |
|---|---|
| `blueprint_scene_cycler.yaml` | Cycle entre plusieurs scènes sur appuis multiples (2e appui = scène 1, 3e appui = scène 2...) |
| `blueprint_auto_extinction.yaml` | Allumage automatique sur mouvement + luminosité, extinction progressive, modes jour/nuit, override manuel |

---

## Prérequis

- **Home Assistant** 2024.1 ou supérieur
- **Zigbee2MQTT** avec le binding Zigbee configuré sur vos interrupteurs (voir vidéo)
- Un capteur de mouvement (`binary_sensor` de classe `motion`)
- Un capteur de luminosité (`sensor` de classe `illuminance`)
- Un `input_boolean` pour le mode nuit (ex: votre mode dodo)

---

## Blueprint 1 : Appuis multiples en boucle (Scènes)

**Fichier :** `blueprint_scene_cycler.yaml`

Ce blueprint fonctionne en complément du binding Zigbee :
- **1 appui** : allumage classique, géré directement par le binding Zigbee (sans Home Assistant)
- **2 appuis** : active la 1ère scène configurée
- **3 appuis** : active la 2ème scène
- **N appuis** : cycle en boucle

### Installation

1. Copiez `blueprint_scene_cycler.yaml` dans `config/blueprints/automation/GuiPoM/`
2. Dans Home Assistant : **Paramètres > Automatisations > Blueprints**
3. Créez une nouvelle automatisation depuis ce blueprint
4. Configurez le capteur d'action de votre interrupteur et les scènes souhaitées

### Paramètres

| Paramètre | Description |
|---|---|
| `button_sensor` | Entité sensor émettant les actions (ex: `sensor.dimmer_cuisine_action`) |
| `button_state` | Valeur déclencheur (ex: `on_press`, `press`, `single`) |
| `scenes` | Liste des scènes à cycler dans l'ordre |

---

## Blueprint 2 : Extinction automatique sur mouvement

**Fichier :** `blueprint_auto_extinction.yaml`

Automatisation complète d'allumage/extinction sur détection de mouvement et luminosité.

### Comportement

- S'active si la pièce est sombre (en dessous du seuil lux) et qu'un mouvement est détecté
- Allume la scène jour ou nuit selon le mode (`input_boolean`)
- Après la fin du mouvement, attend le délai silencieux puis éteint progressivement
- L'extinction progressive sert d'**avertissement visuel** (+ effet Zigbee optionnel)
- Un appui manuel sur le dimmer (`on_press` ou `off_press`) **interrompt l'automation** (mode override)
- Mode `restart` : chaque nouveau mouvement repart d'une instance fraîche

### Installation

1. Copiez `blueprint_auto_extinction.yaml` dans `config/blueprints/automation/GuiPoM/`
2. Dans Home Assistant : **Paramètres > Automatisations > Blueprints**
3. Créez une nouvelle automatisation depuis ce blueprint

### Paramètres principaux

| Paramètre | Description | Défaut |
|---|---|---|
| `motion_sensor` | Capteur de mouvement | — |
| `light_target` | Lumière à piloter | — |
| `dimmer_action_sensor` | Entité action du dimmer | — |
| `lux_sensor` | Capteur de luminosité | — |
| `lux_threshold` | Seuil en lux en dessous duquel allumer | 15 lx |
| `night_boolean` | `input_boolean` mode nuit | — |
| `scene_day` | Scène à activer le jour (optionnel) | — |
| `scene_night` | Scène à activer la nuit (optionnel) | — |
| `inhibition_delay` | Délai d'inhibition après extinction manuelle | 15 s |
| `silence_delay` | Délai sans mouvement avant extinction | 30 s |
| `extinction_transition` | Durée de l'extinction progressive | 15 s |
| `warning_effect` | Effet Zigbee d'avertissement (optionnel) | — |

---

## Ressources

- [Documentation Zigbee2MQTT — Binding](https://www.zigbee2mqtt.io/guide/usage/binding.html)
- [Dépôt home-assistant-snippets](https://github.com/GuiPoM/home-assistant-snippets)
