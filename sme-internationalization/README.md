# R&D Intensity & Firm Performance among European SMEs
### ExInt II: Research Designs in SME Research | WU Vienna | SS 2026

---

## Research Question

> Does R&D intensity negatively affect firm performance among European SMEs,
> and does firm size moderate this relationship?

## Theoretical Background

| Theory | Key claim | Implication for R&D–performance |
|--------|-----------|----------------------------------|
| Absorptive capacity (Cohen & Levinthal 1990) | R&D builds capacity to exploit external knowledge | R&D investment improves long-run performance |
| R&D expensing effect (Hall & Lerner 2010) | R&D is expensed immediately under IFRS | Short-run negative earnings effect; returns accrue with a lag |
| Resource-based view (Penrose 1959) | Firm-specific resources drive competitive advantage | Larger firms have more absorptive capacity to exploit R&D |
| SME innovation (Lu & Beamish 2001) | SMEs face resource constraints in R&D investment | Size moderates the R&D–performance relationship |

## Hypotheses

- **H1:** R&D intensity negatively affects RoA in the short run due to
  immediate expensing under IFRS.
- **H2:** Firm size positively moderates the R&D intensity–RoA relationship.


## Data

| Item | Detail |
|------|--------|
| Source | WRDS / Compustat Global |
| Table | comp_global_daily.g_funda |
| Downloaded | [date of download] |
| License | WRDS subscriber agreement |
| Currency | EUR only (curcd = 'EUR') |
| Sample | European SMEs (≤250 employees OR ≤€43m total assets) |
| Quality filters | at > 0.1, sale > 0, seq > 0 |
| Period | 2015–2024 |
| Unit of analysis | Firm-year |
| Raw rows | [from pull_metadata.txt] |
| Clean rows | [from data/processed/clean_log.txt] |

**Note on DOI variable:** `pifo` (foreign income) is not available in
Compustat Global. `rect/sale` (receivables/sales) was evaluated as a
DOI proxy but produced extreme outliers for SMEs with volatile sales.
The research question was updated to focus on R&D intensity, which has
100% coverage and stronger theoretical grounding for this sample.

## Key Variables

| Variable | Compustat field(s) | Formula | Role |
|----------|-------------------|---------|------|
| RoA | `ib`, `at` | `ib / at` | Dependent (Y) |
| R&D intensity | `xrd`, `at` | `xrd.fillna(0) / at` | Independent (X) |
| R&D × Size | — | `rd_intensity × ln_at` | H2 interaction |
| Firm size | `at` | `log(at)` | Moderator + Control |
| Leverage | `dltt`, `at` | `dltt / at` | Control |
| CAPX intensity | `capx`, `at` | `capx / at` | Control |
| Cash ratio | `che`, `at` | `che / at` | Control |

All continuous variables winsorized at 1st–99th percentiles.

## Main Results

| Model | β(R&D intensity) | β(R&D × Size) | Firm FE | Year FE |
|-------|-----------------|---------------|---------|---------|
| (1) Pooled OLS | -0.796*** | — | No | No |
| (2) TWFE | -0.535*** | — | Yes | Yes |
| (3) TWFE + H2 | -0.635*** | 0.038 (n.s.) | Yes | Yes |

**H1:** R&D intensity significantly reduces current-period RoA (β = -0.535,
p < 0.01). This is consistent with R&D expensing under IFRS — costs are
recognized immediately while performance returns accrue with a multi-year lag.
The OLS estimate (-0.796) is substantially larger in magnitude than the FE
estimate (-0.535), indicating substantial omitted variable bias when firm
fixed effects are omitted.

**H2:** The interaction between R&D intensity and firm size is positive but
not statistically significant (β = 0.038, p = 0.559). H2 is not supported.

## How to Reproduce

```bash
git clone https://github.com/vkiefner/sme-intl
cd sme-internationalization
python -m venv .venv
source .venv/bin/activate        # Mac/Linux
# .venv\Scripts\activate         # Windows
pip install -r requirements.txt
cp .env.example .env
# Add your WRDS username to .env
task all
quarto render research_note.md
```

## Project Structure

```
sme-internationalization/
├── data/
│   ├── raw/                   ← WRDS pull (not in Git)
│   └── processed/             ← clean panel (not in Git)
├── code/
│   ├── 01_pull_data.py        ← WRDS Compustat Global pull
│   ├── 02_clean.py            ← EUR filter, SME filter, quality filters
│   ├── 03_descriptives.py     ← variable construction, summary stats, figures
│   └── 04_regression.py       ← panel FE regressions
├── output/
│   ├── tables/                ← summary_statistics.csv, regression_results.csv
│   └── figures/               ← correlation_matrix.png, main_relationship.png
├── references/
│   └── library.bib            ← Zotero auto-export (Better BibTeX)
├── research_note.md           ← Quarto → PDF
├── Taskfile.yml
├── pyproject.toml
├── requirements.txt
├── .env.example
├── .gitignore
└── README.md
```

## References

Cohen, W. M., & Levinthal, D. A. (1990). Absorptive capacity: A new
perspective on learning and innovation. *Administrative Science Quarterly*,
35(1), 128–152.

Hall, B. H., & Lerner, J. (2010). The financing of R&D and innovation.
In B. H. Hall & N. Rosenberg (Eds.), *Handbook of the Economics of
Innovation* (Vol. 1, pp. 609–639). Elsevier.

Lu, J. W., & Beamish, P. W. (2001). The internationalization and performance
of SMEs. *Strategic Management Journal*, 22(6–7), 565–586.

Penrose, E. T. (1959). *The theory of the growth of the firm*. Oxford
University Press.

Trisovic, A., et al. (2022). A large-scale study on research code quality
and execution. *Scientific Data*, 9(1), 60.
