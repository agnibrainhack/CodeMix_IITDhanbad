# Hinglish Code-Mix Data Generation & LLM-Based Correction

## Overview
This repository provides a **hybrid, linguistically-informed system** for generating realistic **Hinglish (Hindi–English) code-mixed text**, followed by **LLM-based correction**.  
It is designed for research in **code-mixing, NLP data augmentation, conversational AI robustness, and low-resource language modeling**.

The pipeline integrates:

- Linguistic constraints (Matrix Language Frame, Equivalence Constraint)
- spaCy-based syntactic analysis
- Custom lexicon (EN→HI→Romanization + POS tags + frequency)
- Neural Machine Translation (Helsinki OPUS)
- Discourse marker injection
- Phonetic/social media–style spelling variation
- Dataset generation + filtering
- LLM-based cleaning using vLLM + Mistral-7B-AWQ

---

## Key Features

### 🟢 Hybrid Hinglish Generator
- Linguistically valid code-switch prediction  
- Multi-source token generation: lexicon → POS fallback → NMT → fallback  
- Rich controls via `ControlConfig` (CMI, formality, domain)  
- Social-media realism via markers + slang + phonetic variation  

### 🟢 Dataset Builder
- Instruction–input–output triplet generation  
- Deduplication, profanity filtering, short-output filtering  
- Train/val/test split creation  
- Google Drive support for Colab workflows  

### 🟢 LLM-Based Cleaner
- Uses vLLM + Mistral 7B AWQ for correcting Hinglish  
- Preserves code-mixing, meaning, and style  
- Outputs cleaned datasets for robustness training

---

## Directory Structure
project_root/
│
├── data/
│ ├── resources/
│ │ ├── lexicon.json
│ │ ├── markers.json
│ ├── datasets/
│ ├── splits/
│
├── models/
│ └── checkpoints/
│
├── outputs/
│ ├── cleaned_train.jsonl
│ ├── cleaned_val.jsonl
│ ├── cleaned_test.jsonl
│
├── codemix_generator.py
├── dataset_builder.py
├── llm_cleaner.py
├── utils.py
└── README.md

---

## Installation

### 1. Clone the repository
```bash
git clone <repo-url>
cd <repo-folder>
pip install -r requirements.txt
python -m spacy download en_core_web_sm
pip install vllm
control = ControlConfig(
    cmi_target=0.7,      # How Hindi-heavy the output should be
    formality=0.2,       # 0 = very informal, 1 = highly formal
    domain="casual_chat"
)
This affects:

Switch point probability

Marker injection

Phonetic variation intensity

generator = StateOfTheArtCodeMixGenerator(use_nmt=True)
output = generator.generate("I am going to the market", control)
print(output)
```

Pipeline Steps

Linguistic parsing via spaCy

Identify valid switch points

Apply matrix language constraints

Generate candidate tokens:

Lexicon → POS fallback → NMT → original

Score & rank candidates using control parameters

Inject discourse markers (yaar, arre, etc.)

Apply phonetic / slang spellings

Clean formatting

# Example

```
Level 1 Candidate: I am going to the market to buy vegetables
Level 2 Candidate: arre I am goin to the bazaar to buy sabziyaaan yaar
Level 3 LLM Cleaned: arre I am going to the bazaar to buy sabziyaan
```

```
builder = DatasetBuilder(generator)
dataset = builder.build_dataset(base_examples, controls, num_examples_target=50000)
builder.save_and_split()
```
```
data/splits/train.jsonl
data/splits/val.jsonl
data/splits/test.jsonl
```

## Final Data Schema for Instruction finetuning
```
{
  "instruction": "...",
  "input": "English sentence",
  "output": "Generated Hinglish",
  "control": {...},
  "domain": "casual_chat"
}
```

```
@misc{hinglishcodemix2025,
  title={Hinglish Code-Mix Data Generation with LLM-Based Correction},
  author={Nilagnik Chakraborty},
  year={2025},
}
```

