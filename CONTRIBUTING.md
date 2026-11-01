# Contributing

Thanks for helping with the ESP32 bioanalysis project.

## Before opening an issue or pull request

1. Read [README.md](README.md), [01-proposal.md](01-proposal.md), and the relevant
   build or literature notes.
2. Keep the fixed hypotheses and measurement definitions explicit. Do not
   rewrite them after looking at real traces.
3. Do not add raw human recordings, finger photos, identifiers, or other
   sensitive data to this public repository.
4. Prefer small pull requests with one clear change and a short validation note.

## Documentation and literature notes

- Put structured paper extractions in `docs/parsed/`.
- Put interpretation, limitations, and connections to this project in
  `docs/studied/`.
- Keep source links and citation details with every literature note.
- Mark uncertain claims and distinguish published findings from project
  hypotheses.

## Hardware, firmware, and analysis

Document pin changes, board variants, sampling rates, safety limits, and data
formats in the same pull request as the change. Never describe the project as a
clinical device or use it to make diagnostic, vascular-age, or SpO2 claims.
