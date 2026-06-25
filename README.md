# Disease-Module-Aware GNN for Drug Repurposing: A Controlled Benchmark and Negative Result

This repository contains the full pipeline, code, and figures for the study
*"A Disease-Module-Aware Graph Neural Network for Drug Repurposing: A Controlled
Benchmark and Negative Result."*

## Summary

A widely held assumption in network-based drug discovery is that encoding
disease-module proximity as an inductive bias should improve graph neural network
(GNN) predictions for drug repurposing. This work tests that assumption directly.

We integrate three open biomedical resources (BioGRID, CTD, DGIdb) into a
heterogeneous gene–drug graph and introduce a disease-module-aware (DMAA) gating
mechanism driven by random-walk-with-restart (RWR) proximity from curated
glioblastoma seed genes. We compare it against an **architecturally identical
gate-free baseline**, so the disease prior is the only experimental variable.

**Key finding:** across a controlled ablation, a hyperparameter fairness sweep, and
a matched-learning-rate confirmatory test with multiple-comparison correction, the
disease-module prior yields **no significant improvement** over a disease-agnostic
GNN. A mechanistic diagnostic shows that RWR proximity is rank-redundant with
network centrality the model already learns. The same pipeline still recovers known
glioblastoma therapeutics with high statistical significance, demonstrating that
useful repurposing rankings arise from the learned representation rather than the
explicit prior.

## Repository structure

```
.
├── notebooks/
│   └── pipeline.ipynb        # Full 9-step pipeline (data -> graph -> models -> analysis)
├── figures/
│   ├── Figure_1_pipeline.png
│   ├── Figure_2_ppi_degree_rwr.png
│   ├── Figure_3_disease_module_subnetwork.png
│   ├── Figure_4_benchmark_slices.png
│   ├── Figure_5_roc_pr.png
│   ├── Figure_6_hyperparameter_sweep.png
│   ├── Figure_7_proximity_redundancy.png
│   ├── Figure_8_enrichment_casestudy.png
│   └── Figure_9_interpretability_paths.png
├── results/
│   ├── ranked_repurposing_candidates_approved.csv
│   └── enrichment_*.csv
└── README.md
```

## Data

The pipeline uses three openly licensed resources, mirrored as public Kaggle
datasets for convenient use in Kaggle notebooks:

| Resource | Version | Kaggle dataset |
|----------|---------|----------------|
| BioGRID  | 5.0.258 | https://www.kaggle.com/datasets/bassam165/biogrid |
| CTD      | June 2026 | https://www.kaggle.com/datasets/bassam165/comparative-toxicogenomics-database |
| DGIdb    | December 2024 | https://www.kaggle.com/datasets/bassam165/dgidb-drug-gene-interaction-database |

Original sources: [BioGRID](https://thebiogrid.org/),
[CTD](https://ctdbase.org/), [DGIdb](https://dgidb.org/). Please cite the original
database publications (see the paper) when using these data.

## Environment

The pipeline was developed and run on a **Kaggle notebook with a single NVIDIA P100
GPU (16 GB)**. Core dependencies:

- `torch==2.4.1+cu121` (pinned for P100 / sm_60 compatibility)
- `numpy`, `pandas`, `scipy`, `scikit-learn`, `statsmodels`
- `networkx`, `matplotlib`, `seaborn`

To reproduce on Kaggle: create a notebook, attach the three datasets above, set the
accelerator to GPU P100, and run the pipeline notebook end to end.

## Reproducing the study

The pipeline runs in nine sequential steps:

1. Data loading and identifier harmonization (NCBI Gene, MeSH)
2. Heterogeneous graph construction + RWR disease module + node features
3. Task definition (degree-matched negatives, transductive and cold-start splits)
4. Model definitions (DMAA-gated GCN, gate-free GCN, GAT)
5. Training (10 seeds, full-graph encode + batched edge scoring)
6. Benchmark metrics across overall / proximal / non-proximal slices
7. Hyperparameter fairness sweep + matched-learning-rate confirmatory test
8. Mechanistic redundancy diagnostic (RWR vs centrality)
9. Repurposing case study (approved-drug pool, enrichment, interpretability paths)

Each step is a self-contained cell that reads the previous step's saved artifacts.
Run them in order within one session.

## Key results

| Model | Overall AUPRC | Disease-proximal AUPRC |
|-------|---------------|------------------------|
| GCN (gate-free) | 0.9488 ± 0.0069 | 0.9324 ± 0.0112 |
| GAT | 0.9305 ± 0.0218 | 0.9165 ± 0.0278 |
| DMAA-gated | 0.9225 ± 0.0202 | 0.8960 ± 0.0294 |

At matched learning rate, no gated variant significantly improves over the gate-free
GCN (all p_BH > 0.36, overlapping bootstrap CIs).

## Citation

If you use this code or data preparation, please cite:

```bibtex
@article{bassam2025dmaagnn,
  title   = {A Disease-Module-Aware Graph Neural Network for Drug Repurposing:
             A Controlled Benchmark and Negative Result},
  author  = {Abdullah Al Bassam and Mst Mohona Akter},
  journal = {Manuscript under review},
  year    = {2026}
}
```

## License

Code in this repository is released under the MIT License. The biomedical datasets
retain their original licenses (BioGRID: MIT; CTD and DGIdb: see their respective
terms of use).

## Contact

Abdullah Al Bassam — abassamrajbd65@gmail.com
Department of Computer Science and Engineering, Varendra University, Rajshahi, Bangladesh
