# Project: Peptide Sequence Optimization based on Contact Map Alignment

## Overview
This project focuses on the generation and optimization of candidate peptide sequences whose structural contact maps align with those of a target peptide. The computational pipeline integrates sequence preprocessing, initial population generation using biological heuristics, optimization via a Genetic Algorithm (GA) leveraging the ESM-2 model, and structural validation through AlphaFold/ColabFold.

All scripts include configurable directory paths at the beginning of each file to ensure environment adaptability.

---

## Project Structure

### Root Directories
- **experiments_for_models/**: Contains preliminary experiments and benchmarks used to select the final two predictive models.
- **fasta_files/**: Stores the FASTA files corresponding to the target peptides.
- **initial_population/**: Contains generated initial populations (one CSV per peptide, including ID and sequence).
- **real_contact_maps/**: Ground truth contact maps derived from target sequences.
- **real_distances/**: Ground truth distance matrices derived from target sequences.
- **localcolabfold/**: Standard directory generated upon the installation of ColabFold for structural prediction.

### Component-Specific Folders
- **initialize/**: Core preprocessing scripts.
    - `pdb_files/`: Input directory for all target PDB files.
    - `get_contact_maps.py`: Script to extract contact maps from PDBs.
    - `get_matrix_distances.py`: Script to compute distance matrices.
    - `pdf_to_fasta.py`: Utility to convert source data to FASTA format.
- **results_esm/**: Outputs from the Genetic Algorithm optimization.
    - `resultados/`: Main optimization results and logs.
    - `contact_maps/`: Cached contact maps to optimize execution time.
    - `fasta_files/`: FASTA records of evaluated candidate sequences.
- **esm2/**:
    - `get_contact_map_batch.py`: Module for batch prediction of contact maps using the ESM-2 transformer model.
- **results_alphaFold/**: Structural evaluation and comparison data.
    - `metrics_results/`: Results categorized by peptide, including dRMSD, Pearson correlation, and GDT-TS metrics.
    - `results_random/`: Baseline metrics generated from random sequence populations.

---

## Prerequisites
Before execution, ensure that all target PDB files are placed in the following directory:
`initialize/pdb_files/`

---

## Execution Pipeline

### 1. Environment Initialization
Run: `initialize.py`
This script automates the preprocessing phase:
- Generates FASTA files in `fasta_files/`.
- Computes ground truth contact maps in `real_contact_maps/`.
- Computes ground truth distance matrices in `real_distances/`.

### 2. Initial Population Generation
Run: `start_population.ipynb`
This notebook facilitates the creation of the starting population for the GA using two distinct methods:
- `generate_initial_population_by_blosum62`: Uses the BLOSUM62 substitution matrix.
- `generate_initial_population_by_hydrophobic`: Utilizes a specialized hydrophobic substitution dictionary.
**Output:** A CSV file per peptide in `initial_population/` containing sequence IDs and strings.

### 3. Sequence Optimization (Genetic Algorithm)
Run: `esm2.ipynb`
Executes the optimization process based on the ESM-2 model.
- **Parameters:** Target protein, fitness function (`fitness_auc_pr` or `fitness_shannon_entropy`), number of generations, offspring count, and population size.
- **Features:** Includes a caching mechanism for contact maps in `results_esm/contact_maps/` to prevent redundant computations.
- **Metrics:** Execution time is recorded individually for each sequence.

### 4. Structural Evaluation
Run: `alphafold.ipynb`
Predicts the 3D structure of optimized candidate sequences and performs a comparative analysis against the target structures.
- **Selected Individuals:** By default, evaluates indices [0, 14, 29, 44, 59, 74, 89].
- **Metrics Computed:** dRMSD, Pearson correlation, and GDT-TS.
- **Baseline:** Includes automated logic for generating and evaluating random sequences for performance comparison.

### 5. Statistical Analysis
Run: `analysis_of_results.ipynb`
Performs a comprehensive statistical evaluation of the metrics obtained in the previous stages to validate the efficacy of the optimization.

---

## Technical Notes
- **Modularity:** The pipeline is designed to be modular; each step can be executed independently provided the required input files from previous steps are present.
- **Performance:** Caching is heavily utilized in the ESM-2 optimization step to mitigate the computational cost of transformer-based predictions.
- **Scalability:** Directory paths are defined as variables at the top of each script to facilitate deployment across different local or cloud environments.