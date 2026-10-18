# Stage 03: keyword generation

Stage 03 generates CVE keyphrases using five methods:

- Occlusion
- Phrase Similarity
- Modified KeyBERT
- TextRank
- Qwen Keywords

The first three methods select their configuration using validation data and
then produce final training, validation, and testing splits. TextRank and Qwen
are fixed baselines. Generated files are stored in `outputs/`, which is
ignored by Git.

## Run

Run from the repository root after preparing `02_preprocessing/dataset.csv`:

```bash
python 03_keyword_making/methods/occlusion.py
python 03_keyword_making/methods/phrase_similarity.py
python 03_keyword_making/methods/keybert_modified.py
python 03_keyword_making/methods/textrank.py
python 03_keyword_making/methods/qwen_keywords.py
python 03_keyword_making/evaluation/evaluate_all_methods.py
```

Run the methods one at a time to avoid competing for GPU memory. Qwen uses a
7B-parameter model and may require a high-memory GPU. The comparison script expects output from all five methods. If Qwen is
unavailable, omit it and skip the five-method comparison; Stage 04 can still
use the Modified KeyBERT split files.

See [`evaluation/README.md`](evaluation/README.md) for the keyword-only CAPEC
retrieval evaluation and its output definitions.
