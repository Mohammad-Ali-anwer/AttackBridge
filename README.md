# AttackBridge

**Mapping CVE vulnerability descriptions to CAPEC attack patterns** using keyword-guided representations, CWE-guided dual-path fusion, and retrieval-augmented extreme multi-label classification.

NVD tags vulnerabilities with weaknesses (CWE) but almost never with the attack patterns (CAPEC) that describe *how* they can be exploited. AttackBridge predicts the relevant CAPEC attack patterns directly from a CVE's text, ranked with confidence scores.


## Results

CVE → CAPEC multi-label prediction on held-out test data:

| Model | Exact Match | Micro-F1 | Macro-F1 | Hit@1 | Hit@10 |
|---|---|---|---|---|---|
| NN baseline (MiniLM) | 0.508 | 0.652 | 0.308 | 0.745 | 0.853 |
| NN + KeyBERT keywords | 0.546 | 0.673 | 0.342 | 0.760 | 0.868 |
| Fine-tuned MiniLM + KeyBERT | 0.641 | 0.727 | 0.393 | 0.813 | 0.896 |
| RAE-XMC + KeyBERT | 0.699 | 0.727 | 0.385 | **0.842** | 0.903 |
| **AttackBridge (RAE-XMC + CGDPF)** | **0.709** | **0.735** | **0.402** | 0.841 | **0.905** |

The fine-tuned model scores slightly higher on Weighted-F1 and Sample-F1; full results are in `model_performance_tables.txt`.

## Dataset

| Stage | Count |
|---|---|
| NVD CVE records (2021 – May 2026) | 185,717 |
| After removing CWE-noinfo / CWE-other / deprecated CWEs | 165,936 |
| Final CVE–CAPEC instances | 133,915 |
| CAPEC labels covered | 450 of 559 |

Labels are highly imbalanced: many CAPEC patterns have fewer than five examples.

## Pipeline

1. **Ground truth:** build CVE → CAPEC labels from NVD CVE–CWE links and MITRE CWE–CAPEC mappings.
2. **Preprocessing:** normalise vendor, product and version names; clean and lemmatise text.
3. **Keyword extraction:** five methods compared (occlusion, phrase similarity, modified KeyBERT, TextRank, Qwen).
4. **Models:** neural baselines, fine-tuned MiniLM, CGDPF, RAE-XMC, and the AttackBridge fusion.
5. **UI:** a local browser interface that shows the top-K CAPEC predictions for any CVE.

<!-- Add the pipeline figure and a UI screenshot here -->

## Limitations

- CAPEC labels are derived through CWE → CAPEC mappings, so they are not independent of the CWE-guided prediction path. A manually validated test set is in progress.
- The current split is random. Temporally separated evaluation is planned.
- Performance on rare CAPEC classes remains low (Macro-F1 ≈ 0.40).
