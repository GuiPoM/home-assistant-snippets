# 📱 Notifications Smartphone + Home Assistant

**Un script central pour toutes vos notifications — push, mail, SMS, Do Not Disturb.**

▶️ [Regarder la vidéo](https://youtu.be/zj1U9-bSfXE)

## 🎯 A propos

Ce tutoriel vous montre comment :
- Eviter le hardcoding de `notify.mobile_app_xxx` dans chaque automatisation
- Centraliser toute la logique de notification dans un seul script
- Gérer 5 niveaux de sévérité (du push discret au SMS d'urgence)
- Bypasser le mode Ne Pas Déranger Android avec `alarm_stream`
- Piloter le DND de votre téléphone depuis Home Assistant
- Ajouter des boutons d'action interactifs dans vos notifications

## 📦 Contenu du Repository

### Scripts
- **notification_smartphone.yaml** — Script central, 5 niveaux de sévérité, push/mail/SMS, image, tag, actions
- **mobile_controle_dnd.yaml** — Commande système pour activer/désactiver le Do Not Disturb

## 🔢 Les 5 niveaux de sévérité

| Niveau | Canaux | Canal Android | Bypass DND |
|--------|--------|---------------|------------|
| `normal` | Push seul | general | Non |
| `important_push` | Push seul | general | Non |
| `important` | Push + Mail | general | Non |
| `critical_push` | Push seul | alarm_stream | Oui |
| `critical` | Push + Mail + SMS | alarm_stream | Oui |

## 📂 Installation

### 1. Structure des fichiers

Placez les scripts dans votre installation Home Assistant :

```
/homeassistant/
└── scripts/
    ├── notification_smartphone.yaml
    └── mobile_controle_dnd.yaml
```

### 2. Chargement dans configuration.yaml

```yaml
script: !include_dir_merge_named scripts/
```

Ou si vous gérez vos scripts dans un fichier unique :

```yaml
script: !include scripts.yaml
```

Dans ce cas, copiez le contenu des fichiers yaml dans votre `scripts.yaml` en retirant la clé `alias` de premier niveau.

### 3. Points à personnaliser

Recherchez et remplacez les placeholders suivants dans les fichiers :

| Placeholder | Remplacer par | Exemple |
|-------------|---------------|---------|
| `YOUR_MOBILE_APP_DEVICE` | Votre service notify mobile | `notify.mobile_app_sm_s916b` |
| `YOUR_NOTIFY_MAIL` | Votre service notify mail | `notify.smtp_proton` |
| `YOUR_NOTIFY_SMS` | Votre service notify SMS | `notify.free_mobile` |

> Pour trouver le nom de votre service mobile : **Paramètres → Appareils et services → Application mobile** — le nom du service est affiché sous l'appareil.

### 4. Réglages Android requis (Samsung)

Pour que `alarm_stream` fonctionne correctement :

1. Appui long sur l'icône Home Assistant → **Infos d'application → Notifications**
2. Vérifiez que les notifications sont **autorisées**
3. Dans **Catégories**, configurez le canal `alarm_stream` avec le son d'alarme souhaité
4. Dans **Paramètres système → Notifications → Ne pas déranger**, vérifiez que Home Assistant apparaît dans la liste des applications autorisées à piloter le DND
5. **Ne mettez pas** Home Assistant dans "Notifications d'application autorisées" du DND — cela bypaseerait toutes les notifs sans distinction

## 🔧 Utilisation

### Appel minimal

```yaml
action: script.notification_smartphone
data:
  title: "Mon alerte"
  message: "Ce qu'il s'est passé"
  severity: normal
```

### Appel complet avec toutes les options

```yaml
action: script.notification_smartphone
data:
  title: "Mouvement détecté"
  message: "Un mouvement a été détecté devant le garage"
  severity: critical_push
  click_url: /dashboard-maison/securite
  tag: mouvement_garage
  image_url: /local/snapshots/garage.jpg
  actions:
    - action: TRIGGER_SIREN_GARAGE
      title: Déclencher la sirène
    - action: STOP_SIREN_GARAGE
      title: Arrêter la sirène
```

### Sévérité dynamique (Jinja2)

```yaml
action: script.notification_smartphone
data:
  severity: >
    {% if trigger.to_state.state == 'unavailable' %}
      critical_push
    {% else %}
      normal
    {% endif %}
  title: "Onduleur"
  message: "Statut : {{ trigger.to_state.state }}"
```

### Contrôle du DND (mode dodo)

```yaml
# Activer le DND (aller se coucher)
action: script.mobile_controle_dnd
data:
  status: priority_only

# Désactiver le DND (réveil)
action: script.mobile_controle_dnd
data:
  status: "off"
```

### Gérer les actions depuis une automatisation

```yaml
trigger:
  - platform: event
    event_type: mobile_app_notification_action
    event_data:
      action: TRIGGER_SIREN_GARAGE
action:
  - action: siren.turn_on
    target:
      entity_id: siren.garage
```

## ⚠️ Notes techniques

### alarm_stream + actions
Quand des boutons d'action sont définis **et** que la sévérité est `critical_push` ou `critical`, le payload est construit différemment (via `push_data`). C'est une contrainte Android/HA Companion — les deux chemins sont gérés automatiquement par le script.

### TTL et priority
- `ttl: 0` + `priority: high` → livraison immédiate même téléphone en veille
- Ces paramètres sont précalculés automatiquement selon la sévérité

### Samsung vs Android stock
Les téléphones Samsung (OneUI) ne sont pas en AOSP. La configuration du bypass DND est plus complexe qu'un Android standard — voir la vidéo pour le détail des réglages.

## 📚 Ressources

- [Notifications de base — Companion App](https://companion.home-assistant.io/docs/notifications/notifications-basic)
- [Notifications critiques (TTL, priority, alarm_stream)](https://companion.home-assistant.io/docs/notifications/critical-notifications)
- [Notifications avec actions boutons](https://companion.home-assistant.io/docs/notifications/actionable-notifications)
- [Commandes système dont command_dnd](https://companion.home-assistant.io/docs/notifications/notification-commands)
- [Intégration application mobile](https://www.home-assistant.io/integrations/mobile_app)
- [Intégration Free Mobile SMS](https://www.home-assistant.io/integrations/free_mobile)

## 💬 Support

En cas de questions :
- Consultez la vidéo pour le contexte complet
- Vérifiez les logs Home Assistant (**Paramètres → Système → Journaux**)
- Testez chaque niveau indépendamment via **Outils de développement → Actions**

---

**Note :** Ce code est fourni à titre d'exemple. Adaptez-le à votre configuration personnelle — appareils, services mail/SMS, dashboards.
