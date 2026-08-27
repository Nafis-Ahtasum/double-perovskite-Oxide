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


## 2. Multi-Stage Screening Funnel Architecture

The pipeline processes vast combinatorial spaces of $A_2BB'O_6$ compositions through sequential, physics-informed filters:


[ Combinatorial Composition Space: A₂BB'O₆ ]
  │
  ├──► Gate 1: Geometric Formability Filter
  │    (Tolerance factor: 0.8 ≤ t ≤ 1.1; Octahedral factor: μ ≥ 0.414)
  │
  ├──► Gate 2: Thermodynamic Phase Stability Classifier
  │    (Filters for near-hull viability: ΔE_hull ≤ 25–50 meV/atom)
  │
  ├──► Gate 3: Metal / Insulator Classification Gate
  │    (Screens out metallic/conductive electronic configurations)
  │
  └──► Gate 4: Electronic Bandgap Surrogate Regression (RF / SVR / XGBR)
       (Targets the Shockley–Queisser optimum: 1.1 eV ≤ Eg ≤ 1.4 eV)
       │
       ▼
 [ Shortlisted Lead-Free Photovoltaic & Optoelectronic Candidate Endpoints ]
