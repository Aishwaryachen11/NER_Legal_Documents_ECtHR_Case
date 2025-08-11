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
