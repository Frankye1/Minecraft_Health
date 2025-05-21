# 🧱 Minecraft Health LED Dashboard mit Node-RED und MQTT

Visualisiere die Lebenspunkte von Minecraft-Spielern auf einem RGB-LED-Streifen in Echtzeit. Dieses Projekt verbindet Minecraft über RCON mit Node-RED und steuert über MQTT einen LED-Streifen , um den aktuellen Gesundheitszustand eines Spielers farblich darzustellen.

---

## 🧠 Projektübersicht

### Was macht dieses Projekt?

- Ruft über RCON regelmäßig Minecraft-Spielerinformationen ab
- Zeigt alle verbundenen Spieler in einem Dashboard-Dropdown an
- Ermöglicht die Auswahl eines Spielers zur Überwachung
- Fragt kontinuierlich den Health-Wert des gewählten Spielers ab
- Rechnet die Lebenspunkte in ein RGB-Farbverlaufsmuster um
- Sendet die Farbdaten an ein MQTT-Topic (`led`)
- Zeigt den Health-Wert auch im UI-Dashboard als Text an

---

## 🔧 Installation & Einrichtung

### Voraussetzungen

- ✅ [Node-RED](https://nodered.org/) (z. B. über `npm install -g node-red`)
- ✅ Minecraft Java Server mit aktiviertem RCON (Standardport: 25575)
- ✅ MQTT-Broker (z. B. Mosquitto)
- ✅ ESP32/ESP8266 mit MQTT-Unterstützung und angeschlossenem LED-Streifen
- ✅ RGB-LED-Streifen 

### Node-RED Setup

1. **Node-RED installieren** (falls nicht vorhanden)  
   Anleitung: https://nodered.org/docs/getting-started/

2. **Flow importieren**  
   In Node-RED:
   - Klick auf Menü (☰) → *Import* → *Clipboard*
   - Kopiere den Flow-JSON aus diesem Repository

3. **MQTT-Broker konfigurieren**
   - Doppelklick auf den `mqtt-broker` Node
   - Stelle die Verbindung zu deinem MQTT-Server her (z. B. `localhost` oder IP deines Brokers)
   - ![image](https://github.com/user-attachments/assets/8ddb78f8-fab4-4d41-b372-b8c9f47ad384)


4. **RCON-Serverdaten eintragen**
   - Bearbeite den `serverconfig` Node
   - Trage Minecraft-Server-IP, RCON-Port und Passwort ein

5. **Dashboard öffnen**
   - Starte Node-RED: `node-red`
   - Öffne das Dashboard unter: `http://localhost:1880/ui`

---

## 🧪 Funktionsweise im Detail

### 1. Spieler abrufen

- Alle 10 Sekunden wird eine RCON-Anfrage an den Minecraft-Server gesendet (`serverinfo`)
- Die Spielernamen werden extrahiert und im Dropdown-Menü angezeigt (`ui_dropdown`)

### 2. Spieler auswählen

- Im UI kann ein Spieler aus der Liste ausgewählt werden
- Der Name wird im `flow context` gespeichert
- Ein Button startet die Health-Überwachung

### 3. Health-Abfrage

- Ein `setInterval` fragt jede Sekunde den Gesundheitswert des gewählten Spielers ab
- Die Health-Werte werden an zwei Nodes weitergeleitet:
  - **Textanzeige im Dashboard** (`ui_text`)
  - **RGB-Farbumrechnung** (`Health to RGB Color`)

### 4. RGB-Mapping

- Die Health wird in ein Farbschema für LEDs umgerechnet:
  - **Voll gesund:** grüne LEDs
  - **Halb gesund:** Übergang zu Gelb
  - **Niedrige HP:** rot
- Bis zu 16 LEDs werden mit entsprechenden RGB-Werten angesteuert

### 5. MQTT-Ausgabe

- Die RGB-Werte werden als Array an das MQTT-Topic `led` gesendet
- Ein ESP32/ESP8266 mit passender Firmware kann dies empfangen und visualisieren

---

## 💡 Beispiel RGB-Ausgabe (MQTT Payload)

```json
[
  { "r": 0, "g": 255, "b": 0 },
  { "r": 64, "g": 191, "b": 0 },
  { "r": 128, "g": 128, "b": 0 },
  { "r": 255, "g": 0, "b": 0 },
  ...
]
