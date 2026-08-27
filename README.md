# double-perovskite-Oxide
This is a work flow that discovers lead free double perovskite oxides  and validates with first principles DFT



# High-Throughput Machine Learning Discovery of Lead-Free Double Perovskite Oxides

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/)
[![Scikit-Learn](https://img.shields.io/badge/scikit--learn-F7931E?logo=scikit-learn&logoColor=white)](https://scikit-learn.org/)
[![XGBoost](https://img.shields.io/badge/XGBoost-EB2529?logo=xgboost&logoColor=white)](https://xgboost.readthedocs.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE.md)

A machine learning framework for the accelerated discovery of lead-free ordered double perovskite oxides ($A_2BB'O_6$). This repository integrates data-driven surrogate modeling with physics-based screening funnels to resolve the multi-objective optimization challenge across **thermodynamic phase stability**, **metal/insulator classification**, and the **Shockley–Queisser (SQ) optimal bandgap window** ($1.1 \le E_g \le 1.4\text{ eV}$).



## 1. Project Background & Motivation

Finding viable, non-toxic alternatives to traditional lead-halide perovskites requires navigating non-linear physical and chemical trade-offs:
* **Thermodynamic Phase Stability:** Nominal charge neutrality alone does not prevent spontaneous phase separation into competing binary or ternary oxides.
* **Electronic Ground State (Metal vs. Insulator/Semiconductor):** Incorporating open-shell transition metals often collapses the fundamental bandgap into metallic or narrow-gap conductor states.
* **Optical Absorption & SQ Optimum:** Single-junction solar energy conversion demands an electronic bandgap strictly targeted between $1.1\text{ eV}$ and $1.4\text{ eV}$ to maximize theoretical power conversion efficiency.

This framework replaces brute-force forward quantum-mechanical simulations with a multi-stage machine learning screening funnel trained and cross-validated on **5,000+ DFT-calculated double perovskite configurations**.

# Machine Learning Discovery of Lead-Free Double Perovskite Oxides

This repository contains the machine learning pipeline I developed to screen and discover new lead-free double perovskite oxides ($A_2BB'O_6$) for solar cells and optoelectronic applications. 

Running first-principles DFT calculations for thousands of potential compositions simply takes too much computation time and cluster resources. To work around that bottleneck, I trained surrogate machine learning models on a dataset of over 5,000 double perovskite structures to quickly evaluate stability, electronic behavior, and optical bandgaps in a matter of seconds.




### The Problem We Are Solving

Finding a working lead-free double perovskite oxide absorber is difficult because several physical trade-offs compete directly against one another:

* **Thermodynamic phase stability:** Many charge-balanced compositions never form a single-phase crystal and immediately decompose into simpler binary or ternary oxides.
* **Metallic transport bottlenecks:** Introducing various transition metals into the B/B' sublattices frequently causes the valence and conduction bands to overlap, turning the compound into a metallic conductor instead of a semiconductor.
* **Bandgap placement for solar harvesting:** To reach maximum theoretical efficiency under the Shockley–Queisser limit, single-junction photovoltaic absorbers need an electronic bandgap positioned squarely between 1.1 eV and 1.4 eV.


### How the Multi-Stage Screening Works

Instead of running a single regression model on every composition blindly, the pipeline filters candidates through several sequential gates:

1. **Geometric formability:** We first calculate the Goldschmidt tolerance factor ($0.8 \le t \le 1.1$) and octahedral factor ($\mu \ge 0.414$) using Shannon ionic radii to reject structurally unfeasible packing arrangements early.
2. **Thermodynamic stability screening:** Candidates that pass the geometric filter are evaluated by a classification layer to ensure they reside on or very close to the convex hull ($E_{\text{hull}}$), rejecting combinations prone to phase separation.
3. **Metal vs. insulator/semiconductor gating:** The remaining phases are checked to separate true semiconducting/insulating electronic configurations from zero-gap metallic states.
4. **Bandgap regression:** Finally, the pre-trained regression models predict the scalar bandgap ($E_g$) and isolate only the compositions that land right inside the target 1.1 to 1.4 eV Shockley–Queisser window.



### Pre-Trained Models in This Repo

The pre-trained models are saved directly inside `model/bandgap/`:

* **`xgbr_model.joblib`:** An Extreme Gradient Boosting (XGBoost) regressor. This is the primary model used for bandgap targeting because it captures complex, non-linear interactions across mixed cation sublattices with the lowest test error.
* **`rf_model.joblib`:** A Random Forest regressor that serves as a dependable ensemble baseline and helps verify feature importances.
* **`svr_model.joblib`:** A Support Vector Regressor with an RBF kernel for boundary checks in lower-variance descriptor spaces.


### Input Features

All input features are built entirely from basic elemental and structural properties without needing relaxed DFT crystal coordinates:

* **Geometric parameters:** Shannon ionic radii ($r_A, r_B, r_{B'}, r_O$), Goldschmidt tolerance factor ($t$), and octahedral factor ($\mu$).
* **Electronegativity and bonding:** Pauling electronegativities ($\chi_A, \chi_B, \chi_{B'}$) and metal-oxygen electronegativity differences ($\Delta\chi$).
* **Electronic identity:** Formal oxidation states ($B^{3+}/B'^{3+}$ vs. $B^{2+}/B'^{4+}$) and valence electron counts.


