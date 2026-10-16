# mAb19-LS HBsAg kinetic modeling

Code for the HBsAg kinetic modeling in Wang, Tenuta, Le, Svensgaard et al., *"Anti-HBsAg antibody mAb19-LS enhances antiviral immunity in humans with chronic hepatitis B"* (J Exp Med, under revision).

Mechanistic models of HBsAg kinetics are fit to longitudinal HBsAg data from the 20 participants in the virologic analysis (15 mAb19-LS, 5 placebo) by population nonlinear mixed-effects modeling in Monolix. The measured mAb19-LS concentration enters each model as a linearly interpolated time-varying regressor. A Python notebook then reads the Monolix output and computes HBsAg half-lives and the fold increase in clearance.

## Model

The model in the paper is `mlx-satAfinalmore4` (`model-VDsaturateA.txt`):

dS/dt = Q − γS − ψ ε_A(t) S,  ε_A(t) = 1 / (1 + (A₅₀/A(t))^h_A)

| Paper | Code | Notes |
|---|---|---|
| S | `S` | HBsAg (IU/mL), observed as `HBsAg` |
| A(t) | `At` | mAb19-LS (µg/mL), regressor `mAb19` |
| Q | `Dp` | production; S(0) = Q/γ |
| γ | `g` | natural clearance (/day), logit-normal on 0.3–0.8 |
| ψ | `psi` | maximal mAb-induced clearance (/day) |
| A₅₀ | `Ax` | mAb19-LS concentration for half-maximal effect |
| h_A | `hA` | Hill coefficient, fixed at 10 |
|  | `b` | proportional error, fixed at 0.1 |

## Files

- `HBsAg_mAb19_long.xlsx`: data, one row per participant and visit (`participant, time` in days, `HBsAg, mAb19, dose` in mg/kg, `mAb19_cens`).
- `model-*.txt`: Mlxtran structural models.
  - `VDsimple`: linear mAb effect.
  - `VDsaturateA`: saturating mAb effect; this is the paper model.
  - `VDsaturateAS`, `VDsaturateAS2`: additional saturation in S.
  - `epsform`, `vax`: exploratory alternatives with no fitted run here.
- `mlx-*.mlxtran`: Monolix projects. Each `mlx-*/` folder is that project's exported results (Monolix 2024R1).
- `analysisRevision-kiro.ipynb`: the analysis. Its steps:
  - model comparison (−2LL, AIC, BICc);
  - population and individual parameters;
  - detection of the post-infusion equilibrium (drop of more than 1 log10 from baseline, then flat within 0.5 log10 for at least 2 visits);
  - baseline and equilibrium half-lives, ln2/(γ + ψ ε_A), and fold change in clearance;
  - per-person uncertainty from Monolix's conditional-distribution draws;
  - forest plot, half-life over time, PK and fit panels, and observed vs predicted plots.

## Running

1. Optional: re-fit with Monolix 2024R1 by opening any `mlx-*.mlxtran` and running all tasks. The results are already included.
2. Run the notebook from this folder; it uses relative paths and writes figures to `figures/`. It needs Python 3 with numpy, pandas, scipy, matplotlib, seaborn and openpyxl. It was tested with Python 3.11, numpy 1.24, pandas 1.5, scipy 1.11, matplotlib 3.7 and seaborn 0.13.

## Contact

Daniel B. Reeves, Fred Hutchinson Cancer Center.
