# Thermal coupling and simultaneous multi-task reservoir computing in VO₂ neuristor arrays

Simulation scripts, result data and figure scripts for the paper

> [Authors], *Thermal coupling and simultaneous multi-task reservoir computing in VO₂ neuristor arrays*, Neuromorphic Computing and Engineering (submitted).

A 28 × 28 array of thermally coupled VO₂ neuristors is used as a physical reservoir. Each Colored MNIST image is encoded as one input voltage per pixel. From the same 15,680 features (784 neurons × 20 time bins of 0.5 µs), three readouts are trained at the same time: digit (10 classes), colour (10 classes) and parity (2 classes). The reservoir itself is never trained.

Archived version: `[Zenodo DOI, to be added at submission]`

---

## Main results (test set, 10,000 images)

| Result | Digit | Colour | Parity | Data key in `results/paper1_data.json` |
|---|---|---|---|---|
| Coupled array, σ = 0, ridge, 60k | 91.38 % | 81.48 % | 93.43 % | `sigma0_60k_final` |
| Coupled array, σ = 0, logistic readout (colour) | – | 90.45 % | – | `logistic_sigma0_60k` |
| Coupled array, σ = 2×10⁻⁴, 3 noise seeds, ridge | 87.37 ± 0.28 % | 78.19 ± 0.47 % | 90.00 ± 0.26 % | `baselines_60k.reservoir_sigma2e-4_*` |
| Uncoupled array (c = 0), σ = 0, ridge, 60k | 86.87 % | 96.24 % | 89.34 % | `uncoupled_60k_ridge_logistic` |
| Uncoupled array, logistic readout (colour) | – | 96.11 % | – | `uncoupled_60k_ridge_logistic` |
| Per-pixel sigmoid baseline, ridge / logistic (colour) | – | 88.28 % / 92.19 % | – | `baselines_60k`, `baseline_D_logistic_and_20k` |

All numbers in the paper are read from `results/paper1_data.json`. Each entry in that file records its source.

## Repository layout

```
experiments/
  reconstructed/   R1–R6: reconstructions of lost notebooks (see below)
  kaggle/          scripts run in their current form
figures/
  scripts/         make_fig*.py + iop_style.py (shared figure style)
  pdf/             final figures as submitted
results/
  paper1_data.json all numbers used in the paper, with provenance
src/
  README.md        where to get model.py (VO₂ neuristor model of Zhang et al.)
```

## Which script produces which result

| Script | Produces | Data key | Protocol |
|---|---|---|---|
| `experiments/reconstructed/R1_final_sigma0_60k.py` | main result, all time windows, sliding windows, confusion matrices | `sigma0_60k_final` | 60k, σ = 0 |
| `experiments/reconstructed/R2_noise_seeds_sigma2e-4.py` | three noise seeds at σ = 2×10⁻⁴ | `baselines_60k.reservoir_sigma2e-4_*` | 60k |
| `experiments/reconstructed/R3_baselines_ABD.py` | raw-input, random-tanh and per-pixel sigmoid baselines (ridge) | `baselines_60k` | 60k |
| `experiments/reconstructed/R4_fig2_sim.py` | switching simulation `fig2_sim.npz` (Figs 1 and 3) | `switching_sigma0` | 1,000 test images |
| `experiments/reconstructed/R5_diagnostics_dt.py` | switching vs noise for dt = 10 and 5 ns (Fig. 5d) | `diagnostics_dt` | 1,024 test images |
| `experiments/reconstructed/R6_jitter.py` | switch-time jitter across noise realisations | `jitter_sigma2e-4` | 32 test images × 8 realisations |
| `experiments/kaggle/logistic_sigma0_kaggle.py` | logistic colour readout, coupled array and raw input | `logistic_sigma0_60k` | 60k |
| `experiments/kaggle/logistic_uncoupled_kaggle.py` | uncoupled array, ridge and logistic readout | `uncoupled_60k_ridge_logistic` | 60k |
| `experiments/kaggle/logistic_D_kaggle.py` | per-pixel sigmoid baseline: ridge 60k and 20k, logistic colour | `baseline_D_logistic_and_20k` | 60k / 20k |
| `experiments/kaggle/logistic_D_Cext_kaggle.py` | same baseline, logistic with C ∈ {0.1, 1, 10} | `baseline_D_logistic_and_20k.logistic_60k_C_extension` | 60k |
| `experiments/kaggle/c0_sigma_grid_kaggle.py` | C₀ × σ grid (Fig. 5a–c) | `c0_sigma_grid_20k` | 20k |
| `experiments/kaggle/coupling0_kaggle.py` | uncoupled control at σ = 0 and 2×10⁻⁴, S_env control, image-78 snapshots | `coupling_control_20k` | 20k |
| `experiments/kaggle/coupling_sweep_kaggle.py` | coupling sweep c = 0.005–0.04 (Fig. 6a) | `coupling_sweep_20k` | 20k |
| `experiments/kaggle/color_independence_kaggle.py` | digit–colour and parity–colour independence of the labels | `colour_label_independence` | labels only |
| `experiments/kaggle/rt_loop_kaggle.py` | R–T hysteresis of the device model (Fig. A1), `rt_loop.json` | `vo2_R_325K` (check value) | one element |
| `experiments/kaggle/T_subthreshold_mechanism_60k.py` | frozen-temperature control, sub-threshold window (0–2 µs) | `subthreshold_mechanism_60k` | 60k, σ = 0 |
| `experiments/kaggle/T_sub_precision_60k.py` | frozen-temperature control in float32 and float64 | `sub_precision_60k` | 60k, σ = 0 |
| `experiments/kaggle/T_c0_noise_60k.py` | C₀ × σ check in the 60k protocol | `c0_noise_60k` | 60k |

**Protocols.** *60k*: 60,000 training images (50,000 fit / 10,000 validation), 10,000 test images. *20k*: the first 20,000 training images (16,000 fit / 4,000 validation), the same 10,000 test images. In both, features are standardised with fit-split statistics, near-constant features are masked, the ridge parameter α is chosen per task on validation from 10⁻⁶ to 10⁶ (steps of 0.5 decade), and the readout is refit on all training images.

**Model settings (all runs).** Explicit Euler, dt = 10 ns, t_max = 10 µs; C₀ = 145.35 pF; R₀ = 12 kΩ; thermal coupling c = 0.02; substrate heat loss S_env = 0.92 S_th, held fixed when c is changed; T_base = 325 K; input range 10.5–12.2 V, V = 10.5 + 1.7·L·pixel with L = 0.299R + 0.587G + 0.114B. Colour labels: `np.random.seed(42)`, then `randint(0, 10, 60000)` (train) and `randint(0, 10, 10000)` (test).

## About the reconstructed scripts (R1–R6)

The original notebooks for these six experiments were lost. R1–R6 were rewritten from the surviving parts of the original scripts. They share one simulation and readout core, taken from `c0_sigma_grid_kaggle.py`. Each script compares its output with `results/paper1_data.json` and prints `PASS` or `DIFFERENT`.

All six were run in full and checked (`paper1_data.json → reconstruction_checks`):

- **Deterministic results** (R1, R3, R4) reproduce the paper values to within 0.02 percentage points, or one test image.
- **Noisy results** (R2, R5, R6) reproduce the paper values within the seed-to-seed spread. Individual noise realisations are not bit-identical to the originals.

The paper keeps the original values.

## How to run

The scripts were written as single Kaggle notebook cells.

1. Create a Kaggle notebook with a **T4 GPU** and **Internet ON** (MNIST is downloaded by torchvision).
2. Add `model.py` (see `src/README.md`) and `results/paper1_data.json` as notebook inputs. The scripts find both files anywhere under `/kaggle/input`.
3. Paste one script into a cell. Run it first with `SMOKE = True` (a few minutes, small subset). Then set `SMOKE = False` and use *Save & Run All*.
4. Outputs are written to `/kaggle/working/<experiment>/`.

`logistic_D_kaggle.py`, `logistic_D_Cext_kaggle.py` and `color_independence_kaggle.py` need no `model.py` and no GPU.

Typical full-run times on a T4 are given in each script header (from ~1 min to ~5 h).

## Figures

```
python figures/scripts/make_fig1.py         fig2_sim.npz OUTDIR
python figures/scripts/make_fig2_baselines.py results/paper1_data.json OUTDIR
python figures/scripts/make_fig3.py         results/paper1_data.json fig2_sim.npz snapshots_img78.npz OUTDIR
python figures/scripts/make_fig4.py         results/paper1_data.json OUTDIR
python figures/scripts/make_fig5.py         results/paper1_data.json OUTDIR
python figures/scripts/make_fig6.py         results/paper1_data.json OUTDIR
python figures/scripts/make_figA1.py        rt_loop.json OUTDIR
```

`fig2_sim.npz` comes from R4, `snapshots_img78.npz` from `coupling0_kaggle.py`, and `rt_loop.json` from `rt_loop_kaggle.py`.

## Requirements

Python 3 with the packages in `requirements.txt`. The simulations were run on Kaggle (Tesla T4).

## Citation

Please cite the paper above. Citation metadata is in `CITATION.cff`.

The VO₂ neuristor model is that of Zhang et al., *Nat. Commun.* **15**, 6986 (2024). Please cite it as well when you use `model.py`.

## License

The scripts in this repository are released under the MIT License (see `LICENSE`). `model.py` is not part of this repository and keeps the license of its authors.
