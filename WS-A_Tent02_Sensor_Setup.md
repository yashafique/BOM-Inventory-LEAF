# WS-A — Tent02 Sensor Deployment Plan

**Owner:** Yahya Shafique
**Scope:** First real sensor (temp/humidity) live in tent02, feeding Pi → MQTT → HA
**Constraint:** In office today until 15:30; full day Thursday; no work planned Friday.
**Key risk:** If parts don't ship in time for Thursday, deployment needs a fallback (see below).

---

## 1. Equipment required

### Core sensor
| Item | Qty | Approx. cost | Notes |
|---|---|---|---|
| Atlas Scientific EZO-HUM (probe, stainless body) | 1 (start) | ~$70–75 | Reads humidity, dew point, air temp in one unit |

### Wiring (direct-to-GPIO route — fastest, no extra board)
| Item | Qty | Approx. cost | Notes |
|---|---|---|---|
| Female-to-female jumper wires | 1 pack (4+ needed) | ~$5–8 | GND, +V, data (SDA), clock (SCL) |
| Breadboard (half-size) | 1 | ~$5 | Optional but strongly recommended for clean wiring/debug |
| Raspberry Pi (any I2C-capable model) | 1 | — (may already have) | Confirm which unit is dedicated to this |

### Optional — carrier board route (defer unless scaling this week)
| Item | Qty | Approx. cost | Notes |
|---|---|---|---|
| Whitebox Labs Tentacle T3 | 1 | ~$50–60 | Hosts up to 5 EZO devices, handles I2C addressing/isolation — worth it once doing multi-probe averaging, not needed for a single first sensor |
| Atlas Gen 2 USB EZO Carrier Board | 1 | ~$35–40 | Alternative: connects EZO circuit via USB instead of GPIO — simplest single-sensor path if GPIO wiring proves troublesome |

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

## 3. Open items / blockers

- [ ] Confirm order placed today, note actual lead time once quoted
- [ ] Decide fallback sensor now, in case EZO-HUM doesn't arrive by Thursday
- [ ] Confirm with Luigi: is one sensor / one location acceptable for this pass, with multi-location averaging deferred?
- [ ] Confirm with Luigi: is fallback sensor acceptable if EZO-HUM is delayed, or does it need to be Atlas specifically?
- [ ] MQTT topic name is placeholder — finalize once Grace's naming convention doc arrives

---

## 4. Timeline

- **Today (until 15:30):** place order (EZO-HUM + fallback), confirm Pi I2C setup, prep breadboard/jumpers
- **Thursday (full day):** wire sensor (real or fallback) to Pi, confirm raw reading, publish to MQTT, confirm HA receives it
- **No Friday work planned** — demo material should be finalized and handed off in advance if the live demo lands Friday
