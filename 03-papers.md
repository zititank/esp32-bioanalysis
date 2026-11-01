# 03 — Ground papers

Ten papers are enough. Read 1 to 3 first (what b/a is and how it gets detected), then the rest as questions come up. Most links are open access; pyPPG's code and docs are free on [GitHub](https://github.com/godamartonaron/GODA_pyPPG).

| # | Paper | What it discusses | Link |
|---|---|---|---|
| 1 | Takazawa 1998, *Hypertension* | Defines the second-derivative pulse waves (a to e) and the b/a ratio. They go on to convert it toward "vascular age"; we don't. | [doi:10.1161/01.hyp.32.2.365](https://doi.org/10.1161/01.hyp.32.2.365) |
| 2 | Elgendi 2012, fingertip PPG review | Basics of the fingertip pulse signal and its derivatives: what the waves are and how people analyse them. | [PMC3394104](https://pmc.ncbi.nlm.nih.gov/articles/PMC3394104/) |
| 3 | Elgendi 2014, *BioMed Eng OnLine* | Detecting the a and b waves in noisy second-derivative signals. Why a/b detection rate matters as much as b/a itself. | [doi:10.1186/1475-925X-13-139](https://doi.org/10.1186/1475-925X-13-139) |
| 4 | *Applied Sciences* 2020 | Wave detection collapses on dirty signals: the later waves (c, d, e) fail first, then b, then a. Clean signals give very low ratio variability. | [doi:10.3390/app10227977](https://doi.org/10.3390/app10227977) |
| 5 | Al-Halawani 2024, *Sci Rep* | Monte Carlo light model of a finger: melanin reduces red and IR pulse amplitude, red more than IR. Amplitude only, no derivatives. | [doi:10.1038/s41598-024-58435-7](https://doi.org/10.1038/s41598-024-58435-7) |
| 6 | Setchfield 2024, *J Biomed Opt* | Review of skin optical properties across skin types. Darker skin types scatter more beyond 600 nm (pooled, up to 48%). Motivates H2. Not a green-LED number. | [PMC10807857](https://pmc.ncbi.nlm.nih.gov/articles/PMC10807857/) |
| 7 | Yan 2017, *Biosensors* | Heart rate can be kept accurate across skin types with multi-wavelength gain control. Counter-evidence to "timing always fails with pigment", and why IR plus ECG is a fair timing control. | [doi:10.3390/bios7020022](https://doi.org/10.3390/bios7020022) |
| 8 | Fine 2021, review | Wearable PPG accuracy and skin tone. Green-light heart rate *can* fail on darker skin, which is why green is our stress test, not our clock. | [PMC8073123](https://pmc.ncbi.nlm.nih.gov/articles/PMC8073123/) |
| 9 | MacIsaac 2025, *Biosensors* | Per-skin-tone gain correction cuts amplitude variability; the paper reports morphology is preserved. Closest prior work: we test that claim at the second-derivative level. | [doi:10.3390/bios15070423](https://doi.org/10.3390/bios15070423) |
| 10 | Goda 2024, pyPPG, *Physiol Meas* | Open-source Python toolbox for PPG features, including finding the a to e waves. Use it instead of writing our own detector. | [doi:10.1088/1361-6579/ad33a2](https://doi.org/10.1088/1361-6579/ad33a2) |

## How they fit together

- **What we measure:** 1, 2.
- **Why detection can fail before the ratio does:** 3, 4.
- **Why skin and wavelength matter:** 5, 6, 8.
- **Counter-evidence we must respect:** 7 (timing can be equalised), 9 (shape may survive gain correction).
- **Tooling:** 10.

## Two things to keep straight while reading

- Most skin-tone papers are about **amplitude**, blood oxygen, or heart rate. None of them measure b/a against timing. Don't copy their correction factors onto b/a.
- Setchfield's numbers are about **tissue scattering**. Turning our LED down changes signal strength, not scattering. The two are different knobs.
