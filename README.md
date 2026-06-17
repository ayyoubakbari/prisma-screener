# prisma-screener

**AI-powered abstract screener for systematic reviews.**

Screen hundreds of abstracts against your PICO or custom inclusion/exclusion
criteria in minutes — not days. Each paper is classified as **INCLUDE**,
**EXCLUDE**, or **MAYBE**, with a plain-language reason and confidence score,
ready to import into your PRISMA flow chart.

---

<p align="center">
  <img src="https://img.shields.io/badge/python-3.9%2B-blue?style=flat-square" alt="Python 3.9+">
  <img src="https://img.shields.io/badge/license-CC%20BY%204.0-green?style=flat-square" alt="CC BY 4.0">
  <img src="https://img.shields.io/badge/AI-Claude%20Sonnet-orange?style=flat-square" alt="Claude Sonnet">
  <img src="https://img.shields.io/badge/input-.bib%20%7C%20.csv%20%7C%20.xlsx-lightgrey?style=flat-square" alt="Input formats">
  <img src="https://img.shields.io/badge/companion-doi2bib-purple?style=flat-square" alt="doi2bib">
</p>

---

## Why prisma-screener?

Manual abstract screening is the most time-consuming stage of any systematic
review. A single reviewer screening 500 abstracts takes 8–15 hours. With
prisma-screener, the same task takes under 10 minutes, producing a structured
CSV you can audit, filter, and import directly into tools like Rayyan,
Covidence, or Excel.

**Designed to work directly with [doi2bib](https://github.com/ayyoubakbari/doi2bib):**
use doi2bib to convert your DOI list to a `.bib` file with abstracts, then
feed that `.bib` straight into prisma-screener.

---

## Features

| Feature | Detail |
|---|---|
| **Three input formats** | `.bib` (from doi2bib), `.csv`, `.xlsx` |
| **PICO framework** | Population, Intervention, Comparison, Outcome — the standard for clinical and engineering reviews |
| **Custom criteria** | Add any inclusion/exclusion rules on top of PICO |
| **Three-way classification** | INCLUDE / EXCLUDE / MAYBE with confidence score |
| **Plain-language reasons** | One-sentence explanation per paper |
| **Resume support** | Continue an interrupted run without re-screening done papers |
| **Summary report** | Auto-generated `.txt` summary listing all included and maybe papers |

---

## Installation

```bash
git clone https://github.com/ayyoubakbari/prisma-screener.git
cd prisma-screener
pip install -r requirements.txt
```

You also need a free **Anthropic API key**:
1. Sign up at [console.anthropic.com](https://console.anthropic.com)
2. Create an API key
3. Set it as an environment variable:

```bash
# macOS / Linux
export ANTHROPIC_API_KEY="sk-ant-..."

# Windows
set ANTHROPIC_API_KEY=sk-ant-...
```

---

## Quick start

**Step 1 — Generate a criteria file**

```bash
python prisma_screener.py --make-criteria
# Creates: criteria_template.json
```

Open `criteria_template.json` and fill in your PICO elements and any
additional inclusion/exclusion rules. See `criteria_example.json` for a
filled example.

**Step 2 — Run the screener**

```bash
python prisma_screener.py \
  --input   refs.bib \
  --criteria criteria_template.json \
  --output  screened_results.csv
```

**Step 3 — Review the output**

Open `screened_results.csv` in Excel or import into Rayyan/Covidence.
Read `screened_results_summary.txt` for a human-readable summary.

---

## Usage

```
python prisma_screener.py [OPTIONS]

Options:
  --input        PATH     Input file (.bib / .csv / .xlsx / .xls)   (required)
  --criteria     PATH     JSON criteria file                          (required)
  --output       PATH     Output CSV filename   (default: screened_results.csv)
  --api-key      TEXT     Anthropic API key (or set ANTHROPIC_API_KEY)
  --batch        INT      Abstracts per API call (default: 5)
  --delay        FLOAT    Seconds between API calls (default: 1.0)
  --resume               Resume from partial output file
  --make-criteria        Write a blank criteria template and exit
  --make-example         Write a filled example criteria file and exit
  --version              Show version and exit
```

---

## Criteria file format

The criteria file is a `.json` file. Set `"mode"` to `"pico"` (recommended)
or `"custom"` (free-form criteria only).

```json
{
  "mode": "pico",

  "pico": {
    "population":   "Adults 18+ with type 2 diabetes",
    "intervention": "Aerobic or resistance exercise",
    "comparison":   "Sedentary control group",
    "outcome":      "HbA1c, fasting glucose, BMI"
  },

  "custom": {
    "inclusion": [
      "Randomised controlled trials only",
      "Follow-up period at least 8 weeks"
    ],
    "exclusion": [
      "Studies on type 1 diabetes only",
      "Fewer than 20 participants"
    ]
  },

  "study_designs": ["RCT", "meta-analysis"],
  "language": "English",
  "date_range": "2010-2024"
}
```

All fields except `"mode"` are optional — use only what applies to your review.

---

## Output format

### `screened_results.csv`

| Column | Description |
|---|---|
| `ID` | Cite key or row identifier |
| `Title` | Paper title |
| `Authors` | Author list |
| `Year` | Publication year |
| `Journal` | Journal or conference name |
| `DOI` | Digital Object Identifier |
| `Abstract` | Full abstract text |
| `Decision` | `INCLUDE` / `EXCLUDE` / `MAYBE` |
| `Confidence` | 0–100 score |
| `Reason` | One-sentence plain-English justification |
| `Flags` | Which criteria were met or missed |

### `screened_results_summary.txt`

A human-readable report listing all INCLUDE and MAYBE papers with reasons,
suitable for pasting into your PRISMA documentation.

---

## Typical workflow with doi2bib

```
Your DOI list (.csv / .xlsx)
        │
        ▼
  doi2bib.py ──► references.bib   (with abstracts)
        │
        ▼
  prisma_screener.py ──► screened_results.csv
                    └──► screened_results_summary.txt
        │
        ▼
  Manual full-text review of INCLUDE + MAYBE papers
        │
        ▼
  PRISMA flow diagram
```

---

## Performance and cost

| Papers | Estimated time | Approx. API cost* |
|---|---|---|
| 100 | ~2 min | ~$0.05 |
| 500 | ~8 min | ~$0.25 |
| 1 000 | ~15 min | ~$0.50 |
| 5 000 | ~60 min | ~$2.50 |

*Approximate using Claude Sonnet pricing. Actual cost depends on abstract
length and batch size. Use `--batch 10` to reduce API calls and cost.

---

## Tips

**No abstract?** Papers without abstracts are classified as MAYBE with a flag.
Use doi2bib first to maximise abstract coverage.

**Interrupted run?** Use `--resume` to continue without repeating already
screened papers.

**Reduce cost?** Increase `--batch` to 10 (maximum recommended) and set
`--delay 0.5`.

**Two reviewers?** Run twice with different `--output` filenames and compare.
Discrepancies (one INCLUDE, one EXCLUDE) should go to consensus.

---

## Citing this tool

```bibtex
@software{AkbariMoghanjoughi2026prisma,
  author       = {Akbari-Moghanjoughi, A.},
  title        = {{prisma-screener: AI-Powered Abstract Screener for Systematic Reviews}},
  year         = {2026},
  institution  = {Universitat Polit{\`e}cnica de Catalunya (UPC)},
  url          = {https://github.com/ayyoubakbari/prisma-screener},
  license      = {CC BY 4.0}
}
```

---

## Related tool

**[doi2bib](https://github.com/ayyoubakbari/doi2bib)** — convert a spreadsheet
of DOIs to a fully populated BibTeX file with abstracts. Use it upstream of
prisma-screener to enrich your reference list before screening.

---

## License

[Creative Commons Attribution 4.0 International (CC BY 4.0)](LICENSE)

**Author:** A. Akbari-Moghanjoughi
**Affiliation:** Universitat Politècnica de Catalunya (UPC)
