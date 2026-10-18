# Keyword-only CAPEC evaluation

`evaluate_all_methods.py` compares these five keyword methods:

- Occlusion
- Phrase Similarity
- KeyBERT Modified
- TextRank
- Qwen Keywords

The evaluator uses `sentence-transformers/all-MiniLM-L6-v2` as a fixed text
encoder for each keyphrase and CAPEC Name + Description, then ranks CAPECs by
cosine similarity. It does not train a classifier or fine-tune MiniLM. For a
CVE with multiple keyphrases, each CAPEC receives the maximum score from any
of those keyphrases. Only the keyword column is used as the CVE query.

The five highest-ranked CAPEC IDs form the predicted multilabel set. The CSV
metrics include micro, macro, weighted, and sample precision/recall/F1, sample
Jaccard, Hamming loss, exact match, Hit@1, and Hit@5.

Run the comparison from the repository root after generating all five method
outputs:

```bash
python 03_keyword_making/evaluation/evaluate_all_methods.py
```

The main result is written to:

`03_keyword_making/outputs/evaluation/minilm_cosine_top5/comparison_summary.csv`

Each method also gets `metrics.csv` and prediction CSVs for the training,
validation, and testing splits.
