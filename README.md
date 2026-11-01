# ESP32 bioanalysis

An open collaboration workspace for a small ESP32 fingertip PPG/ECG
measurement study. The project asks whether a second-derivative pulse-shape
measure becomes unreliable before beat-timing measures as optical signal
strength falls.

This is a measurement-validity project, not a clinical device or a source of
diagnoses, vascular-age estimates, or SpO2 results.

## What this is

A small fingertip sensor rig on an ESP32. A light sensor reads the pulse at three colours (green, red, infrared) while an ECG board reads the heartbeat, all on one clock. We turn the LED brightness down step by step and ask one question: does a pulse-*shape* number (b/a, from the second derivative of the pulse) fall apart before the beat *timing* numbers do? That is a question about how trustworthy wearable readings are when the signal gets weak. It is not a clinical device.

## Available information

| Area | Document |
|---|---|
| Research question and hypotheses | [01-proposal.md](01-proposal.md) |
| Hardware, wiring, firmware, and measurement ladder | [02-build-and-measure.md](02-build-and-measure.md) |
| Starting literature set | [03-papers.md](03-papers.md) |
| Structured source extraction | [docs/parsed/](docs/parsed/) |
| Project-facing reading notes | [docs/studied/](docs/studied/) |
| Shared task list | [TODO.md](TODO.md) |
| Contribution and data rules | [CONTRIBUTING.md](CONTRIBUTING.md) |

## Reading order

1. [01-proposal.md](01-proposal.md): the question and the hypotheses.
2. [03-papers.md](03-papers.md): read the first three papers.
3. [02-build-and-measure.md](02-build-and-measure.md): keep it open while wiring.

The initial source material is intentionally small. Hardware procurement,
firmware, analysis code, and parsed/studied literature notes should be added
through focused pull requests as the team agrees on them.

## First week checklist

- [ ] Board shows up over USB-C and blinks an LED (Arduino settings: Flash **8MB**, PSRAM **Disabled**).
- [ ] Multimeter on the 18650 (voltage and polarity) before it ever goes in the holder.
- [ ] Solder the male headers onto the MAX30105.
- [ ] MAX30105 answers on I²C at address `0x57` (SDA GPIO15, SCL GPIO16).
- [ ] Green LED check: drive the green channel (`LED3_PA`, register `0x0E`) and look through a phone camera. No green means it is a relabelled MAX30102; return it.
- [ ] Add the analysis package and run its synthetic checks.

## Safety, short

- **3.3 V only.** AD8232 and both MAX boards go on the T-Energy **3V3** pin. The board also has a **5V** pin right there: that is the easy mistake. The AD8232 absolute maximum is 3.5 V.
- **ECG leads on a body only on battery.** Laptop unplugged from the wall, or connected through the USB isolator. A laptop on mains power can leak current into the electrodes.
- **For now nobody wears the ECG leads.** Test the AD8232 on the bench (3.3 V, GND, OUTPUT).
- **18650:** buy a *protected* cell; the holder is not protection. Multimeter check first (about 3.6 to 4.2 V, right polarity). Do not force a cell that jams.
- **No soldering with the cell in.**
- **LED current:** the MAX goes up to about 51 mA per LED. Firmware stays well below that; the code default cap is 25 mA (`MAX_LED_MA` in `code/safety.py`). Never point an LED at an eye. Use an opaque finger clip.

## Data rule, short

Putting the sensor on your own finger to see that it works is fine. **Saving or analysing anyone's data for a study or paper needs the university ethics process first**, and that includes our own fingers if the trace ends up in a paper. Until then the flag `HUMAN_RECORDING_ENABLED` in `code/safety.py` stays `False`, and the only dataset is synthetic signals.

Later, when recording is allowed: stop at once on dizziness, pain, skin reaction, a warm device, or if the person asks. No names, student IDs, or phone numbers in filenames or CSV headers (use a random session ID). Pulse waveforms and finger photos count as sensitive personal data. Raw files are never edited and never pushed to a public repo.

## Board gotchas

- The T-Energy is an **ESP32-S3FN8**: 8 MB flash, **PSRAM off**. Many online listings for "T-Energy-S3" are a different chip (N16R8). Trust the chip in your hand.
- ECG OUTPUT goes to **GPIO1**, not GPIO3. GPIO3 is the board's battery-voltage reader.
- MAX30105 and MAX30102 both live at address `0x57`, so they need **separate I²C buses** (05 on SDA 15 / SCL 16, 02 on SDA 8 / SCL 9).
- The part-ID register reads the same on both chips. Only the green LED tells a 05 from a 02.
- Some breakouts can have red and infrared swapped. On a finger, the infrared pulse should be bigger than the red one; if not, check the channels.
- I²C is not a cable. It is two wires (SDA and SCL). Dupont jumpers are just the plugs.

## What not to do

- No "vascular age in years". No SpO2 or medical interpretation, ever, to anyone.
- No "we fixed melanin bias". Turning the LED down is not the same thing as darker skin; it is a controlled stand-in for a weaker signal.
- Do not present LED auto-gain by skin tone as our new idea. Others have published it.
- Skip for now: motion, camera-based pulse (rPPG), machine learning, polarizers, custom analog front ends, pressure sensors.
- Do not change the hypotheses after seeing real traces. If something unexpected shows up, change the *next* experiment instead.

## Scope and data safety

The source folder was separated from a larger local vault for collaboration.
Only project material intended for this public repository belongs here. Raw
human recordings and identifiable data must remain outside GitHub; see
[CONTRIBUTING.md](CONTRIBUTING.md) and [.gitignore](.gitignore).
