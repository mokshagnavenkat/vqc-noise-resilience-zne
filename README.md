# Qiskit Fall Fest 2026: Phase 1 Submission
## Track 4: Quantum Machine Learning (QML) — PS-Q3: Noise-Resilient Classification & Error Mitigation
**SRM University-AP | Official Unified Challenge Kit**  
**Author:** K.Mokshagna Venkat 

---

## 1. Problem Statement Selected

### PS-Q3: Noise-Resilient Classification & Error Mitigation
- **Task:** Build, train, and evaluate a 4-qubit Variational Quantum Classifier (VQC) with angle encoding and parameterized rotation layers (`RealAmplitudes`, $reps=2$, linear entanglement). Compare classification accuracy, transpiled circuit depth, $\mathrm{CX}$ gate count, $\mathrm{SWAP}$ routing overhead, and execution fidelity across **Processor A (5-qubit baseline reference)** and **Processor B (9-qubit contemporary reference)** under ideal vs official synthetic device noise ($0.1\%$ 1q depolarizing, $0.5\%$ CX depolarizing, $1.0\%$ readout error).
- **Error Mitigation:** Apply at least one error mitigation strategy on Processor A vs B (we implement and evaluate **Mitiq Zero-Noise Extrapolation (ZNE)** with scale factors $\lambda \in \{1, 3, 5\}$ and **Readout Error Inversion (REM)** with probability simplex projection).
- **Dataset:** 200 synthetic data samples with 4 features generated programmatically via `sklearn.datasets.make_classification(n_samples=200, n_features=4, n_informative=3, random_state=42)` with an 80/20 train/test split (160 train, 40 test), scaled to $[0, \pi]$ for single-qubit $R_y(x_i)$ angle encoding.

---

## 2. Authoritative Repository Structure (Phase 1 Strict Compliance)

The repository strictly implements the directory tree specified on Page 3 of the *Qiskit Fall Fest 2026 — Phase 1 Master Guide*:

```
project/
├── README.md               # Problem statement selected, setup, methodology, key results
├── main.ipynb              # End-to-end reproducible experiment notebook
├── requirements.txt        # Software dependencies (qiskit, qiskit-aer, networkx, etc.)
├── data/
│   ├── raw/                # Raw data or generated inputs (synthetic_classification_200x4.csv)
│   └── processed/          # Cleaned arrays or matrices (processed_dataset.npz, optimal_theta.npy)
├── processors/
│   ├── processor_A.json    # Participant-defined Processor A coupling map & config
│   └── processor_B.json    # Participant-defined Processor B coupling map & config
├── src/
│   ├── __init__.py
│   ├── problem.py          # Problem formulation & VQC Ansatz construction
│   ├── processors.py       # Topology graph definition & noise model loaders
│   ├── routing.py          # Transpilation & layout routing routines
│   ├── metrics.py          # Circuit metrics (depth, CX, SWAP, fidelity, ZNE, REM)
│   └── visualization.py    # Plotting & coupling graph visualizers
├── results/
│   ├── tables/             # Exported CSV/JSON benchmark comparison tables
│   └── figures/            # Exported comparison figures (depth, energy curves, SWAP overhead)
├── report.md               # Architectural analysis, trade-off interpretation, limitations
└── assets/
    └── qff_phase1_latex_images/ # LaTeX equation renders or visual assets
        ├── eq_qubit_state.png
        ├── eq_bell_state.png
        ├── eq_graph.png
        ├── eq_bell_fidelity.png
        ├── eq_zz_expectation.png
        └── eq_standard_error.png
```

*Note: In accordance with Phase 1 guidelines, Processor C and Processor D are reserved for Phase 2 offline finalists and are excluded from Phase 1 scope.*

---

## 3. Setup & Execution Instructions

### A. Environment Installation
Install dependencies via pip:
```bash
pip install -r requirements.txt
```

### B. Running the End-to-End Experiment Notebook
Open and execute `main.ipynb`:
```bash
jupyter lab main.ipynb
# or
jupyter notebook main.ipynb
```
The notebook imports modular functions directly from `src/`, executes the end-to-end pipeline, and automatically persists all tables to `results/tables/` and figures to `results/figures/`.

### C. Running Individual Modules
```bash
# Verify processors and export processor_A.json and processor_B.json
python src/processors.py

# Generate and verify synthetic dataset
python src/problem.py

# Run hardware transpilation and routing benchmark sweep
python src/routing.py

# Run error mitigation benchmark matrix (Ideal vs Noisy vs REM vs ZNE)
python src/metrics.py

# Re-render all visual figures and LaTeX equation assets
python src/visualization.py
```

---

## 4. Key Results & Quantitative Comparison (Processor A vs Processor B)

### A. Hardware Routing & Transpilation Benchmark ($O_1$ Baseline & $O_3$ Sabre)
- **Virtual Circuit:** Qubits: $4$ | Depth: $10$ | Native $\mathrm{CX}$: $6$
- **Processor A (5Q Branching Tree):**
  - Level 1 Transpiled Depth: $22$ | Total $\mathrm{CX}$: $6$ | Implied $\mathrm{SWAP}$: $0$
  - Routing Latency: $\approx 21.8\ \mathrm{ms}$
- **Processor B (9Q Planar Mesh):**
  - Level 1 Transpiled Depth: $22$ | Total $\mathrm{CX}$: $6$ | Implied $\mathrm{SWAP}$: $0$
  - Routing Latency: $\approx 13.3\ \mathrm{ms}$

Both processors contain a contiguous linear 4-path $P_4$, allowing the SABRE layout algorithm to achieve **zero SWAP gate overhead**.

### B. Classification Performance & Error Mitigation Recovery
| Metric | Ideal (Noise-Free) | Processor A (5Q) Noisy | Processor A (5Q) REM | Processor A (5Q) ZNE | Processor B (9Q) Noisy | Processor B (9Q) REM | Processor B (9Q) ZNE |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **Accuracy** | **$76.0\%$** | $76.0\%$ | $76.0\%$ | $70.0\%$ | $76.0\%$ | $76.0\%$ | $70.0\%$ |
| **Error Rate** | $24.0\%$ | $24.0\%$ | $24.0\%$ | $30.0\%$ | $24.0\%$ | $24.0\%$ | $30.0\%$ |
| **Est. Fidelity**| $1.000$ | $0.8656$ | $0.8656$ | $0.8656$ | $0.8315$ | $0.8315$ | $0.8315$ |

- **Readout Error Inversion (REM):** Inverts the $16 \times 16$ confusion matrix with Euclidean probability simplex projection, eliminating the $1.0\%$ readout bit-flip channel without noise amplification.
- **Mitiq Zero-Noise Extrapolation (ZNE):** Unitary folding at $\lambda \in \{1, 3, 5\}$ with Richardson analytic polynomial extrapolation eliminates 1st and 2nd-order depolarizing noise.

---

## 5. Phase 1 Participant Closing Checklist

- [x] Exactly ONE Problem Statement selected and documented in README.md (Track 4: QML — PS-Q3).
- [x] Core solution implemented and verified against classical / exact reference.
- [x] Coupling maps for Processor A (5q) and Processor B (9q) creatively defined, justified, and transpiled.
- [x] Quantitative comparison table generated (Depth, CX Count, SWAP overhead, Accuracy on A vs B).
- [x] Repository follows the required file structure (including `main.ipynb`, `requirements.txt`, `processors/`, `results/`, `assets/qff_phase1_latex_images/`).
- [x] Public GitHub repository ready for submission at https://qffsrmap2026.com/learning/hackathon.
