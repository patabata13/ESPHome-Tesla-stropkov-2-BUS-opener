# ESPHome Tesla Stropkov 2-Bus Opener

A DIY **Tesla Stropkov 2-Bus intercom opener** using an **ESP8266 (WeMos D1 Mini)** and a **4-channel relay module** to emulate button presses for remote door unlocking.

This is my first ever DIY project with ESP Home as well as first entry in Github. So be nice and any suggestions and modifications are welcomed :)


## 🚀 Features
- **Emulates button presses** for:
  - `1` (Required to press when you're trying to open door without anyone calling you)
  - `Lock` (Opening the door)
  - `Headset Pickup` (Simulating lifting the intercom handset)
- **ESPHome-powered** for seamless Home Assistant integration
- **WiFi connectivity** for remote control
- **Stable operation** with state restoration and boot flicker prevention


## 🛠️ Hardware Requirements
**Please note, that some soldering skills are required to make this. For the original intercom board as well as for the D1 mini board**
| Component         | Description |
|------------------|-------------|
| [ESP8266 (D1 Mini)](https://www.laskakit.cz/wemos-d1-mini-esp8266-wifi-modul/) | Microcontroller running ESPHome |
| [4-Channel Relay Module](https://www.laskakit.cz/4-kanaly-rele-modul-5vdc-250vac-10a/) | Controls button press simulation |
| Tesla Stropkov 2Bus Intercom | Target intercom system (my model is Elegant, but it should work with others) |
| Wires & Connectors | For wiring the relay to the intercom |
| 3D printer | For a case (3D model available for download) |

---

## 🔌 Wiring Diagram
| Relay Channel | Function |
|--------------|------------|
| Relay 1 (D1) | Simulates lifting the handset |
| Relay 2 (D7) | Simulates pressing `1` |
| Relay 3 (D6) | Simulates pressing `Lock` |
| Relay 4 (D5) | (Optional) Future expansion |

---

## 📜 ESPHome Configuration
```yaml
esphome:
  name: tesla-2bus-opener
  friendly_name: Tesla 2BUS Opener
  on_boot:
    priority: -200  # Runs early
    then:
      - switch.turn_off: relay_1
      - switch.turn_off: relay_2
      - switch.turn_off: relay_3
      - switch.turn_off: relay_4

esp8266:
  board: d1_mini
  restore_from_flash : true
  early_pin_init: false



# Enable logging
logger:

# Enable Home Assistant API
api:
  encryption:
    key: "YourEncryptionKey"

ota:
  - platform: esphome
    password: "YourOTAPassword"

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password

  # Enable fallback hotspot (captive portal) in case wifi connection fails
  ap:
    ssid: "Tesla-2Bus-Opener"
    password: "ChooseAPPassword"



switch:
  - platform: gpio
    pin: D1 
    name: "Sluchátko"
    id: relay_1
    inverted: true
    restore_mode: RESTORE_DEFAULT_OFF
  - platform: gpio
    pin: D7  
    name: "Tlačítko 1"
    id: relay_2
    inverted: true
    restore_mode: RESTORE_DEFAULT_OFF
  - platform: gpio
    pin: D6  
    name: "Tlačítko zámek"
    id: relay_3
    inverted: true
    restore_mode: RESTORE_DEFAULT_OFF
  - platform: gpio
    pin: D5
    name: "Relay 4"
    id: relay_4
    inverted: true
    restore_mode: RESTORE_DEFAULT_OFF
button:
  - platform: restart
    name: "Restartovat"
```

---

## 🎮 Home Assistant Integration
Once ESPHome is flashed and running, Home Assistant will automatically detect the device. You can control the relays via the HA dashboard or automations.

## YAML for Homeassistant automation
```yaml
alias: Tesla 2-bus opener
description: ""
triggers:
  - trigger: state
    entity_id:
      - lock.tesla_2bus_opener_sluchatko
    to: unlocked
conditions: []
actions:
  - delay:
      hours: 0
      minutes: 0
      seconds: 0
      milliseconds: 500
  - action: switch.turn_on
    metadata: {}
    data: {}
    target:
      entity_id: switch.tesla_2bus_opener_tla_tko_1
  - delay:
      hours: 0
      minutes: 0
      seconds: 0
      milliseconds: 100
  - action: switch.turn_off
    metadata: {}
    data: {}
    target:
      entity_id: switch.tesla_2bus_opener_tla_tko_1
  - delay:
      hours: 0
      minutes: 0
      seconds: 0
      milliseconds: 500
  - action: switch.turn_on
    metadata: {}
    data: {}
    target:
      entity_id: switch.tesla_2bus_opener_tla_tko_z_mek
  - delay:
      hours: 0
      minutes: 0
      seconds: 0
      milliseconds: 100
  - action: switch.turn_off
    metadata: {}
    data: {}
    target:
      entity_id: switch.tesla_2bus_opener_tla_tko_z_mek
  - delay:
      hours: 0
      minutes: 0
      seconds: 0
      milliseconds: 500
  - action: switch.turn_on
    metadata: {}
    data: {}
    target:
      entity_id: switch.tesla_2bus_opener_tla_tko_z_mek
  - delay:
      hours: 0
      minutes: 0
      seconds: 0
      milliseconds: 100
  - action: switch.turn_off
    metadata: {}
    data: {}
    target:
      entity_id: switch.tesla_2bus_opener_tla_tko_z_mek
  - delay:
      hours: 0
      minutes: 0
      seconds: 0
      milliseconds: 500
  - action: switch.turn_on
    metadata: {}
    data: {}
    target:
      entity_id: switch.tesla_2bus_opener_tla_tko_z_mek
  - delay:
      hours: 0
      minutes: 0
      seconds: 0
      milliseconds: 100
  - action: switch.turn_off
    metadata: {}
    data: {}
    target:
      entity_id: switch.tesla_2bus_opener_tla_tko_z_mek
  - delay:
      hours: 0
      minutes: 0
      seconds: 10
      milliseconds: 0
  - action: switch.turn_off
    metadata: {}
    data: {}
    target:
      entity_id:
        - switch.tesla_2bus_opener_tla_tko_1
        - switch.tesla_2bus_opener_tla_tko_z_mek
  - action: lock.lock
    metadata: {}
    data: {}
    target:
      entity_id: lock.tesla_2bus_opener_sluchatko
mode: single
```

---

## 🏗️ Installation
1. Flash ESPHome firmware to the D1 Mini.
2. Wire the relays to the Tesla Stropkov intercom (I've used twisted pairs from a leftover UTP cable).
3. Connect to WiFi and add the device to Home Assistant.
4. Test by triggering the relays.
5. Use helper to change switch entity of a Relay_1 `Headset Pickup` to a lock entity
6. Use provided automation (or edit it to your needs) to automate relay switching for automatic unlocking. 

---

## 📸 Screenshots
*(Add images of the setup, wiring, and Home Assistant UI here)*

---

## 🤝 Contributing
Feel free to open issues or submit pull requests to improve the project!

---

## ⚠️ Disclaimer
This is not plug and play solution. Some soldering to a original intercom board is required. You are doing this at your own risk!
This project is intended for educational and personal use. Ensure compliance with any local regulations before modifying intercom systems.

---

### 🚀 Enjoy your smart intercom opener!
