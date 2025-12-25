# Kalman Filtering Experiments for Multi-Floor Shear Models

This repository collects Jupyter notebooks that compare multiple Kalman filtering strategies on Gaussian shear-frame building models. The notebooks sweep through single- and multi-floor observation scenarios, linear and nonlinear measurement models, and small (3 floor) versus larger (10 floor) structures. Each experiment saves the generated ensembles, probability density functions (PDFs), and plots so you can reproduce or extend the studies.

## Repository Layout

| Path | Description |
| --- | --- |
| `KF/` | Classic Kalman Filter notebooks (`kalman_singlefloor.ipynb`, `Kalman_update_2floor.ipynb`) plus stored priors/updates (`pdf_prior_2data.npz`, `pdf_update_*.npz`) and the reference plot `KF_floor3_1observed.pdf`. |
| `EnkF/` | Ensemble Kalman Filter studies (`EnKF_singlefloor.ipynb`, `EnkF_multiplefloors.ipynb`, `EnkF_multiplefloors_nonlinear.ipynb`, `check.ipynb`), supporting ensembles (`enkf_prior_*`, `enkf_update_*.npz`), and comparison figures in `matched/`. |
| `PCKF/` | Polynomial Chaos Kalman Filter notebooks for linear and nonlinear observation models plus extensive PDF exports (e.g., `PCKF_floor*_2data_nonlinear_{10k,100k,1mill,10mill}.pdf`). The `matched/` folder contains overlays used to verify agreement with EnKF/KF. |
| `example_10dof/` | Ten-degree-of-freedom examples for both EnKF and PCKF (standard and averaged-measurement variants) along with linear/nonlinear comparison data in `matched/linear` and `matched/nonlinear`. |

Supporting artifacts such as `sample.txt`, `data_sample.txt`, and `pckf_prior_alpha.npz` capture the raw ensembles and chaos coefficients that feed the update steps.

## Requirements

- Python 3.9+  
- [JupyterLab](https://jupyter.org) or Jupyter Notebook  
- NumPy  
- SciPy (for `scipy.stats`, `scipy.special`)  
- Matplotlib

Install dependencies into a virtual environment to keep the workspace isolated:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install jupyterlab numpy scipy matplotlib
```

## Getting Started

1. Activate the virtual environment and launch Jupyter:
   ```bash
   source .venv/bin/activate
   jupyter lab
   ```
2. Open any notebook in the folder of interest (`KF/`, `EnkF/`, `PCKF/`, or `example_10dof/`).
3. Run the cells from top to bottom. Most notebooks expose key scenario parameters near the top:
   - `floors` selects the structural dimension (3 or 10).
   - `observed` chooses how many floors are instrumented (e.g., 1, 2, 3, or 5 averaged floors for 10-DOF studies).
   - `nsamp` (or `npc`) controls ensemble size/Polynomial Chaos order.
   - `Gamma`, `noisevar`, and `epsilon` specify measurement noise assumptions.

## Running the Experiments

### KF (`KF/`)
- `kalman_singlefloor.ipynb` reconstructs a single observed floor in the 3-floor model and analytically derives covariance matrices for validation.
- `Kalman_update_2floor.ipynb` expands to multiple observed floors and writes PDFs to `pdf_update_single.npz`, `pdf_update_2data.npz`, and `pdf_update_multiple.npz` for downstream comparison.
- Use the exported `KF_floor3_1observed.pdf` plot or regenerate it by re-running the notebook; PDFs are stored with a consistent naming scheme so EnKF/PCKF notebooks can load them.

### Ensemble Kalman Filter (`EnkF/`)
- `EnKF_singlefloor.ipynb` and `EnkF_multiplefloors.ipynb` follow the same structural model but rely on Monte Carlo ensembles; they output the priors/updates as `enkf_prior_*.npz` and `enkf_update_*.npz`.
- `EnkF_multiplefloors_nonlinear.ipynb` replaces the observation model with a nonlinear mapping and employs kernel density estimates via `scipy.stats.gaussian_kde`.
- `check.ipynb` provides quick sanity plots or scalar checks.
- The `matched/` subdirectory holds side-by-side PDFs (EnKF vs. PCKF) for each floor to verify the agreement of the ensemble approximation.

### Polynomial Chaos Kalman Filter (`PCKF/`)
- `PCKF_linear.ipynb` and `PCKF_nonlinear.ipynb` load the saved EnKF ensembles (see the `np.load('./../EnKF/enkf_*.npz')` calls) to match the stochastic inputs and then propagate Polynomial Chaos coefficients (`pckf_prior_alpha.npz`, `pckf_update_alpha.npz`).
- Each notebook exports KDE plots for individual floors with varying sample budgets (`10k`, `100k`, `1mill`, `10mill`), letting you study convergence as ensemble size grows.
- The `matched/nonlinear` folder mirrors the EnKF comparisons and helps confirm that the chaos expansion reproduces the target PDFs floor-by-floor.

### 10-DOF Examples (`example_10dof/`)
- `EnkF_10floors.ipynb` and `PCKF_10floors.ipynb` scale the approach to ten floors with direct measurements, while the `_averaged` variants (`EnkF_10floors_averaged.ipynb`, `PCKF_10floors_averaged.ipynb`) use averaged measurement matrices (see the custom `Hk` definitions).
- `matched/linear` and `matched/nonlinear` contain nested folders (`1data`, `2data`, `3data`, `10data`) with the saved priors/posteriors (`pckf_prior_pdfs.npz`, `pckf_update_pdfs.npz`) and Polynomial Chaos coefficients, allowing you to reproduce the figures or feed the data into other notebooks.
- Each notebook saves an accompanying `pckf_prior_pdfs.npz`, `pckf_update_pdfs.npz`, and (when relevant) `pckf_prior_alpha.npz` / `pckf_update_alpha.npz`, enabling deterministic reruns without regenerating the ensembles.

## Data and Outputs

- `.npz` archives capture ensembles, priors, and posteriors for KF, EnKF, and PCKF runs. They are ready to be reloaded with `numpy.load` for downstream analyses.
- `.pdf` files (for example `PCKF_floor3_2data_nonlinear_1mill.pdf` or `EnKF_floor1_2data.pdf`) summarize the PDFs for each floor, observation count, and noise setting. Many are duplicated under `matched/` folders to simplify cross-filter comparisons.
- Plain-text helpers such as `EnkF/sample.txt` and `example_10dof/data_sample.txt` store the actual observation draws that seeded a notebook run.

## Extending the Studies

- Adjust `observed`, `floors`, or `Hk` inside the notebooks to test different sensor layouts or averaged measurements.
- Increase `nsamp`/`npc` to study convergence (the existing `10k` to `10mill` PDFs provide a reference for how the density evolves).
- Swap the nonlinear observation sections in `EnkF_multiplefloors_nonlinear.ipynb` and `PCKF_nonlinear.ipynb` with your own measurement equations, keeping the saved `.npz` files in sync if you want to compare filters.
- Use the `matched/` artifacts as regression baselines; rerun a notebook, regenerate the PDFs, and compare them to the saved plots to confirm nothing drifted.

## Questions ?
Contact : Sudhi Sharma P V 
Email: sudhisharmapadillath@gmail.com
