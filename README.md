# GLaDOS dans Home Assistant — Annonces vocales sarcastiques

> Vidéo associée : [Et si votre maison connectée arrêtait d'être polie ?](https://youtu.be/qeDvR1QU5XM)
> Branche : `video/glados-annonces-vocales`

Intégrez la voix de GLaDOS (saga Portal) dans Home Assistant pour des annonces vocales sarcastiques et faussement polies. Ce dépôt fournit deux scripts réutilisables et un exemple d'automatisation.

---

## Fichiers fournis

| Fichier | Description |
|---|---|
| `annonce_vocale_technique.yaml` | Script bas niveau : gestion présence, volume, TTS, snapshot/restauration |
| `annonce_vocale.yaml` | Script point d'entrée : sélection du mode vocal, dictionnaires de phrases, intégration Gemini |
| `exemple_automatisation_lumiere.yaml` | Exemple : annonce vocale si une lumière reste allumée trop longtemps |

---

## Prérequis

- **Home Assistant** 2024.1 ou supérieur
- **Intégration Piper** ([documentation](https://www.home-assistant.io/integrations/piper/)) — moteur TTS local
- **Modèle de voix GLaDOS VF** — projet Piper par Tazerman ([GitHub](https://github.com/Tazerman/piper-voice-glados-fr))
- **Intégration Google Generative AI** ([documentation](https://www.home-assistant.io/integrations/google_generative_ai/)) — uniquement pour le mode `glados_ia`

---

## Architecture

```
Automatisation
     │
     ▼
script.annonce_vocale          ← point d'entrée unique pour toutes tes automations
     │
     ├─ Lit input_select.YOUR_VOCAL_MODE_SELECT
     ├─ Construit la phrase (dictionnaire local ou Gemini)
     │
     ▼
script.annonce_vocale_technique  ← couche technique
     │
     ├─ Vérifie présence (input_boolean.YOUR_PRESENCE_BOOLEAN)
     ├─ Vérifie mode dodo (input_boolean.YOUR_SLEEP_MODE_BOOLEAN)
     ├─ Résout l'enceinte cible (capteurs volumétriques ou paramètre)
     ├─ Snapshot du media player
     ├─ Règle le volume
     ├─ Appelle tts.speak
     ├─ Attend (durée dynamique selon longueur du message)
     └─ Restaure la scène
```

---

## Helpers Home Assistant à créer

Crée ces helpers dans **Paramètres → Appareils et services → Helpers**.

### `input_boolean.YOUR_PRESENCE_BOOLEAN`
Indique si quelqu'un est présent dans la maison. Lié à ton système d'alarme ou de présence.

### `input_boolean.YOUR_SLEEP_MODE_BOOLEAN`
Mode nuit/dodo. Quand activé, les annonces vocales sont suspendues (sauf `force_announcement: true`).

### `input_select.YOUR_VOCAL_MODE_SELECT`
Sélecteur du mode vocal avec les options suivantes :

| Option | Valeur | Description |
|---|---|---|
| Désactivé | `off` | Aucune annonce vocale |
| Classique | `classique` | Google TTS, ton neutre |
| GLaDOS | `glados` | Piper TTS, phrases sarcastiques locales |
| GLaDOS IA | `glados_ia` | Piper TTS + réécriture Gemini à la volée |

---

## Installation du modèle de voix GLaDOS

1. Télécharge les fichiers `.onnx` et `.onnx.json` depuis le [projet Tazerman](https://github.com/Tazerman/piper-voice-glados-fr)
2. Copie-les dans `/share/piper/` sur ton serveur Home Assistant (via Samba ou SSH)
3. **Étape critique** : ouvre le fichier `.onnx.json` et vérifie que la clé `"dataset"` est bien présente. Si elle est absente, ajoute-la manuellement (voir la vidéo, 12:39)
4. Dans Home Assistant, va dans **Paramètres → Assistants vocaux** et ajoute la voix GLaDOS dans la configuration Piper
5. Vérifie le nom de la voix : dans `annonce_vocale_technique.yaml`, la valeur `fr_FR-glados-medium` doit correspondre au nom de ton fichier `.onnx`

---

## Placeholders à remplacer

Recherche et remplace ces valeurs dans les fichiers YAML avant import dans Home Assistant.

| Placeholder | Description |
|---|---|
| `media_player.YOUR_SPEAKER_1` | Première enceinte (ex. bureau) |
| `media_player.YOUR_SPEAKER_2` | Deuxième enceinte (ex. couloir, salon) |
| `media_player.YOUR_SPEAKER_GROUP` | Groupe d'enceintes pour diffusion multi-room |
| `input_boolean.YOUR_PRESENCE_BOOLEAN` | Helper booléen de présence |
| `input_boolean.YOUR_SLEEP_MODE_BOOLEAN` | Helper booléen mode nuit |
| `input_select.YOUR_VOCAL_MODE_SELECT` | Helper sélecteur de mode vocal |
| `binary_sensor.YOUR_MOTION_SENSOR_1` | Capteur volumétrique zone 1 (pour routing automatique) |
| `binary_sensor.YOUR_MOTION_SENSOR_2` | Capteur volumétrique zone 2 (pour routing automatique) |
| `conversation.YOUR_AI_AGENT` | Agent de conversation Gemini (mode `glados_ia` uniquement) |
| `light.YOUR_LIGHT_ENTITY_*` | Entités lumières dans l'exemple d'automatisation |

---

## Exemples d'utilisation

### Appel minimal (depuis une automatisation)

```yaml
action: script.annonce_vocale
data:
  key: volets_coucher
```

### Appel avec variables

```yaml
action: script.annonce_vocale
data:
  key: verrou_ouvert
  vars:
    nom: "Porte d'entrée"
    duree: "5"
```

### Appel avec enceinte et volume spécifiques

```yaml
action: script.annonce_vocale
data:
  key: accueil_retour
  target_speakers:
    - media_player.YOUR_SPEAKER_1
  volume_level: 0.6
```

### Appel forcé (ignore présence et mode dodo)

```yaml
action: script.annonce_vocale
data:
  key: mode_dodo
  force_announcement: true
```

### Clés disponibles et variables associées

| Clé | Variables | Description |
|---|---|---|
| `verrou_ouvert` | `nom`, `duree` | Verrou resté déverrouillé |
| `porte_ouverte` | `nom`, `duree` | Porte restée ouverte |
| `lumiere_allumee` | `nom`, `piece`, `duree` | Lumière restée allumée |
| `volets_coucher` | _(aucune)_ | Rappel fermeture des volets |
| `detecteur_toggle` | `nom`, `etat` | Activation/désactivation d'un détecteur |
| `nas_changelog` | `system_count`, `docker_count` | Mises à jour NAS disponibles |
| `mode_dodo` | _(aucune)_ | Activation du mode nuit |
| `accueil_retour` | _(aucune)_ | Retour à la maison |
| `accueil_lever` | _(aucune)_ | Début de journée |

---

## Liens utiles

- [Projet voix GLaDOS Piper VF — Tazerman](https://github.com/Tazerman/piper-voice-glados-fr)
- [Intégration Piper — Home Assistant](https://www.home-assistant.io/integrations/piper/)
- [Intégration Google Generative AI (Gemini) — Home Assistant](https://www.home-assistant.io/integrations/google_generative_ai/)
- [Site officiel Home Assistant](https://www.home-assistant.io/)

---

## Licence

MIT — Usage personnel uniquement. Voir [LICENSE](https://github.com/GuiPoM/home-assistant-snippets/blob/master/LICENSE).

> La voix GLaDOS est une création originale de Valve et Ellen McLain (VO) / Scarlett Perreux (VF).
> Le modèle Piper utilisé ici est un clone vocal destiné à un usage personnel et non commercial uniquement.
> Respecte les droits d'auteur et les conditions d'utilisation associées.
