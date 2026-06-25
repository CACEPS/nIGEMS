# nIGEMS
# Governance architecture shapes the equitable allocation of the global carbon budget
*Abul Quasem Al-Amin & Naomi Oreskes (2026)*
# nIGEMS — Reproduction repository (version 36)

Code and data to reproduce *Governance architecture shapes the equitable allocation of the global carbon budget* (manuscript `IGEMS_N_CC_v36_AlAmin.docx`). The repository is named **nIGEMS** — the "n" denotes the Nature submission — formerly IGEMS.

Everything here is at a single consistent version (v36). The model uses one allocation convention throughout: each base mechanism (market, equity, capacity) is normalised to the 10 Gt budget, the hybrid is the 40:35:25 blend of those normalised vectors renormalised to 10 Gt, so every allocation sums to 10 Gt.

## What reproduces what

| Manuscript element | File |
| --- | --- |
| Tables 4, 5, 6, 8; Notes S1–S6; Solidarity Fund | `IGEMS_complete_v36.gms` |
| Table 9, Figure 11 (RICE coupling) | `IGEMS_RICE_coupling_v36.gms` |
| Baseline inputs (Table 2) | `data_baseline_regions.csv` |
| Raw base allocation vectors | `data_base_allocations.csv` |
| Table 5 allocations, cuts, Justice Index | `data_table5_allocations.csv` |
| Per-mechanism outcomes (compliance, cost, regret, per-capita) | `model_outcomes_v36.csv` |
| Solidarity Fund derivation | `model_fund_derivation.csv` |
| Coupling headline (CPP vs CPD) | `coupling_table9.csv` |
| λ-sweep and flip thresholds | `coupling_lambda_sweep.csv` |
| Coupling sensitivity grid | `coupling_sensitivity_grid.csv` |
| Figure 11 image | `Figure11_coupling.png` |

## GAMS (two files; both v36)

- **`IGEMS_complete_v36.gms`** — the all-in-one IGEMS model: data, allocations, capacity-graded feasible rates, trajectory-convergence compliance, Gini/Justice Index, stylised MAC cost layer, Monte Carlo, Nash NLP, acceptance, six-metric robustness, two-bloc, stylised TCRE climate, structural sensitivity, Solidarity Fund, and the per-capita / equity-cut reconciliation.
  - `gams IGEMS_complete_v36.gms` (deterministic) · `--STOCH=1` · `--STRUCT=1` · `--OPTIM=1`
- **`IGEMS_RICE_coupling_v36.gms`** — companion one-way soft-link. Each IGEMS allocation enters a RICE/DICE-form regional cost module as an exogenous emission-cap glide path; the module prices the caps and never re-optimises the allocation. Damages off; SSP2-4.5 baseline; no-trade headline.
  - `gams IGEMS_RICE_coupling_v36.gms` · `--SOLVE=1` (optional NLP cross-check)

The coupling is a deliberately separate model (RICE, not IGEMS): it shares the same nine regions and four allocation vectors but is a distinct cost module. They are kept as two files because GAMS identifiers are case-insensitive and the two models reuse several names (`Cost`/`cost`, `dmax`, the time set) with different meanings; merging into one literal file would require renaming working code. Run them in sequence.

## Python runners (numpy, pandas)

- `igems_optionB.py` — trajectory-convergence compliance + Monte Carlo
- `igems_optionC.py` — acceptance-weighted compliance (Note S2)
- `igems_iam_supplement.py` — stylised MAC cost layer (Note S1)
- `igems_extended_analyses_v36.py` — six-metric robustness, structural sensitivity, stylised TCRE climate, two-bloc (Notes S3–S5)

## Headline numbers (v36)

- Compliance: binary market 0.24 vs 0.46 others; Monte Carlo mean hybrid 0.54 vs market 0.46; P(high) 14% vs 4%; continuous hybrid 0.94; cross-metric regret hybrid 0.045.
- Per-capita ratio: 11.1 (market) → 2.6 (hybrid). Parity benchmark ~1.0 t/person.
- Equity rule: development surplus only for Sub-Saharan Africa; India ~33%, South Asia ~30% cuts.
- Solidarity Fund: ~$1.0–2.3T/yr (2.36 Gt × IPCC AR6 1.5 °C price), corroborated by IHLEG.
- RICE coupling: cost per planned tonne — market cheapest (100), hybrid 110, equity 129, capacity 133. Cost per expected-delivered tonne (λ=1) — market most expensive (100), hybrid 90, equity 58. Flip at λ* ≈ 0.84 (hybrid), 0.81 (equity); feasible at finite prices ($214–252/t); robust across SSP1/2/5, backstop, MAC slope, discount rate, CDR cap (λ* 0.76–0.94).

## Reproduction notes

Deterministic, Gini, MAC, six-metric, fund, per-capita, and the full coupling (CPP, CPD, λ*) results are closed-form and match exactly across GAMS, Python, and the CSVs. Monte Carlo / climate / structural modules use random draws; GAMS uses native RNG (`execseed` fixed), so interval and probability values are close to, not bit-identical to, the Python runners. Means and rankings agree.

## External data sources (author-verified)

Global Carbon Project / Our World in Data (baseline); IPCC AR6 WGIII 2022 (carbon prices); UNEP Emissions Gap Report 2025 (ambition gap); IHLEG / Songwe, Stern & Bhattacharya 2022 (finance need); IMF 2023 (subsidies); Riahi et al. 2017, van Vuuren et al. 2022 (cost-optimal SSP allocations).

## Compliance-function robustness (Note S2 / Table S8)
- `igems_altcompliance_v36.py` — applies three deliberately different compliance
  functions (geometric binary corridor [primary], logistic acceptance, linear
  implementation difficulty) to identical capacity-graded effort and allocations.
  Under the main-text unweighted region-year aggregation the ranking is preserved
  across all three forms (market least reachable 93-100% of MC draws; hybrid most
  robust 58-68%; hybrid best or tied-best deterministically under every form), so the headline ordering is not an artefact
  of the corridor's binary shape. The equity-vs-hybrid metric-dependence of Note S2 is
  shown to be an aggregation effect (population weighting), not a functional-form effect.
