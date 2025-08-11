## Named Entity Recognition (NER) for Legal Documents — ECtHR Cases
This project performs Named Entity Recognition (NER) on European Court of Human Rights (ECtHR) legal case documents.
It focuses on identifying legal-specific entities such as PARTY, JUDGE, STATUTE, CASE_NO, etc.

### Project Overview
The project includes:
Data preprocessing and tokenization
Alignment of tokens with NER tags
Training of NER models (BERT-based, BiLSTM-based)
Evaluation and error analysis
Reporting results and confusion patterns

The models are designed to work with legal-domain language models like nlpaueb/legal-bert-base-uncased.

### Dataset
The dataset consists of ECtHR legal case texts annotated with entity labels.
It is structured in a HuggingFace DatasetDict format with train, validation, and test splits.

### Workflow (high-level)
Extract text you’ll annotate
Load ECtHR case texts and flatten paragraph lists into a single string per case.

Remove very short/noisy entries.

Save a clean CSV with case_id,text for annotation (e.g., ecthr_annotation_ready.csv).

### Define your NER label schema
Decide entity types that matter for legal downstream tasks (example set):

CASE_NUMBER, DATE, ARTICLE_REF, PERSON, COUNTRY, ORG, LAW

Save both a human-readable list (e.g., schema/labels.txt) and a machine-readable JSON (e.g., schema/labels.json with descriptions/examples).

### Create Doccano-ready JSONL
Convert each (case_id, text) row to a JSONL record:

{"id": <case_id>, "text": "<full case text>", "labels": []}

This is importable into Doccano / Label Studio for span annotation.

### Convert annotations → token labels (BIO)
Export annotated spans (Doccano JSONL).

Tokenize each text deterministically and align spans to tokens.

Emit one CoNLL-style line per token: token <TAB> BIO-TAG with blank lines between sentences.

Produce: ner_conll/train.conll (+ optional label_counts.csv).

### Split into train/dev/test
Split by document (not by sentence) to avoid leakage.

Write ner_conll/train.conll, ner_conll/dev.conll, ner_conll/test.conll.

### Modeling (kept simple)
Tokenization + label alignment (BIO → subwords): align labels to first wordpiece; mark others -100.

Lightweight baselines you can run anywhere:

BiLSTM (no-CRF): tiny, stable, no extra dependencies.

Most-Frequent-Tag (per token): zero-training sanity check.

Transformer (optional, stronger): nlpaueb/legal-bert-base-uncased for domain text. Use when resources allow.

Tip: If your gold test is accidentally all O, span metrics will be 0/undefined. Ensure your annotated test split actually contains entities.

### Evaluation
Metrics: strict span Precision / Recall / F1 (micro + per-entity) with seqeval.
Robust reporting:
If either gold or predictions contain no entities, print a clear note and skip per-entity table.

Save:
reports/evaluation_report.md (human summary)
eports/eval_summary.json (numbers for automation)
reports/examples.csv (first 100 token-level mismatches with context)

### Explainability (lightweight)
Rule attributions (optional when time is short):
Log which simple rule/gazetteer (e.g., Article + number, \d+/\d+ case nos.) triggered a prediction override.
Save to reports/simple_explanations.csv with token, original tag, new tag, and rule name.

### Repro checklist

Extract: create ecthr_annotation_ready.csv (case_id, text).
Schema: write schema/labels.txt and schema/labels.json.
Annotate in Doccano / Label Studio; export JSONL.
Convert export → CoNLL BIO (ner_conll/train|dev|test.conll).
Tokenize & align labels (for Transformer) or load directly for BiLSTM.
Train one model (BiLSTM minimal or Legal-BERT if resources allow).
Evaluate with seqeval and save the reports.
(Optional) Explainability: add simple rule overrides and log CSV.
