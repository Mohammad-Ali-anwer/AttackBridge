# AttackBridge

AttackBridge is a research pipeline for mapping Common Vulnerabilities and
Exposures (CVEs) to Common Attack Pattern Enumeration and Classification
(CAPEC) labels. It combines CVE text preprocessing, keyword extraction,
CVE-to-CWE and CVE-to-CAPEC classifiers, retrieval-augmented extreme
multi-label classification (RAE-XMC), and validation-calibrated score fusion.

The repository contains the source code, compact mapping tables, figures, and
reported result summaries. Raw CVE feeds, processed datasets, model
checkpoints, embeddings, logs, and generated experiment outputs are excluded
because they are large and can be recreated by the pipeline.

## Pipeline

```text
NVD CVE data
    |
    v
Stage 01: CWE/CAPEC ground truth and mappings
    |
    v
Stage 02: CVE cleaning and preprocessing
    |
    v
Stage 03: keyword extraction and keyword-only evaluation
    |
    v
Stage 04: neural baselines, CGDPF, RAE-XMC, and AttackBridge fusion
    |
    v
Stage 05: local prediction UI
```

## Repository structure

| Path | Purpose |
| --- | --- |
| `01_cwe_capec_ground_truth/` | CWE/CAPEC catalog preparation, mappings, and paper figures |
| `02_preprocessing/` | CVE text cleaning and construction of `dataset.csv` |
| `03_keyword_making/` | Occlusion, phrase-similarity, modified KeyBERT, TextRank, and Qwen keyword methods |
| `04_evaluation/` | CVE-to-CWE/CAPEC baselines, CGDPF, RAE-XMC, fusion, and result collection |
| `05_UI/` | Browser UI and Python inference server |
| `model_performance_tables.txt` | Compact summary of saved evaluation results |

Stage-specific details are documented in
[`03_keyword_making/README.md`](03_keyword_making/README.md),
[`03_keyword_making/evaluation/README.md`](03_keyword_making/evaluation/README.md),
[`04_evaluation/README.md`](04_evaluation/README.md), and
[`05_UI/README.md`](05_UI/README.md).

## Environment

Python 3.10 or newer is recommended. A CUDA-capable GPU is recommended for
neural training and Qwen keyword generation; preprocessing and result
collection can run on CPU.

```bash
python -m venv .venv
# macOS/Linux:
source .venv/bin/activate
# Windows PowerShell:
# .venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
python -m spacy download en_core_web_sm
```

## Data preparation

1. Create `00_input_data/` and place the required NVD yearly CSV files there.
   The preprocessing code expects names such as `nvdcve-2.0-2024.csv`.
2. The compact CWE/CAPEC catalogs and mappings needed by later stages are
   already included. To regenerate them from MITRE sources, run the Stage-01
   script from its own directory:

   ```bash
   cd 01_cwe_capec_ground_truth
   python preprocess.py
   cd ..
   ```

3. Build the processed CVE dataset:

   ```bash
   python 02_preprocessing/preprocess.py
   ```

   This creates `02_preprocessing/dataset.csv`, which is intentionally ignored
   by Git.

## Running the experiments

Run commands from the repository root after completing data preparation.
Stage 03 generates five keyword sets and then compares them:

```bash
python 03_keyword_making/methods/occlusion.py
python 03_keyword_making/methods/phrase_similarity.py
python 03_keyword_making/methods/keybert_modified.py
python 03_keyword_making/methods/textrank.py
python 03_keyword_making/methods/qwen_keywords.py
python 03_keyword_making/evaluation/evaluate_all_methods.py
```

Qwen uses a large model and may require substantial GPU memory. To skip it,
omit the Qwen command; the five-method comparison requires outputs for all five
methods. Detailed Stage-03 options are in
[`03_keyword_making/README.md`](03_keyword_making/README.md).

Stage-04 models can be run directly with Python; train the prerequisite CWE
and direct-CAPEC models before the dependent fusion models. See
[`04_evaluation/README.md`](04_evaluation/README.md) for the model matrix and
execution order. Generated files are written below the stage `outputs/`
folders and are ignored by Git.

## Local UI

The UI needs a completed keyword-aware Stage-04 model bundle and the processed
dataset. These large trained artifacts are not stored in Git. After training,
follow [`05_UI/README.md`](05_UI/README.md) to select the model directory and
start the server.

```bash
python 05_UI/server.py
```

Then open <http://127.0.0.1:8000>.

## Reproducibility and generated files

The train/validation/test split uses seed 42, and hyperparameters are selected
on validation data before a single final test evaluation. Generated datasets,
checkpoints, embeddings, logs, caches, and result directories are covered by
`.gitignore`; keep them outside commits. The small checked-in tables and
figures provide the ground-truth resources and reported paper outputs needed
to understand the experiments.
