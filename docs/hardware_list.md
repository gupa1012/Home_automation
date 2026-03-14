# Hardware-Liste: Smart Home Schaltzentrale

## Pflicht-Hardware (Kernkomponenten)

### Home Assistant Server

| Artikel | Preis ca. | Bemerkung |
|---------|-----------|-----------|
| **Beelink Mini S12 Pro** (Intel N100, 16 GB RAM, 500 GB NVMe) | 150–180 € | Empfohlen: leise, stromsparend (~6 W Idle), kein Lüftergeräusch |
| *Alternative:* Raspberry Pi 5 (8 GB) + 64 GB SD + Gehäuse | 100–130 € | Ausreichend für Basis-Setup, kein NVMe ohne HAT |
| *Alternative:* Intel NUC 12 / Minisforum | 250–350 € | Falls viele Add-ons / NVR geplant |

> **Empfehlung:** Beelink Mini S12 Pro – bestes Preis-Leistungs-Verhältnis für HA mit NVR.

### Netzwerk

| Artikel | Preis ca. | Bemerkung |
|---------|-----------|-----------|
| **TP-Link TL-SG108PE** (8-Port PoE+ Switch, 65 W) | 55–70 € | Versorgt bis zu 4 PoE-Kameras mit Strom und Daten |
| *Alternative:* TP-Link TL-SG1210MPE (10-Port, 150 W PoE+) | 80–100 € | Für mehr Kameras |
| WLAN-Router oder Access Point (z. B. TP-Link EAP670) | 80–100 € | Stabiles WLAN für Tablet, WLAN-Geräte |

### Reolink Türklingel

| Artikel | Preis ca. | Bemerkung |
|---------|-----------|-----------|
| **Reolink Video Doorbell PoE** | 70–85 € | Empfohlen: PoE, 5 MP, 180° Blickwinkel |
| *Alternative:* Reolink Video Doorbell WiFi | 65–80 € | WLAN-Variante, keine PoE-Verkabelung nötig |
| Türklingel-Unterputzdose (falls nicht vorhanden) | 5–10 € | |
| **Vorhandener Türklingel-Transformator** (8–24 V AC) | ggf. vorhanden | Für WiFi-Variante, PoE-Variante braucht keinen |

> **Empfehlung:** PoE-Variante – zuverlässiger, kein WLAN-Ausfall-Risiko.

### Reolink Außenkameras

| Artikel | Preis ca. | Bemerkung |
|---------|-----------|-----------|
| **Reolink RLC-810A** (4K PoE, SmartDetection) | 50–65 € / Stück | Empfohlen: 4K, Personen-/Fahrzeugerkennung |
| *Alternative:* Reolink RLC-823A (4K PoE + Spotlight) | 70–85 € / Stück | Mit Scheinwerfer |
| PoE-Verlängerungskabel (Cat.6, je nach Strecke) | 10–30 € | Für Montage außen |
| Kabelkanal / Außeninstallation | variabel | |

> **Empfehlung:** 2–4 Kameras für Einfahrt, Garten, Terrasse, Seite.

### Android-Wandtablet

| Artikel | Preis ca. | Bemerkung |
|---------|-----------|-----------|
| **Samsung Galaxy Tab A9+** (11", 4 GB RAM, 64 GB) | 250–280 € | Empfohlen: gutes Display, WLAN 6, USB-C |
| *Alternative:* Lenovo Tab M10 Plus (3. Gen) | 180–220 € | Günstiger, ausreichend für HA |
| **Wandhalterung mit USB-Ladekabel** (z. B. Hecklerdesign [U]) | 60–80 € | Elegante Wandmontage, dauerhaft laden |
| *Alternative:* 3D-gedruckte Halterung + Unterputz-USB | 10–20 € | DIY-Lösung |

### SMA Solar (falls noch nicht vorhanden)

| Artikel | Bemerkung |
|---------|-----------|
| **SMA Sunny Boy** (Einphasen-Wechselrichter) oder **SMA Sunny Tripower** (Dreiphasen) | Vorhandenes Gerät, LAN-Anbindung sicherstellen |
| **SMA Sunny Home Manager 2.0** | Für Energiemanagement, Überschusssteuerung; kommuniziert via LAN |
| Ethernet-Kabel vom Wechselrichter/SHM zum Switch | Falls noch nicht vorhanden |

---

## Empfohlene Erweiterungen (spätere Ausbaustufen)

### Zigbee-Stick (für Smart Lighting, Sensoren)

| Artikel | Preis ca. | Bemerkung |
|---------|-----------|-----------|
| **SONOFF Zigbee 3.0 USB Dongle Plus** | 15–20 € | Empfohlen für Zigbee2MQTT |
| *Alternative:* ConBee II / ConBee III | 30–40 € | |

### Steckdosen / Relais

| Artikel | Preis ca. | Bemerkung |
|---------|-----------|-----------|
| **Shelly Plus 1PM** (WLAN-Relais) | 12–18 € | Lichtschalter, Rollladenmotor |
| **Shelly Plus 2PM** | 18–22 € | Rollladen-Steuerung (2 Kanäle) |
| **Shelly Plus Plug S** | 12–15 € | Smarte Steckdose mit Strommessung |

### Sonstiges

| Artikel | Preis ca. | Bemerkung |
|---------|-----------|-----------|
| USV APC BE600M2 | 60–80 € | Notstrom für HA-Server bei Stromausfall |
| Raspberry Pi Zero 2W | 15–20 € | Optional: Bluetooth-Proxy für BLE-Geräte |
| Netatmo Wetterstation | 100–130 € | Innensensoren, Außensensoren, CO2 |

---

## Gesamtkosten (Schätzung)

| Kategorie | Kosten ca. |
|-----------|-----------|
| HA-Server (Beelink Mini S12 Pro) | 165 € |
| Netzwerk (PoE-Switch + Router/AP) | 130 € |
| Reolink Türklingel (PoE) | 80 € |
| Reolink Kameras × 3 (RLC-810A) | 180 € |
| Android-Tablet + Halterung | 330 € |
| Kabel, Kleinteile | 40 € |
| **Gesamt (ohne SMA Solar)** | **~925 €** |
