# Home Automation System

Vollständige Home-Assistant-Konfiguration für ein modernes Smart Home mit:

- 🔔 **Reolink Türklingel** → weckt Android-Wandtablet automatisch auf
- 📷 **Reolink Überwachungskameras** (innen & außen)
- ☀️ **SMA Solar Wechselrichter + Sunny Home Manager 2.0**
- 📱 **Android-Wandtablet** (Fully Kiosk Browser)
- 📣 **Push-Benachrichtigungen** auf alle Mobilgeräte
- 🏠 **Erweiterbar** auf Zigbee, Z-Wave, Heizung, Licht, Rollladen u.v.m.

---

## Inhaltsverzeichnis

1. [Systemübersicht](#systemübersicht)
2. [Benötigte Hardware](#benötigte-hardware)
3. [Softwarestack](#softwarestack)
4. [Schnellstart](#schnellstart)
5. [Konfigurationsstruktur](#konfigurationsstruktur)
6. [Integrationen im Detail](#integrationen-im-detail)
7. [Erweiterungsmöglichkeiten](#erweiterungsmöglichkeiten)

---

## Systemübersicht

```
Internet
    │
    ▼
 Router/Firewall
    │
    ├── Home Assistant Server (Mini-PC / Raspberry Pi 5)
    │       ├── Reolink Integration (Türklingel + Kameras via LAN)
    │       ├── SMA Solar Integration (WebConnect / Modbus)
    │       ├── MQTT Broker (Mosquitto)
    │       └── Fully Kiosk Browser API (Tablet-Steuerung)
    │
    ├── Reolink Türklingel (PoE oder WLAN)
    ├── Reolink Kameras (PoE)
    ├── SMA Wechselrichter (LAN)
    ├── SMA Sunny Home Manager 2.0 (LAN)
    └── Android-Tablet an der Wand (WLAN)
```

---

## Benötigte Hardware

### Pflicht

| Gerät | Empfehlung | Zweck |
|-------|-----------|-------|
| **Home Assistant Server** | [Beelink Mini S12 Pro](https://www.bee-link.com) (Intel N100, 16 GB RAM, 500 GB SSD) oder Raspberry Pi 5 (8 GB) | Zentrale Schaltzentrale |
| **Netzwerk-Switch (PoE)** | TP-Link TL-SG108PE (8-Port PoE+) | Strom + Daten für Kameras |
| **Reolink Türklingel** | [Reolink Video Doorbell PoE](https://reolink.com/de/product/reolink-video-doorbell/) | Video-Türklingel |
| **Reolink Kameras** | [Reolink RLC-810A](https://reolink.com/de/product/rlc-810a/) (PoE, 4K) | Außenkameras |
| **Android-Tablet** | Samsung Galaxy Tab A9+ oder Lenovo Tab M10 Plus | Wandpanel / Türklingel-Anzeige |
| **Tablet-Halterung** | Hecklerdesign [U] Wall Mount | Wandmontage |
| **SMA Wechselrichter** | vorhandener SMA Sunny Boy / Tripower | Solaranlage |
| **SMA Sunny Home Manager 2.0** | SHM 2.0 | Energiemanagement |
| **USB Zigbee-Stick** | SONOFF Zigbee 3.0 USB Dongle Plus | Für spätere Zigbee-Geräte |

### Optional / Empfohlen

| Gerät | Zweck |
|-------|-------|
| USV (APC BE600M2) | Notstrom für HA-Server |
| Unifi Access Point | Stabiles WLAN für Tablet + WLAN-Geräte |
| Shelly 1PM / 2.5 | Rollladensteuerung, Lichtschalter |
| Philips Hue Bridge | Smart Lighting |
| Netatmo Thermostat | Heizungssteuerung |

---

## Softwarestack

| Software | Funktion |
|----------|----------|
| **Home Assistant OS** | Zentrale Smart-Home-Plattform |
| **Mosquitto MQTT** | Nachrichtenbus (Add-on) |
| **Fully Kiosk Browser** | Tablet-Kiosk-App (Android) |
| **HA Companion App** | Push-Nachrichten auf Smartphones |
| **Samba Share** | Einfacher Dateizugriff auf HA-Konfig (Add-on) |
| **VS Code Server** | Konfig im Browser bearbeiten (Add-on) |

---

## Schnellstart

### 1. Home Assistant installieren

```bash
# Option A: Docker Compose (empfohlen für Mini-PC)
git clone https://github.com/gupa1012/Home_automation.git
cd Home_automation
cp .env.example .env          # Umgebungsvariablen anpassen
docker compose up -d

# Option B: Home Assistant OS direkt auf dem Gerät
# https://www.home-assistant.io/installation/
```

### 2. Konfiguration einrichten

```bash
# Konfig-Verzeichnis in den HA-Container/Datenordner kopieren
cp -r homeassistant/* /pfad/zu/deinem/ha-config/
```

### 3. Geräte einbinden

1. Home Assistant öffnen: `http://<server-ip>:8123`
2. **Einstellungen → Integrationen** → „Reolink" suchen und konfigurieren
3. **Einstellungen → Integrationen** → „SMA Solar" konfigurieren
4. Tablet: Fully Kiosk Browser installieren, Remote Admin aktivieren
5. Secrets in `homeassistant/secrets.yaml` eintragen (siehe `secrets.yaml.example`)

---

## Konfigurationsstruktur

```
homeassistant/
├── configuration.yaml          # Haupt-Konfiguration
├── automations.yaml            # Alle Automationen
├── scripts.yaml                # Wiederverwendbare Skripte
├── secrets.yaml.example        # Vorlage für Zugangsdaten
├── packages/
│   ├── reolink.yaml            # Reolink Kameras & Türklingel
│   ├── sma_solar.yaml          # SMA Wechselrichter & Sunny Home Manager
│   ├── tablet_panel.yaml       # Android-Wandtablet (Fully Kiosk Browser)
│   └── notifications.yaml      # Benachrichtigungsregeln
└── lovelace/
    ├── main_dashboard.yaml     # Haupt-Dashboard
    ├── doorbell_dashboard.yaml # Türklingel-Ansicht
    └── solar_dashboard.yaml    # Solar-Übersicht
docker-compose.yml              # Container-Stack
docs/
├── hardware_list.md            # Ausführliche Hardware-Liste
└── setup_guide.md              # Schritt-für-Schritt-Anleitung
```

---

## Integrationen im Detail

### Reolink Türklingel + Kameras

- Offizielle HA-Integration: [Reolink](https://www.home-assistant.io/integrations/reolink/)
- Liefert: Kamera-Stream, Bewegungserkennung, Klingelauslöser
- Klingeln → Automation weckt Tablet, zeigt Live-Bild, sendet Push-Notification

### Android-Wandtablet

- **Fully Kiosk Browser** läuft im Kiosk-Modus
- HA kann den Bildschirm per REST-API ein-/ausschalten
- Bei Klingeln: Bildschirm an, automatisch zur Türklingel-Ansicht navigieren
- Lovelace-Dashboard mit Kamera-Feed + Türöffner-Button

### SMA Solar

- **SMA WebConnect / Modbus TCP**: HA liest Echtzeit-Daten
- Liefert: Erzeugung (W), Einspeisung, Bezug, Tagesertrag (kWh)
- **Sunny Home Manager**: optimiert Verbrauch, HA kann Schaltbefehle senden
- Automation: Bei Solarüberschuss → Waschmaschine/Spülmaschine starten

---

## Erweiterungsmöglichkeiten

| Integration | Zweck |
|-------------|-------|
| **Zigbee2MQTT** | Zigbee-Geräte (Lampen, Schalter, Sensoren) |
| **Z-Wave JS** | Z-Wave-Geräte |
| **Shelly** | WLAN-Relais für Rollläden, Steckdosen |
| **Netatmo** | Wettermessung, Heizung |
| **Spotify / Apple Music** | Musiksteuerung |
| **Google / Alexa** | Sprachsteuerung |
| **Telegram Bot** | Nachrichten & Kamerabilder per Telegram |
| **Node-RED** | Komplexe Flows visuell erstellen |
| **InfluxDB + Grafana** | Langzeitdiagramme Energie/Temperatur |
| **Frigate NVR** | KI-gestützte Personenerkennung in Kameras |
