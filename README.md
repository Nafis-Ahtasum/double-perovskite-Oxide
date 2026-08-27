# double-perovskite-Oxide
This is a work flow that discovers lead free double perovskite oxides  and validates them with first-principles DFT



# High-Throughput Machine Learning Discovery of Lead-Free Double Perovskite Oxides

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/)
[![Scikit-Learn](https://img.shields.io/badge/scikit--learn-F7931E?logo=scikit-learn&logoColor=white)](https://scikit-learn.org/)
[![XGBoost](https://img.shields.io/badge/XGBoost-EB2529?logo=xgboost&logoColor=white)](https://xgboost.readthedocs.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE.md)

It is a machine learning framework trained on over 5000 double perovskite datasets, deployed for the accelerated discovery of lead-free ordered double perovskite oxides ($A_2BB'O_6$). This repository integrates data-driven surrogate modeling with physics-based screening funnels to resolve the multi-objective optimization challenge across **thermodynamic phase stability**, **metal/insulator classification**, and the **Shockley–Queisser (SQ) optimal bandgap window** ($1.1 \le E_g \le 1.4\text{ eV}$).







### The Problem We Are Solving

Finding a working lead-free double perovskite oxide absorber is difficult because several physical trade-offs compete directly against one another:

* **Thermodynamic phase stability:** Many charge-balanced compositions never form a single-phase crystal and immediately decompose into simpler binary or ternary oxides.
* **Metallic transport bottlenecks:** Introducing various transition metals into the B/B' sublattices frequently causes the valence and conduction bands to overlap, turning the compound into a metallic conductor instead of a semiconductor.
* **Bandgap placement for solar harvesting:** To reach maximum theoretical efficiency under the Shockley–Queisser limit, single-junction photovoltaic absorbers need an electronic bandgap positioned squarely between 1.1 eV and 1.4 eV.



### How the Screening Works

Instead of running a bandgap model on every compound blindly, the pipeline filters candidates step-by-step:

1. **Geometric sanity check:** Calculates the Goldschmidt tolerance factor ($0.8 \le t \le 1.1$) and octahedral factor ($\mu \ge 0.414$) to drop distorted or impossible packing geometries early.
2. **Stability filter:** Keeps compounds close to the convex hull ($E_{\text{hull}}$) to avoid phases that decompose.
3. **Metal vs. semiconductor gate:** Drops metallic/conductive phases.
4. **Bandgap regression:** Uses regression models to predict $E_g$ and flag anything in the 1.1–1.4 eV target window.

### Validation through Density Functional Theory (DFT) Calculations

For the top candidate endpoints that passed all machine learning gates, we carried out first-principles DFT calculations to explicitly verify their ground-state properties:

* **Structural Relaxations:** Performed spin-polarized geometry optimizations using the GGA-PBE functional, allowing both cell parameters and atomic coordinates to fully relax until residual forces converged.
* **Electronic Band Structures & PDOS:** Computed electronic dispersion and projected density of states to confirm:
  * The true fundamental electronic bandgap ($E_g$).
  * Direct vs. indirect bandgap nature.
  * Orbital character and hybridization at the valence band maximum (VBM) and conduction band minimum (CBM).
* **Thermodynamic Phase Stability:** Evaluated decomposition enthalpies ($\Delta H$) against competing binary/ternary oxide decomposition routes to confirm experimental synthesizability and low energy above the convex hull.



### Saved Models

The trained model files are in `model/bandgap/`:

* `xgbr_model.joblib`: Tuned XGBoost regressor (gives the lowest test error and serves as the main model).
* `rf_model.joblib`: Random Forest baseline.
* `svr_model.joblib`: Support Vector Regressor with an RBF kernel.


### Descriptors

All input features are generated directly from composition and elemental tables, so you don't need relaxed DFT structures beforehand:

* **Radii & Packing:** Shannon ionic radii ($r_A, r_B, r_{B'}, r_O$), tolerance factor ($t$), and octahedral factor ($\mu$).
* **Electronegativity:** Pauling electronegativities and metal-oxygen differences ($\Delta\chi$).
