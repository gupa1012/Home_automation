# Setup-Anleitung: Home Automation System

## Schritt-für-Schritt-Einrichtung

---

## Schritt 1: Netzwerk vorbereiten

1. **Feste IP-Adressen vergeben** (im Router per DHCP-Reservierung):

   | Gerät | IP-Adresse |
   |-------|-----------|
   | HA-Server | 192.168.1.10 |
   | Reolink Türklingel | 192.168.1.50 |
   | Reolink Kamera Einfahrt | 192.168.1.51 |
   | Reolink Kamera Garten | 192.168.1.52 |
   | SMA Wechselrichter | 192.168.1.60 |
   | SMA Sunny Home Manager | 192.168.1.61 |
   | Android Tablet | 192.168.1.100 |

2. **PoE-Switch** an Router anschließen, Kameras und Türklingel per Netzwerkkabel verbinden.

3. **Reolink-Geräte** einrichten: Reolink App → Gerät hinzufügen → IP-Adresse aus obiger Tabelle setzen.

---

## Schritt 2: Home Assistant installieren

### Option A: Docker Compose (empfohlen für Mini-PC)

```bash
# Repository klonen
git clone https://github.com/gupa1012/Home_automation.git
cd Home_automation

# Umgebungsvariablen konfigurieren
cp .env.example .env
nano .env   # TZ und HA_CONFIG_PATH anpassen

# Stack starten
docker compose up -d

# Logs prüfen
docker compose logs -f homeassistant
```

### Option B: Home Assistant OS (empfohlen für Raspberry Pi)

1. [Home Assistant OS Image](https://www.home-assistant.io/installation/raspberrypi) herunterladen
2. Mit [Balena Etcher](https://www.balena.io/etcher/) auf SD-Karte oder USB schreiben
3. Pi starten, warten bis HA bootet (~5 Minuten)
4. Im Browser öffnen: `http://homeassistant.local:8123`

### Option C: Home Assistant OS auf Mini-PC (Beelink etc.)

1. [HA OS Generic x86-64 Image](https://www.home-assistant.io/installation/generic-x86-64) herunterladen
2. Mit [Rufus](https://rufus.ie) / Ventoy auf USB-Stick schreiben, booten
3. Im Browser: `http://<server-ip>:8123`

---

## Schritt 3: Konfiguration kopieren

```bash
# Auf dem HA-Server (falls Docker):
cp -r homeassistant/* /pfad/zu/ha-config/

# Secrets anpassen
cp homeassistant/secrets.yaml.example homeassistant/secrets.yaml
nano homeassistant/secrets.yaml
# → Alle Felder mit echten Werten befüllen
```

> Bei **Home Assistant OS**: Samba Share Add-on installieren → Netzlaufwerk verbinden → Dateien kopieren.

---

## Schritt 4: MQTT-Broker einrichten

### Als HA Add-on (einfachste Option):

1. HA → **Einstellungen → Add-ons → Mosquitto broker** installieren
2. Starten und im Konfigurationsreiter Benutzer anlegen
3. HA → **Einstellungen → Integrationen → MQTT** – automatisch erkannt

### Mit Docker Compose (bereits enthalten):

```bash
# Mosquitto-Passwortdatei erstellen
docker run --rm -it eclipse-mosquitto:2 mosquitto_passwd -c /mosquitto/config/passwd ha_mqtt
# Passwort eingeben und merken → in secrets.yaml eintragen
```

---

## Schritt 5: Reolink Integration einrichten

1. HA → **Einstellungen → Integrationen → + Integration hinzufügen**
2. „**Reolink**" suchen → klicken
3. **Türklingel konfigurieren:**
   - Host: `192.168.1.50`
   - Benutzername: `admin`
   - Passwort: Reolink-Passwort
4. **Kameras** jeweils einzeln hinzufügen (192.168.1.51, .52 usw.)
5. Entities prüfen: `camera.reolink_*`, `binary_sensor.reolink_*`

---

## Schritt 6: SMA Solar einrichten

### SMA Wechselrichter (WebConnect):

1. Am Wechselrichter: Benutzeroberfläche öffnen → Netzwerk aktivieren → LAN-Kabel anschließen
2. HA → **Einstellungen → Integrationen → SMA Solar** hinzufügen
   - Host: `192.168.1.60`
   - Passwort: SMA WebConnect Passwort (Standard: `0000` oder auf Gerät aufgedruckt)
3. Entities prüfen: `sensor.sma_power_ac`, `sensor.sma_total_yield` usw.

### SMA Sunny Home Manager 2.0:

1. SHM 2.0 über Sunny Portal konfigurieren und ins lokale Netz einbinden
2. HA → **Einstellungen → Integrationen → SMA Sunny Home Manager 2.0** hinzufügen
   - Host: `192.168.1.61`
3. Entities für Gesamtverbrauch, Einspeisung, Netzbezug prüfen

### Energie-Dashboard:

1. HA → **Einstellungen → Energie** öffnen
2. „Solarproduktion" hinzufügen → `sensor.sma_total_yield`
3. „Netz-Einspeisung" → `sensor.sma_grid_feedin_total`
4. „Netzbezug" → `sensor.sma_grid_drawfrom_total`

---

## Schritt 7: Android-Tablet einrichten

### Fully Kiosk Browser installieren:

1. **Fully Kiosk Browser** aus dem Play Store installieren (kostenlos oder Pro ~6 €)
2. Einstellungen → **Remote Administration** aktivieren
   - Port: `2323`
   - Passwort setzen (in `secrets.yaml` eintragen als `fkb_password`)
3. Start-URL: `http://192.168.1.10:8123/lovelace/home?kiosk`
4. **Kiosk-Modus aktivieren** (Statusleiste ausblenden)
5. **Motion Detection** aktivieren (Bildschirm durch Bewegung wecken)
6. „Keep Screen On" auf `false` (HA steuert Bildschirm)

### HA Companion App (für Push-Benachrichtigungen):

1. **Home Assistant** App aus dem Play Store installieren
2. Mit HA verbinden, Benachrichtigungen erlauben
3. In `secrets.yaml` eintragen: `notify_person1_device: mobile_app_<gerätename>`
   (Gerätename unter HA → Einstellungen → Integrationen → Companion App → Gerät)

### Tablet dauerhaft laden:

- Wandhalterung montieren, dauerhaft per USB-C laden
- Android: Einstellungen → Akku → „Beim Laden nie ausschalten" oder
  `adb shell settings put global stay_on_while_plugged_in 3`

---

## Schritt 8: Lovelace-Dashboard anpassen

1. HA öffnen → **Dashboards** → Dashboard-Editor
2. Die YAML-Datei aus `homeassistant/lovelace/main_dashboard.yaml` als Referenz verwenden
3. Entitätsnamen ggf. anpassen (z. B. `camera.reolink_doorbell` → echter HA-Entitätsname)

**HACS installieren** (für custom cards wie mini-graph-card):

1. HA → **Terminal & SSH** Add-on installieren
2. `wget -O - https://get.hacs.xyz | bash -`
3. HA neu starten → **Einstellungen → Integrationen → HACS** aktivieren
4. HACS → Frontend → `mini-graph-card`, `button-card` installieren

---

## Schritt 9: Testen

### Türklingel-Test:
1. Türklingel läuten → HA-Logs prüfen (`binary_sensor.reolink_doorbell_visitor` → `on`)
2. Tablet sollte sich einschalten und Türklingel-Ansicht zeigen
3. Push-Benachrichtigung auf Handy prüfen

### Solar-Test:
1. Tagsüber: `sensor.sma_power_ac` > 0 W
2. Energie-Dashboard zeigt Balken

### Automation-Test:
- HA → **Einstellungen → Automationen** → Automation manuell ausführen

---

## Tipps & Tricks

- **SSL aktivieren:** DuckDNS Add-on + Let's Encrypt für sicheren Fernzugriff
- **Nabu Casa:** Einfachste Option für Fernzugriff (5 €/Monat, unterstützt HA-Entwicklung)
- **Backups:** HA → Einstellungen → Backups → automatische tägliche Backups aktivieren
- **HACS:** Community-Store für zusätzliche Integrationen und UI-Karten
- **Node-RED:** Visueller Flow-Editor für komplexe Automationen (als Add-on verfügbar)
- **Zigbee2MQTT:** Für Zigbee-Geräte (Lampen, Schalter, Sensoren) ohne proprietäre Hubs

---

## Häufige Probleme

| Problem | Lösung |
|---------|--------|
| Reolink-Kamera nicht erreichbar | Feste IP prüfen, Firewall im Router |
| SMA nicht gefunden | WebConnect am Wechselrichter aktivieren |
| Tablet wacht nicht auf | FKB Remote Admin prüfen, Port 2323 im WLAN erreichbar? |
| Push-Nachrichten kommen nicht | Companion App neu verbinden, `notify_*_device` in secrets.yaml prüfen |
| MQTT-Verbindung schlägt fehl | Mosquitto-Logs prüfen: `docker compose logs mosquitto` |
