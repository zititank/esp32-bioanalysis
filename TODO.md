# Project TODO

This list is the shared starting point for the project. Add an issue for any
task that needs discussion, then link it here when the scope is agreed.

## Project setup

- [ ] Agree on repository collaborators and review expectations.
- [ ] Choose a license for original documentation and future code.
- [ ] Add a short `CITATION.cff` once the project has a stable title and author list.

## Literature and study design

- [ ] Parse papers 1-3 from [03-papers.md](03-papers.md) into `docs/parsed/`.
- [ ] Write structured study notes for papers 1-3 in `docs/studied/`.
- [ ] Record the university ethics and data-management requirements before any human recording.
- [ ] Pre-register the hypotheses, quality gates, and exclusion rules before collecting data.

## Hardware and firmware

- [ ] Confirm the exact T-Energy ESP32-S3FN8 board and pinout.
- [ ] Acquire and bench-test the MAX30105, MAX30102, AD8232, protected 18650, and USB isolator.
- [ ] Create the firmware project with a reproducible build configuration.
- [ ] Verify both I²C buses and the MAX30105 green channel.
- [ ] Implement timestamped CSV output using the schema in [02-build-and-measure.md](02-build-and-measure.md).

## Analysis

- [ ] Implement ECG R-peak and heart-rate extraction.
- [ ] Implement pulse-arrival-time definitions and sensitivity checks.
- [ ] Use pyPPG or an equivalent documented method for a-to-e wave detection.
- [ ] Add synthetic tests for H1, H2, the null, and detector failure modes.
- [ ] Define the quality report and headline dropout figure before real recordings.

## Data safety

- [ ] Keep raw recordings outside this public repository.
- [ ] Use random session IDs and remove names, student IDs, phone numbers, and photos.
- [ ] Keep `HUMAN_RECORDING_ENABLED` disabled until ethics approval is documented.
