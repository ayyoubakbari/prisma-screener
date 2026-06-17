# Changelog

## [1.0.0] — 2026-06-17

### Added
- Abstract screening from `.bib`, `.csv`, `.xlsx`, and `.xls` files
- PICO framework support (Population, Intervention, Comparison, Outcome)
- Custom inclusion/exclusion criteria on top of PICO
- Three-way classification: INCLUDE / EXCLUDE / MAYBE
- Confidence score (0–100) per paper
- Plain-language one-sentence reason per decision
- Criteria flags (which criteria were met or missed)
- Batch processing with configurable batch size and delay
- Resume support (`--resume`) for interrupted runs
- Auto-generated plain-text summary report
- `--make-criteria` flag to generate a blank criteria template
- `--make-example` flag to generate a filled example criteria file
- Direct compatibility with doi2bib `.bib` output
- CC BY 4.0 license
