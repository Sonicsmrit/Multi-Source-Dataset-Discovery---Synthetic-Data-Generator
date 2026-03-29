<div align="center">

```
██████╗  █████╗ ████████╗ █████╗ ███████╗███████╗████████╗
██╔══██╗██╔══██╗╚══██╔══╝██╔══██╗██╔════╝██╔════╝╚══██╔══╝
██║  ██║███████║   ██║   ███████║███████╗█████╗     ██║   
██║  ██║██╔══██║   ██║   ██╔══██║╚════██║██╔══╝     ██║   
██████╔╝██║  ██║   ██║   ██║  ██║███████║███████╗   ██║   
╚═════╝ ╚═╝  ╚═╝   ╚═╝   ╚═╝  ╚═╝╚══════╝╚══════╝   ╚═╝   
                                                            
██████╗ ██╗███████╗ ██████╗ ██████╗ ██╗   ██╗███████╗██████╗ ██╗   ██╗
██╔══██╗██║██╔════╝██╔════╝██╔═══██╗██║   ██║██╔════╝██╔══██╗╚██╗ ██╔╝
██║  ██║██║███████╗██║     ██║   ██║██║   ██║█████╗  ██████╔╝ ╚████╔╝ 
██║  ██║██║╚════██║██║     ██║   ██║╚██╗ ██╔╝██╔══╝  ██╔══██╗  ╚██╔╝  
██████╔╝██║███████║╚██████╗╚██████╔╝ ╚████╔╝ ███████╗██║  ██║   ██║   
╚═════╝ ╚═╝╚══════╝ ╚═════╝ ╚═════╝   ╚═══╝  ╚══════╝╚═╝  ╚═╝   ╚═╝  
```

A terminal-native, AI-powered pipeline for discovering, cleaning, and synthesizing datasets — from Hugging Face & Kaggle to production-ready synthetic data.

![Python](https://img.shields.io/badge/Python-3.10%2B-magenta?style=for-the-badge&logo=python&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-pink?style=for-the-badge)
![SDV](https://img.shields.io/badge/SDV-Synthetic%20Data%20Vault-blueviolet?style=for-the-badge)
![HuggingFace](https://img.shields.io/badge/%F0%9F%A4%97-Hugging%20Face-yellow?style=for-the-badge)
![Kaggle](https://img.shields.io/badge/Kaggle-20BEFF?style=for-the-badge&logo=Kaggle&logoColor=white)
![Groq](https://img.shields.io/badge/Groq-AI%20Scoring-orange?style=for-the-badge)

</div>

---

## Overview

Multi-Source Dataset Discovery & Synthetic Data Generator (Dataset Discovery) is an end-to-end CLI pipeline that handles the entire dataset lifecycle — from search and discovery to automated cleaning and synthetic data generation. It searches across Hugging Face and Kaggle simultaneously, uses a Groq LLM to intelligently rank results by relevance, lets you preview and download datasets directly, then automatically cleans them and generates up to 40,000 rows of synthetic data using state-of-the-art generative models.

Everything runs from your terminal with a rich, styled interface — no notebooks, no GUIs, no hassle.

---

## Features

- Multi-Source Search — Queries Hugging Face (top 100) and Kaggle (top 5 pages) simultaneously with async execution
- AI-Powered Scoring — Uses Groq's LLaMA 4 Scout to evaluate dataset relevance against your query, combined with a weighted scoring formula factoring in downloads, recency, and size
- Interactive Preview — Stream the first 10 rows of any Hugging Face dataset or list all files of a Kaggle dataset before downloading
- One-Click Download — Downloads and unzips Kaggle datasets directly into an organized `Downloads/` directory
- Automated Cleaning — Removes duplicates, drops empty columns/rows, fills missing values (median for numerics, `"Unknown"` for categoricals), and strips whitespace
- Synthetic Data Generation — Three SDV model options to synthesize up to 40,000 rows per file, preserving statistical distributions
- Organized File Structure — Outputs cleanly separated into `Downloads/`, `Cleaned/`, and `Synthetic/` directories
- Beautiful Terminal UI — Fully styled with Rich panels, tables, spinners, and magenta/pink color theming throughout

---

## Pipeline Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     DATASET DISCOVERY                       │
│                                                             │
│   User Query                                                │
│       │                                                     │
│       ├──────────────────────────────────────┐              │
│       ▼                                      ▼              │
│  Hugging Face API                       Kaggle API          │
│  (top 100, by downloads)            (top 5 pages, hottest)  │
│       │                                      │              │
│       └──────────────┬───────────────────────┘              │
│                      ▼                                      │
│             Groq LLaMA 4 Scout                              │
│           (AI Relevance Scoring)                            │
│                      │                                      │
│                      ▼                                      │
│           Weighted Score Formula                            │
│     (AI × 0.30 + Size × 0.25 + Downloads × 0.25 +           │
│                   Recency × 0.10 + Likes × 0.10)            │
│                      │                                      │
│                      ▼                                      │
│            Top 30 Results Table                             │
│                      │                                      │
│          ┌───────────┴───────────┐                          │
│          ▼                       ▼                          │
│      Preview                 Download                       │
│   (stream / list)         (Downloads/)                      │
│                                 │                           │
└─────────────────────────────────┼───────────────────────────┘
                                  │
┌─────────────────────────────────▼───────────────────────────┐
│                      CLEANING PIPELINE                      │
│                                                             │
│   Downloads/ ──► Remove Duplicates                          │
│              ──► Drop Empty Cols/Rows                       │
│              ──► Fill Missing Values                        │
│              ──► Strip Whitespace                           │
│              ──► Save to Cleaned/                           │
│                                                             │
└─────────────────────────────────┬───────────────────────────┘
                                  │
┌─────────────────────────────────▼───────────────────────────┐
│                   SYNTHETIC DATA PIPELINE                   │
│                                                             │
│   Cleaned/ ──► SDV Metadata Detection                       │
│             ──► Sample up to 10,000 rows                    │
│             ──► Fit chosen model                            │
│             ──► Generate 40,000 synthetic rows              │
│             ──► Save to Synthetic/                          │
│                                                             │
│   Models:  [1] GaussianCopula  [2] CTGAN  [3] TVAE          │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Project Structure

```bash
dataset-discovery/
│
├── main.py                  # Entry point — runs the full pipeline
├── CLI.py                   # Search logic, Groq AI scoring, Kaggle + HF API setup
├── DisplayResults.py        # Display, preview, and download interface
├── cleaning.py              # Data cleaning pipeline and file management
├── synthetic.py             # Synthetic data generation with SDV models
│
├── .env                     # API keys (not committed)
├── requirements.txt         # Python dependencies
│
├── Downloads/               # Raw downloaded datasets (auto-created)
├── Cleaned/                 # Cleaned datasets (auto-created)
└── Synthetic/               # Synthetic datasets (auto-created)
```

---

## Installation

1. Clone the repository

```bash
git clone https://github.com/yourusername/dataset-discovery.git
cd dataset-discovery
```

2. Install dependencies

```bash
pip install -r requirements.txt
```


Then fill in your API keys:

```bash
KAGGLE_USERNAME=your_kaggle_username
KAGGLE_KEY=your_kaggle_api_key
HF_TOKEN=your_huggingface_token
GROQ_API_KEY=your_groq_api_key

```

---

## Usage

Run the full pipeline with:

```bash
python main.py
```

The pipeline runs in three sequential stages. Each stage is interactive and prompts you for input.

### Stage 1 — Search & Download

<<<<<<< HEAD
```
🔍︎ Search Datasets:
```
=======

<img width="1858" height="582" alt="image" src="https://github.com/user-attachments/assets/935c4a66-6f04-4de3-93ac-35dc69a21bae" />
<img width="1327" height="844" alt="image" src="https://github.com/user-attachments/assets/ecc1bf00-ca71-424d-b5ea-7d0335fa2250" />

>>>>>>> 4c46170cc4870878eb759665fae96011b3cd5fb8

The tool searches both Hugging Face and Kaggle, scores all results with AI, and presents a ranked table of up to 30 datasets. You can then:

- Preview — Enter the serial numbers of datasets to stream a sample or list files
- Download — Enter serial numbers to download directly to `Downloads/`
- Skip — Type `skip` to bypass either step

### Stage 2 — Clean

Select which downloaded datasets to clean. The cleaner will:

- Remove duplicate rows
- Drop columns/rows that are entirely empty
- Fill missing numeric values with the column median
- Fill missing categorical values with `"Unknown"`
- Strip leading/trailing whitespace from string columns

Cleaned files are saved to `Cleaned/` mirroring the `Downloads/` folder structure. You'll also be prompted whether to delete the original downloads to save space.

### Stage 3 — Synthesize

Select which cleaned datasets to synthesize and choose a model:

| Option | Model | Best For |
|--------|-------|----------|
| `1` | GaussianCopulaSynthesizer | Fast generation, tabular data with numeric correlations |
| `2` | CTGANSynthesizer | Complex distributions, mixed data types (requires epochs) |
| `3` | TVAESynthesizer | Deep statistical fidelity, smaller datasets (requires epochs) |

For CTGAN and TVAE, you'll be prompted to set the number of training epochs (default: 300). The synthesizer fits on up to 10,000 sampled rows and generates 40,000 synthetic rows per file.

---

## Scoring Formula

Each dataset is scored out of 10 using a weighted combination of signals:

```
Final Score = AI Score × 0.30
            + Size Score × 0.25
            + Download Score × 0.25
            + Recency Score × 0.10
            + Likes/Votes Score × 0.10
```

If the AI scoring fails (e.g. API error), the formula falls back to:

```
Final Score = Download Score × 0.30
            + Size Score × 0.30
            + Recency Score × 0.25
            + Likes Score × 0.15
```

Score colors in the results table: 🟢 ≥ 7.0 &nbsp;|&nbsp; 🟡 ≥ 4.0 &nbsp;|&nbsp; 🔴 < 4.0

---

## Supported File Formats

| Format | Search | Preview | Download | Clean | Synthesize |
|--------|:------:|:-------:|:--------:|:-----:|:----------:|
| `.csv` | ✅ | ✅ | ✅ | ✅ | ✅ |
| `.json` | ✅ | ✅ | ✅ | ✅ | ✅ |
| `.xlsx` | ✅ | ✅ | ✅ | ✅ | ✅ |
| `.parquet` | ✅ | ✅ | ✅ | ✅ | ✅ |

---


## Getting API Keys

Kaggle
1. Log in to [kaggle.com](https://www.kaggle.com) and go to Account Settings
2. Scroll to the API section and click Create New API Token

Hugging Face
1. Log in to [huggingface.co](https://huggingface.co) and go to Settings → Access Tokens
2. Click New token, set role to `read`, and copy the token into `HF_TOKEN`

Groq
1. Sign up at [console.groq.com](https://console.groq.com)
2. Navigate to API Keys and click Create API Key
3. Copy into `GROQ_API_KEY`

---

## Notes & Limitations

- Hugging Face downloads — Due to the variety of HF dataset formats and authentication requirements, direct downloads are not automated. The tool will print the dataset URL so you can download manually.

---

## License

This project is licensed under the MIT License.

---
