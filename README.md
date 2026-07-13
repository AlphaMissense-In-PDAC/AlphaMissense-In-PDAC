# AlphaMissense in Pancreatic Ductal Adenocarcinoma (PDAC)

This project evaluates whether [AlphaMissense](https://github.com/google-deepmind/alphamissense) pathogenicity scores capture genuine oncogenic signal in PDAC, and whether that signal is strong enough to support two diagnostic tasks: distinguishing PDAC patients from healthy individuals, and resolving the clinically relevant Classical and Basal-like molecular subtypes.

## Background

PDAC is characterized by a small set of recurrently mutated driver genes (`KRAS`, `TP53`, `SMAD4`, `CDKN2A`) and has two major molecular subtypes, Classical and Basal-like, which respond differently to chemotherapy. Subtyping currently depends on gene-expression sequencing, which is too slow and resource-intensive for routine clinical use. A diagnostic route derived directly from DNA sequencing would be more compatible with existing clinical workflows.

AlphaMissense is an AI model that predicts the pathogenicity of human missense mutations by combining AlphaFold2 structural context with evolutionary sequence signal, in contrast to context-free substitution matrices such as BLOSUM62. This project scores somatic mutations from PDAC patients (TCGA-PAAD) and germline variants from healthy individuals (1000 Genomes Project) to test whether these scores reflect real oncogenic biology and carry diagnostic value.

## Key findings

- AlphaMissense separates somatic (PDAC) from germline (healthy) missense variants far more effectively than BLOSUM62 (effect size CLES 0.83 vs. 0.55).
- Pathogenic mutations concentrate in core functional protein domains and recover established mutation hotspots (e.g. KRAS G12/Q61, TP53 R248/R273, SMAD4 R361, CDKN2A H83).
- A single-feature AlphaMissense threshold classifier separates PDAC patients from healthy individuals at optimal AM threshold of 0.994, with an AUPRC of 0.902. It surfaces 20 candidate driver-like genes beyond the four canonical drivers, out of which five are novel candidates based on literature verification.
- Classical vs. Basal-like subtype resolution remains weak (AUROC ~0.55-0.68, even with a purpose-built pathway-level classifier: Biologically Validated Pathway - Linear Discriminant Analysis), suggesting subtype identity depends on signal beyond the somatic mutation profile alone (e.g. gene expression, epigenetics).

## Repository structure

### `1_Pipeline/`
Builds the annotated mutation datasets used by all downstream analyses.

| Notebook | Purpose |
|---|---|
| `1_TCGA_AlphaMissense_Pipeline.ipynb` | Builds the TCGA-PAAD somatic mutation table (168 PDAC patients) annotated with AlphaMissense scores |
| `2_1000G_AlphaMissense_Pipeline.ipynb` | Builds the 1000 Genomes germline variant table (3,202 healthy individuals) annotated with AlphaMissense scores |
| `3_Merge_TCGA_1000G.ipynb` | Merges the somatic and germline tables into a single combined dataset |

### `2_Analyses/`
A sequence of key objectives (KO1-KO7) analyzing the merged dataset.

| Notebook | Purpose |
|---|---|
| `KO1_AM_somatic_vs_germline.ipynb` | Compares AlphaMissense score distributions between somatic and germline variants |
| `KO2_AM_driver_genes.ipynb` | Examines AlphaMissense scores in the four canonical driver genes and additional driver-like candidates |
| `KO3_AM_vs_BLOSUM62.ipynb` | Compares AlphaMissense against BLOSUM62 as a diagnostic score |
| `KO4_domain_AM_3D.ipynb` | Maps pathogenicity scores and mutation hotspots onto protein domains and 3D structures |
| `KO5_AM_threshold_classifier.ipynb` | Builds a single-feature AlphaMissense-threshold classifier for PDAC vs. healthy |
| `KO6_1_Unsupervised_PCA.ipynb` | Unsupervised PCA/clustering on gene- and pathway-level mutation features |
| `KO6_2_LDA_Overfit.ipynb` | Full-data (overfit) LDA used for pathway feature selection and ranking |
| `KO6_3_Healthy_PDAC_LDA.ipynb` | Cross-validated pathway-level LDA classifier, Healthy vs. PDAC |
| `KO6_4_Classical_Basal_LDA.ipynb` | Cross-validated pathway-level LDA classifier, Classical vs. Basal-like subtype |
| `KO6_5_Scatter_Plot.ipynb` | Combined 2D view of both LDA discriminant scores across all individuals |
| `KO7_Model_Overview.ipynb` | Consolidated comparison of all classifiers across both diagnostic tasks |
| `domain_annotations.yaml` | Protein domain and color configuration for KRAS, TP53, SMAD4, and CDKN2A |

## Data sources

- **TCGA-PAAD** somatic mutations, via cBioPortal (GRCh38)
- **1000 Genomes Project** germline variants, 2022 high-coverage phased release
- **AlphaMissense** hg38 pathogenicity scores ([Zenodo record 10813168](https://zenodo.org/records/10813168))
- **Reactome** and **MSigDB Hallmark** gene sets, for pathway-level feature construction

Data file paths referenced in the notebooks are specific to the machine they were developed on and will need to be adjusted locally before rerunning any notebook.

## Requirements

No formal dependency file is included; each notebook lists its own requirements in an initial "Prerequisites" cell. Core libraries used throughout the project:

- [Polars](https://pola.rs/) — data manipulation
- [SciPy](https://scipy.org/) — statistical tests
- [scikit-learn](https://scikit-learn.org/) — machine learning (PCA, LDA, clustering)
- [scikit-posthocs](https://scikit-posthocs.readthedocs.io/) — post-hoc statistical tests
- [BioPython](https://biopython.org/) — BLOSUM62 matrix
- [GSEApy](https://gseapy.readthedocs.io/) — MSigDB Hallmark gene set retrieval
- [PyMOL](https://pymol.org/) — 3D structural visualization
- [great-tables](https://posit-dev.github.io/great-tables/) — formatted tables
- Matplotlib / Seaborn — plotting

## Citation

If you use this work, please cite it as:

> van der Sommen, F. & Eeltink, D. (2026). *AlphaMissense in Pancreatic Ductal Adenocarcinoma (PDAC)* [Software]. GitHub. https://github.com/AlphaMissense-In-PDAC/AlphaMissense-In-PDAC

```bibtex
@software{vandersommen_eeltink_alphamissense_pdac_2026,
  author = {van der Sommen, Felix and Eeltink, Daan},
  title  = {AlphaMissense in Pancreatic Ductal Adenocarcinoma (PDAC)},
  year   = {2026},
  url    = {https://github.com/AlphaMissense-In-PDAC/AlphaMissense-In-PDAC}
}
```

This is a placeholder citation for the code itself; it will be updated once the associated research is published. Machine-readable citation metadata is also available in [`CITATION.cff`](CITATION.cff).

## License

This project is licensed under the [MIT License](LICENSE).

## Authors

- Felix van der Sommen — felixvandersommen@gmail.com — [ORCID](https://orcid.org/0009-0009-2177-3874)
- Daan Eeltink — daan.eeltink@gmail.com — [ORCID](https://orcid.org/0009-0000-2138-3336)

LACDR, Leiden University
