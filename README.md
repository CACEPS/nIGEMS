# nIGEMS — Replication Package (v40)

Replication code and data for **"Governance architecture shapes the robustness of global carbon budget allocation"**

IGEMS (Integrated Global Emissions Management System) is a governance-allocation simulator that isolates the **governance channel** — the sequence and principle by which the carbon budget is divided — while holding the physical budget, decarbonization rate, and growth identical across four allocation mechanisms (market/grandfathering, equity/CBDR+RC, capacity, hybrid cooperative), nine macro-regions, fifty years.

This package updates the v36 bundle to **v40**, adding the endogenous strategic-compliance game (Note S7) and the historical back-test (Note S8).

---

## Structure

```
nIGEMS_v40/
  gams/        self-contained GAMS models (all data embedded)
  src/         Python reproduction scripts
  data/        input and reference data (+ data/backtest/ observed data)
  results/     reference figures and expected outputs
  future_empirical_validation/   prospective extension (not a paper result)
  requirements.txt
```

## What each component reproduces

**GAMS (all-in-one models; data embedded, no external inputs required)**
- `gams/IGEMS_complete_v40.gms` — the complete IGEMS model: the four allocation mechanisms, the corridor/compliance metric, and the headline allocations and compliance rates. This is the single all-in-one model file.
- `gams/IGEMS_RICE_coupling_v40.gms` — the IGEMS–RICE minimum coupling (§4.8, Table 9, Figure of the cost-effectiveness reordering).

**Python (run from the package root)**
- `src/igems_optionB.py`, `src/igems_optionC.py` — binary corridor and continuous-adherence compliance metrics (Table 6, core compliance results). Self-contained.
- `src/igems_altcompliance_v40.py` — ranking robustness to the **form** of the compliance function (Note S2 / Table S8). Self-contained.
- `src/igems_extended_analyses_v40.py` — Monte Carlo ensemble (Table 8), six-metric robustness (Note S4), and related sensitivity analyses. Self-contained.
- `src/igems_iam_supplement.py` — IAM coupling, λ-sweep, and the cost-effectiveness reordering (Table 9). Self-contained.
- `src/igems_strategic_compliance.py`, `src/reproduce_S7.py`, `src/make_figure.py` — **endogenous strategic-participation game** (Note S7, Tables S7.1–S7.3). Reads `data/data_baseline_regions.csv` and `data/data_allocations.csv`.
- `src/igems_historical_backtest.py` — **historical back-test** against observed NDC ambition and emissions trajectories (Note S8). Reads `data/backtest/*.csv`.

## Quick start

```bash
pip install -r requirements.txt

# Endogenous strategic compliance (Note S7)
python src/reproduce_S7.py

# Historical back-test (Note S8)
python src/igems_historical_backtest.py --data data/backtest

# Compliance-function robustness (Note S2 / Table S8)
python src/igems_altcompliance_v40.py

# Monte Carlo robustness and extended analyses (Table 8, Note S4)
python src/igems_extended_analyses_v40.py
```

The GAMS models require a GAMS installation; the Python scripts require only the packages in `requirements.txt`.

## Expected results (spot checks)

- **Strategic game (Note S7):** ranking by delivered abatement is `equity > hybrid > capacity > market`; the market rule is the cooperative optimum in fewer than 6% of grid parameterisations.
- **Back-test (Note S8):** on observed emissions shares the grandfathering/market rule fits best (RMSE ≈ 0.045 — partly true by construction); on NDC ambition no rule fits well (RMSE ≈ 0.35).
- **Compliance-function robustness (Note S2):** under the main-text aggregation the market rule is least reachable and the hybrid is the most robust across geometric, logistic, and linear compliance functions.

## Data provenance

The historical back-test (Note S8) uses observed emissions and NDC targets for **15 countries with complete, reliable public records**: USA, CAN, MEX, CHN, IND, DEU, FRA, GBR, BRA, IDN, JPN, SAU, ZAF, NGA, PAK (JPN is excluded from the NDC test, leaving 14). NDC targets were compiled from the **UNFCCC NDC Registry**; emissions from the **Global Carbon Project**.

A further 59 countries lack complete, reliable NDC or emissions records in these public sources — a well-documented data gap recognised by the UNFCCC, the World Bank, and national-inventory reporting. In the raw working file these countries were represented by placeholder rows; they were never included in any reported analysis, and the script restricts the back-test to the 15 real-data countries by design. To avoid any ambiguity in a public repository, the placeholder rows have been removed here, so this package distributes only genuine observed data.

`future_empirical_validation/` contains a prospective behavioural-law script. It is exploratory future work, **not** a result reported in the manuscript, and requires the user's own observed panel data to run.

## Repository

github.com/caceps/nIGEMS (made public on acceptance; a read-only copy is available to editors on request). An archived release with a persistent DOI will be minted at acceptance.
