# WS-A — Tent02 Sensor Deployment Plan
 
**Owner:** Yahya Shafique
**Scope:** First real sensor (temp/humidity) live in tent02, feeding Pi → MQTT → HA
**Key risk:** If parts don't ship in time for Thursday, deployment needs a fallback (see below).
 
---
 
## What's needed
 
To get a real sensor live in tent02 this week, the following needs to be ordered/confirmed:
 
1. **Atlas Scientific EZO-HUM probe** — the core sensor (humidity, dew point, air temp in one unit)
2. **Whitebox Labs Tentacle T3** — carrier board for clean I2C connection to the Pi, and the right call if we're scaling to multiple probes later
3. **Jumper wires + breadboard** — likely already have these, confirming
4. **A dedicated Raspberry Pi** — confirming which unit is assigned to this
5. **A fallback sensor (SHT31 or DHT22)** — only needed if the EZO-HUM doesn't ship in time for Thursday
Everything below this is supporting detail: costs, setup steps, and the fallback plan.
 
---
 
## 1. Equipment required
 
### Core sensor
 
| Item | Qty | Approx. cost | Notes |
|---|---|---|---|
| Atlas Scientific EZO-HUM (probe, plastic body) | 1 (start) | ~$55 | Reads humidity, dew point, air temp in one unit |
 
<img src="https://github.com/user-attachments/assets/a77014aa-8c65-4f6e-82dd-90390a7084ce" alt="EZO-HUM" width="500"/>

### Strongly recommended — carrier board route
 
| Item | Qty | Approx. cost | Notes |
|---|---|---|---|
| Whitebox Labs Tentacle T3 | 1 | ~$50–60 | Hosts up to 5 EZO devices, handles I2C addressing/isolation — worth it once doing multi-probe averaging, not needed for a single first sensor |
 
<img src="https://github.com/user-attachments/assets/a1820bed-7db8-47d3-b45b-1db4af9c7745" alt="Tentacle T3" width="500" />
### Wiring
 
| Item | Qty | Approx. cost | Notes |
|---|---|---|---|
| Female-to-female jumper wires | 1 pack (4+ needed) | ~$5 (May Have) | GND, +V, data (SDA), clock (SCL) |
| Breadboard | 1 | ~$5 (May Have) | Optional but strongly recommended for clean wiring/debug |
| Raspberry Pi (any) | 1 | — (may already have) | Confirm which unit is dedicated to this |
 
### Fallback (only if EZO-HUM doesn't ship by Thursday)
 
| Item | Qty | Approx. cost | Notes |
|---|---|---|---|
| SHT31 or DHT22 temp/humidity sensor | 1 | ~$10–25 | Common, fast-shipping, I2C or digital-pin option — stand-in to still get *something* live Thursday; swap for EZO-HUM once it arrives |
 
---
 
## 2. Setup steps (direct-wire path)
 
1. Confirm EZO-HUM is in I2C mode (default is UART — check datasheet for mode switch).
2. Enable I2C on the Pi (`raspi-config` → Interfacing Options → I2C), reboot.
3. Wire: GND → GND, VCC → 3.3V/5V (confirm from datasheet), SDA → Pi SDA pin, SCL → Pi SCL pin.
4. Run `i2cdetect -y 1` to confirm the sensor's address is visible.
5. Pull a raw reading locally (Atlas sample Python scripts) before touching MQTT.
6. Publish reading to MQTT topic (pending final naming convention from Grace — placeholder: `cea/tent02/humidity_temp/probe1`).
7. Confirm HA subscribes and displays the value.
---
 
