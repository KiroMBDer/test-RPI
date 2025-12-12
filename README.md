# Room Comfort & Activity Monitor

IoT system using Raspberry Pi Pico W with sensors, servo motor, LED, and desktop GUI.

## Components

| Component | Pin | Function |
|-----------|-----|----------|
| Potentiometer | GP26/ADC0 | Simulated temp (0-100%) → controls servo |
| LDR | GP27/ADC1 | Light level (0-100%) → controls LED |
| Button | GP15 | User input (0/1) |
| Internal Temp | ADC4 | MCU temperature (°C) |
| Extra Analog | GP28/ADC2 | Optional sensor |
| Servo | GP16 | 0° when cold, 90° when hot |
| LED | GP14 | ON when dark, OFF when bright |

## Wiring

```
POTENTIOMETER:          LDR:                    BUTTON:
3V3 ─── Pot ─── GND     3V3 ─ LDR ─┬─ GP27     GP15 ─┬─ Button
        │                          │                  │
       GP26                     10kΩ                GND
                                   │
                                 GND

SERVO:                  LED:
GP16 ── Signal          GP14 ── 220Ω ── LED ── GND
5V ──── VCC (external)
GND ─── GND (common)
```

## ThingSpeak Setup

Create a channel with these fields:
- Field 1: sim_temp
- Field 2: light_level
- Field 3: button_state
- Field 4: internal_temp
- Field 5: extra_analog (optional)

## Configuration

**main.py** (Pico W):
```python
WIFI_SSID = "your_wifi"
WIFI_PASSWORD = "your_password"
THINGSPEAK_WRITE_API_KEY = "your_write_key"
TEMP_THRESHOLD = 60.0
LIGHT_THRESHOLD = 40.0
```

**iot_viewer.py** (Desktop):
```python
CHANNEL_ID = "your_channel_id"
READ_API_KEY = "your_read_key"
```

## Running

**Pico W:**
1. Flash MicroPython
2. Copy `main.py` to Pico W
3. Runs automatically on boot

**Desktop:**
```bash
pip install requests matplotlib
python iot_viewer.py
```

**Testing (no hardware):**
```bash
python test_thingspeak.py -s   # Single upload
python test_thingspeak.py -c   # Continuous
```

## Actuator Logic

| Sensor | Condition | Action |
|--------|-----------|--------|
| Temp (pot) | < 60% | Servo at 0° |
| Temp (pot) | >= 60% | Servo at 90° |
| Light (LDR) | < 40% | LED ON (dark) |
| Light (LDR) | >= 40% | LED OFF (bright) |

## Files

- `main.py` - Pico W firmware
- `iot_viewer.py` - Desktop GUI
- `test_thingspeak.py` - Test script
- `README.md` - Documentation
