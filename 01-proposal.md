# 01 — Proposal: does pulse shape fail before pulse timing?

## The question

When the optical pulse at the fingertip gets weaker (LED current turned down; darker skin may do the same), does a second-derivative pulse-shape index, **b/a**, break down *before* beat timing (ECG heart rate, pulse arrival time) on the same heartbeats?

## Background

- **PPG** shines an LED into the finger and measures the light that comes back; each heartbeat makes a small wave.
- **ECG** measures the heart's electrical activity. Its R-peak marks each beat and does not depend on skin optics.
- **b/a:** the second derivative of the PPG wave has peaks and dips labelled a to e. The b/a ratio is a published shape index that changes with age (Takazawa 1998). We use it only as a shape measure, never as "vascular age in years".
- **Pulse arrival time (PAT):** the delay from the ECG R-peak to the foot of the optical pulse.
- **Skin-tone bias** in optical sensors is well documented for blood oxygen and heart rate. That part is crowded.

**The gap:** nobody has compared *shape* against *timing* on the same beats while signal quality falls, with skin tone recorded.

## Why it matters

Wearables increasingly report shape-derived numbers. A second derivative amplifies noise, so shape plausibly needs a cleaner signal than a timestamp. If shape dies first under weak light, those numbers are least trustworthy where signals are weakest. This is a measurement-validity question, not a clinical claim.

## Design

- **Within-person LED-current sweep:** step the LED down, record at each step. Each person is their own control.
- **Three wavelengths:** green (the stress test, most pigment-sensitive), red, infrared (IR).
- **ECG as a skin-independent clock.** ECG heart rate plus the IR pulse foot are the timing reference. Green heart rate is not the clock, because it can itself fail on darker skin.
- **Fingertip**, same clip and finger, tightness noted. Sampling at 200 Hz or more.
- **Skin tone** (ITA°, from a photo of the contact finger, not the face) is a moderator recorded alongside, not the main axis.

## Hypotheses

Fixed before any data; not changed after seeing traces.

| | Statement | Shown wrong if |
|---|---|---|
| **H1** | As signal quality falls, b/a detection rate drops and/or b/a variability rises **faster** than heart-rate or arrival-time variability on the same beats. | With quality gating, shape does not fail before timing. |
| **H2** | **Average** b/a shifts even after signal strength is matched: the light path changes, not only the noise. | Mean recovers once strength is matched; only variability or detection changes. |
| **Null** | Shape and timing fail together. | — |

H1, H2, both, or the null are all reportable results. If H1 is null, we don't relabel H2 as "what we meant all along".

**Telling H1 from H2 (same sweep, no extra hardware):** compute b/a on raw and on amplitude-normalised beats (plain scaling cancels in a ratio). Match signal strength via the LED, then compare the mean. Mean recovers → H1-like. Mean stays shifted → H2-like, expected stronger on green and red than IR.

**Correction map (only if H1 or H2 shows up):** the LED or perfusion level where each metric stops being trustworthy becomes a practical rule: raise the LED, or flag those beats. That is prevention, not "solving melanin". No map, no correction.

**Optional side ideas** (droppable, not promised results):

- **H3:** wavelengths differ in the *order they fail* as LED falls, not in mean b/a at high current.
- **H4:** perfusion index is a better x-axis than the LED setting.
- **H5:** detection fails before variability does.
- **H6:** several arrival-time definitions, since shape changes can fake a delay.
- **H7:** finger-clip tightness as a confound.

## Measures

- **Shape:** mean b/a, variability (coefficient of variation, CV), a/b detection rate. A CV rise can just be the detector failing, so both are reported, CV with and without quality gating.
- **Timing:** CV of heart rate and CV of PAT (ECG plus IR foot).
- **Signal quality:** perfusion index, AC and DC per wavelength.

Headline figure: metric dropout vs LED current (or perfusion index), everything on the same axes.

## Limits

- **Small, young pilot (10 people or fewer)** at seated rest: no population claims, no skin-tone regression. H2 may stay untested at this size.
- **No vascular age in years**, no diagnosis.
- **LED current is not melanin.** Dimming the LED lowers signal strength; it does not mimic pigment scattering. Low perfusion is not dark skin either.
- **Precision, not accuracy:** there is no ground truth for b/a here.
- **LED auto-gain is not new** (patents exist). What's new is *which metric dies first*.
- **No real-person data** is saved or analysed until the university ethics process is in place.

## Closest prior work

**MacIsaac 2025** (paper 9 in [03-papers.md](03-papers.md)) adjusted analog gain per skin tone, cut amplitude variability from 37.9% to 13.5%, and reports morphology is preserved. We test that at the second-derivative level: if it holds there too, H1 fails and we say so.
