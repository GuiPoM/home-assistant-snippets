# 🌊 IKEA Klippbok + Home Assistant

**Sécurisez votre logement contre les inondations !**

📺 [Regarder la vidéo](https://youtu.be/vnOwSnuNEPI)

## 📌 À propos

Ce tutoriel vous montre comment :
- Intégrer le détecteur de fuite IKEA Klippbok via Matter/Thread
- Construire une logique d'automatisation complète de gestion d'inondation
- Gérer les sirènes et alertes
- Créer des scripts d'alerte générale
- Monitorer votre sécurité domotique

## 📦 Contenu du Repository

### Blueprints
- **securite_alarme_declenchement.yaml** - Déclenche l'alarme sur détection capteur
- **securite_alarme_actions.yaml** - Actions après déclenchement (notifications + script d'alerte)
- **securite_alarme_rappel.yaml** - Rappels périodiques si non-réarmé

### Scripts
- **notification_smartphone.yaml** - Gestion des notifications selon sévérité
- **alerte_generale.yaml** - Script d'alerte générique (à personnaliser)
- **alerte_inondation.yaml** - Script spécifique pour alerte inondation

### Templates
- **macros.jinja** - Macros Jinja pour formater les informations de capteurs

## 🚀 Installation Rapide

### 1. Structure des fichiers

Placez les fichiers dans votre installation Home Assistant :

```
/homeassistant/
├── blueprints/
│   └── automation/
│       ├── securite_alarme_declenchement.yaml
│       ├── securite_alarme_actions.yaml
│       └── securite_alarme_rappel.yaml
├── scripts/
│   ├── notification_smartphone.yaml
│   ├── alerte_generale.yaml
│   └── alerte_inondation.yaml
└── custom_templates/
    └── macros.jinja
```

### 2. Configuration YAML

Dans votre `configuration.yaml`, assurez-vous que les blueprints et scripts sont chargés :

```yaml
# Configuration basique (automatique par défaut)
automation: !include automations.yaml
script: !include scripts.yaml

# Si besoin, charger explicitement les custom_templates
homeassistant:
  custom_templates: /homeassistant/custom_templates
```

### 3. Dashboard - Sécurité Inondation

Créez une vue dédiée à la sécurité inondation dans votre dashboard. Exemple de structure minimale :

```yaml
views:
  - type: sections
    max_columns: 4
    title: Sécurité Inondation
    path: securite-inondation
    icon: mdi:water-alert
    sections:
      - type: grid
        cards:
          - type: tile
            grid_options:
              columns: full
              rows: 2
            entity: alarm_control_panel.securite_inondation
            name: Inondation
            icon: mdi:water
            color: blue
            vertical: true
            tap_action:
              action: call-service
              service: alarm_control_panel.alarm_disarm
              target:
                entity_id: alarm_control_panel.securite_inondation
            features_position: bottom
        column_span: 2
      - type: grid
        cards:
          - type: heading
            heading: Capteurs d'Inondation
            heading_style: title
            icon: mdi:water-alert
          - type: vertical-stack
            cards:
              - type: heading
                heading: Sous-sol
                heading_style: title
              - type: grid
                columns: 3
                square: false
                cards:
                  - type: tile
                    entity: binary_sensor.klippbok_water_leak_sensor
                    name: Garage
                    vertical: false
                    features_position: bottom
                  - type: tile
                    entity: binary_sensor.water_sensor_cave_1
                    name: Cave (S1)
                  - type: tile
                    entity: binary_sensor.water_sensor_cave_2
                    name: Cave (S2)
              - type: heading
                heading: Rez-de-chaussée
                heading_style: title
              - type: grid
                columns: 3
                square: false
                cards:
                  - type: tile
                    entity: binary_sensor.flood_sensor_kitchen
                    name: Cuisine
                    vertical: false
              - type: heading
                heading: Étage
              - type: grid
                columns: 3
                square: false
                cards:
                  - type: tile
                    entity: binary_sensor.flood_sensor_bathroom
                    name: Salle de bain
                    vertical: false
              - type: heading
                heading: Maintenance
              - type: grid
                columns: 3
                square: false
                cards:
                  - type: tile
                    entity: input_boolean.test_inondation
                    name: Test
                    icon: mdi:water-alert
                    color: blue
                    vertical: false
                    features_position: bottom
        column_span: 2
    cards: []
    subview: true
```

**À personnaliser :**
- Remplacez `securite-inondation` par le chemin de votre dashboard
- Remplacez les entity IDs (`binary_sensor.klippbok_*`, etc.) par vos vrais capteurs
- Remplacez `alarm_control_panel.securite_inondation` par votre panel d'alarme

### 4. Créer les automatisations

Dans Home Assistant UI :
1. **Paramètres → Automations**
2. **Créer une automation**
3. **Créer à partir d'une blueprint**
4. Sélectionnez les blueprints fournis
5. Configurez vos entités (capteurs, panel d'alarme, scripts)

### 5. Tester

```yaml
# Appel manuel du script dans Developer Tools → Services
service: script.alerte_inondation
```

## ⚙️ Configuration Typique

### Entités à avoir/créer

**Panel d'alarme inondation :**
```yaml
alarm_control_panel.securite_inondation
# ou via intégration Alarmo HACS
```

**Capteurs d'inondation (à adapter) :**
```yaml
binary_sensor.klippbok_water_leak_sensor      # IKEA Klippbok
binary_sensor.water_sensor_cave_1             # Autres capteurs
binary_sensor.water_sensor_cave_2
binary_sensor.flood_sensor_kitchen
binary_sensor.flood_sensor_bathroom
```

**Helper pour présence (optionnel) :**
```yaml
input_boolean.present
# À créer dans Paramètres → Appareils et services → Helpers
```

**Notifications :**
```yaml
notify.notify
# Service standard Home Assistant - À adapter avec votre service (mobile_app, email, etc.)
```

**Dashboard URL :**
```yaml
# À personnaliser avec votre chemin dashboard
click_url: /dashboard-maison/securite-inondation
```

### Points À Personnaliser

1. **Chemins du dashboard** - Les URLs comme `/dashboard-maison/securite-inondation` doivent correspondre à vos vues
2. **Entity IDs des capteurs** - Remplacer par vos vrais capteurs d'inondation
3. **Entity ID du panel d'alarme** - Doit correspondre à votre intégration (Alarmo, native, etc.)
4. **Service de notification** - Adapter `script.notification_smartphone` à vos besoins (Apple, Android, Email, etc.)
5. **Délai de détection** - Le paramètre `delay` du blueprint déclenchement est configurable en secondes

## 💡 Comment ça marche

### Flux d'automatisation

```
Capteur détecte inondation
        ↓
Blueprint "Déclenchement" vérifie délai
        ↓
Panel d'alarme → triggered
        ↓
Blueprint "Actions" déclenche en parallèle :
  - notification_smartphone
  - alerte_inondation (→ alerte_generale)
        ↓
Blueprint "Rappel" toutes les 15 min si pas réarmé
```

### Scripts d'alerte

```
alerte_inondation.yaml
        ↓
appelle alerte_generale.yaml avec:
  - color_name: bleu
  - sirene_tone: "3"
  - duration: 300s
  - check_presence: true
```

## 🔧 Personnalisation

### Modifier la macro `format_sensors`

Éditer `custom_templates/macros.jinja` pour changer le format de l'affichage des capteurs.

### Modifier le script `alerte_generale`

Le script `alerte_generale.yaml` est fourni en version basique. Vous devez l'adapter à votre setup :

- Ajouter vos vrais scripts `script.gyrophare`
- Ajouter vos vrais scripts `script.sirene`
- Ajouter vos lumières de sécurité
- Configurer les helpers (input_boolean.present, etc.)

### Modifier les notifications

Le script `notification_smartphone.yaml` utilise le service générique `notify.notify`. Adaptez-le à votre setup (Apple, Android, Email, etc.).

## 🔗 Ressources

- [Home Assistant](https://www.home-assistant.io/)
- [IKEA Klippbok](https://www.ikea.com/fr/fr/p/klippbok-detecteur-de-fuites-deau-connecte-30604193/)
- [Alarmo HACS](https://github.com/nielsfaber/alarmo)
- [Matter dans Home Assistant](https://www.home-assistant.io/integrations/matter/)
- [Thread dans Home Assistant](https://www.home-assistant.io/integrations/thread/)

## 💬 Support

En cas de questions :
- Consultez la vidéo pour le contexte complet
- Vérifiez les logs Home Assistant (`Settings → System → Logs`)
- Testez chaque composant indépendamment

---

**Note :** Ce contenu est fourni à titre informatif. Adaptez-le à votre configuration personnelle pour une sécurité optimale.
