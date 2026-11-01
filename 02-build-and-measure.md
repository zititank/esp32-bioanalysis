# Build and measure

## Parts and what each one does

| Part | Job |
|---|---|
| LILYGO T-Energy ESP32-S3FN8 (18650 holder, USB-C) | The brain. One clock timestamps ECG, pulse, and LED steps. |
| MAX30105 (green, red, infrared) | Test sensor. Green is the stress test (most melanin-sensitive). |
| MAX30102 (red, infrared) | Reference sensor on its own I²C bus. Still to buy. |
| AD8232 (purple, 3.5 mm jack) + 3-lead cable | ECG: the heartbeat clock (R-peak). |
| Protected 18650 cell | Power, so the ECG never touches mains. |
| USB isolator | Keeps the laptop's mains power out when USB is plugged in. |
| Opaque finger clips ×2 | Block room light; one per MAX board. |

Anything not on the desk yet should be added to the hardware procurement
checklist in a future project pull request.

## Wiring

I²C is just two wires, SDA (data) and SCL (clock); Dupont jumpers are the plugs. Solder headers onto the MAX first.

| From (T-Energy) | To | Note |
|---|---|---|
| 3V3 | MAX30105 VIN | **Never the 5V pin** |
| GND | MAX30105 GND | |
| GPIO15 | MAX30105 SDA | First I²C bus |
| GPIO16 | MAX30105 SCL | First I²C bus |
| (none) | MAX30105 INT | Leave unconnected |
| 3V3 / GND | MAX30102 VIN / GND | **Never the 5V pin** |
| GPIO8 / GPIO9 | MAX30102 SDA / SCL | Second I²C bus (both chips are `0x57`) |
| 3V3 | AD8232 3.3V | Never 5 V |
| GND | AD8232 GND | |
| GPIO1 | AD8232 OUTPUT | ADC1. **Not GPIO3** (battery reader) |
| GPIO4 / GPIO5 | AD8232 LO+ / LO− | Leads-off flags |

No small resistors next to SDA and SCL on the breakout? Add 4.7 kΩ pull-ups to 3.3 V on each line (4.7 k, not 4.7 Ω).

LED current registers: `LED1_PA` `0x0C` red, `LED2_PA` `0x0D` infrared, `LED3_PA` `0x0E` green. They reach about 51 mA; we clamp at 25 mA by default.

## Firmware build steps

Each step works before the next one starts.

1. **Blink.** Board enumerates over USB; an LED or log line proves the toolchain. Arduino: Flash 8MB, PSRAM Disabled.
2. **Read the MAX** at **200 Hz or more** (the code refuses lower rates for second-derivative work).
3. **Stepped LED currents.** Equal current on each colour, stepped down, capped well below 51 mA.
4. **ECG on the same clock.** OUTPUT on GPIO1, leads-off on GPIO4/5. Bench only for now.
5. **Write CSV** with exactly these columns:

```text
timestamp_ms, led_step, lambda, ac, dc, pi_proxy, ecg_adc, lo_plus, lo_minus
```

`ac` and `dc` are the pulsing and steady parts of the light; `pi_proxy` is their ratio (a perfusion-index stand-in). Signal processing stays in Python, not firmware. Patterns worth copying: the SparkFun MAX3010x library and OpenHRStrap (ESP32-S3 with AD8232).

## Why this can work

- **Second derivatives amplify noise.** Differentiating is a high-pass filter, so a shape feature like b/a needs a cleaner signal than finding *when* a beat happens. As the LED goes down, shape should suffer first. That is the bet we test, not assume.
- **The ECG is a fair clock.** The R-peak is electrical; skin colour and LED brightness do not touch it.
- **A ratio cancels plain scaling.** b/a divides one wave by another, so just shrinking the pulse should not move its average. If the average *does* move at matched signal strength, the waveform itself changed (H2 in [the proposal](01-proposal.md)).

## Software path

All Python is in `code/`, standard library only so far (the lab PC has no numpy).

| File | What it does |
|---|---|
| `synthetic_signals.py` | Fake ECG and pulse signals, with a simple light-loss model and noise |
| `signal_ops.py` | Derivatives, Savitzky–Golay smoothing, signal-to-noise |
| `ppg_features.py` | Pulse peaks and foot, second-derivative a and b waves, b/a |
| `demo_pipeline.py` | Runs the chain; prints b/a mean, variability, detection rate |
| `hypothesis_failtests.py` | Synthetic cases built to make each named hypothesis fail |
| `safety.py` | LED cap, minimum sample rate, human-recording flag, banned phrases |
| `tests/` | Unit tests for the safety rules and fail-tests |

```bash
cd code
python3 hypothesis_failtests.py           # writes out/hypothesis_failtests.md
python3 demo_pipeline.py
python3 -m unittest discover -s tests -v
```

The fail-tests show the synthetic model *can* say no. Example: a model that only scales brightness cannot shift the b/a average, so H2 fails there by construction. That checks our tools; it says nothing about real skin.

**Next:** an ECG module (R-peaks, heart rate) and an analysis module. Lift, do not rewrite: NeuroKit2 for R-peaks and signal quality, pyPPG for the a to e waves (NeuroKit2 does not find them). That is where numpy comes in. Use one smoother with its window set in milliseconds, not samples. Always print detection rate next to variability.

## Verification ladder

Climb in order.

1. **Synthetic fail-tests** pass and can still fail on purpose.
2. **Bench signals.** Sensor on your own finger to check wiring and channels. Nothing saved for the study.
3. **Quality report on every CSV** (PC side) before any variability claim.
4. **Real recordings** only after the university ethics process, with `HUMAN_RECORDING_ENABLED` switched on for that reason only.
