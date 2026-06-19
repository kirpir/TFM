# TFM - Trust Me, I'm a Machine
## Rhetoric, Affect, and Identity in Human, Aligned, and Uncensored LLM Phishing

MSc Computational Social Science - Universidad Carlos III de Madrid (UC3M)

---

## Overview

This repository contains all code and data for the thesis *"Trust Me, I'm a Machine: Rhetoric, Affect, and Identity in Human, Aligned, and Uncensored LLM Phishing"*. The project presents a computational sociolinguistic analysis of phishing emails produced by three categories of producing agent: human authors, commercially aligned large language models (LLMs), and uncensored LLMs.

Three analytical dimensions are examined:
- **Dimension 1 - Stylometric Fingerprint:** MTLD, Flesch-Kincaid Grade, function word rate, passive voice rate, exclamation rate
- **Dimension 2 - Affective Register:** NRC Emotion Lexicon, sentimentr valence scoring
- **Dimension 3 - Social Identity Construction:** Modal constructions, imperative constructions, ConvoKit politeness strategies

Finally, the data generated within the analytical dimensions is used as a feature matrix for conducting source classification using random forest and naive Bayes classifiers.

---

## Data Sources

### Source A - Human Phishing (Nazario Corpus)
The human phishing corpus is derived from the Nazario Phishing Corpus (2015–2021), a publicly available dataset of real-world phishing emails. The raw Nazario corpus is available at https://monkey.org/~jose/phishing/.

### Source B - Aligned LLM Phishing
1,089 phishing emails generated using three commercially aligned LLMs:
- GPT-4o (OpenAI, temperature 1.2)
- Claude Sonnet 4.6 (Anthropic, temperature 1.0)
- Gemini 2.5 Flash (Google, temperature 1.4)

Generation required API access to OpenAI, Anthropic, and Google Gemini. See **API Setup** below.

### Source C - Uncensored LLM Phishing
1,089 phishing emails generated using three uncensored LLMs via Ollama:
- Llama 2 Uncensored
- Dolphin-Mistral
- Dolphin-Llama3

All models run locally via Ollama. See **Ollama Setup** below.

---

## Software Requirements

### R
- R version 4.5 or higher
- RStudio recommended

Required R packages:
```r
install.packages(c(
  "tidyverse", "tidytext", "koRpus", "koRpus.lang.en",
  "syuzhet", "sentimentr", "politeness", "quanteda",
  "reticulate", "ranger", "caret", "naivebayes",
  "pROC", "rstatix", "FSA", "knitr",
  "kableExtra", "ggcorrplot", "cld3", "quanteda.textstats",
  "base64enc", "ggdendro"
))

# GitHub packages
remotes::install_github("kbenoit/quanteda.dictionaries")
```

### Python
- Python 3.10 or higher
- Virtual environment recommended

Required Python packages:
```bash
pip install spacy textstat convokit pandas numpy
python -m spacy download en_core_web_sm
```

### Ollama (for Source C generation only)
Install Ollama from [https://ollama.com](https://ollama.com) and pull the required models:
```bash
ollama pull llama2-uncensored
ollama pull dolphin-mistral
ollama pull dolphin-llama3
```

---

## API Setup

Source B generation requires API keys for OpenAI, Anthropic, and Google Gemini. Keys should be stored as environment variables and never hardcoded:

```r
# Add to your .Renviron file
OPENAI_API_KEY=your_key_here
ANTHROPIC_API_KEY=your_key_here
GOOGLE_API_KEY=your_key_here
```

To edit your `.Renviron` file in R:
```r
usethis::edit_r_environ()
```

---

## Reproducing the Analysis

### Before You Begin

1. Clone the repository and open `TFM.Rproj` in RStudio - this automatically sets the working directory correctly
2. Run the `{r directory_setup}` chunk in `01_data_generation.Rmd` - this creates the required directory structure automatically
3. Download the Nazario Phishing Corpus `.txt` files from [https://monkey.org/~jose/phishing/](https://monkey.org/~jose/phishing/) and place them in `data/raw/`
4. Add your API keys to `.Renviron` (see **API Setup** above). You have to have set up API keys for OpenAI, Anthropic, and Google Gemini via their respective platforms.

Once these steps are complete you are ready to run the pipeline.

The full project is split across two R Markdown documents that must be run in order.

### Step 1 - Data Generation (`01_data_generation.Rmd`)

This document covers:
1. Source A cleaning and preprocessing
2. BERTopic scenario taxonomy derivation
3. Source B generation via aligned LLM APIs
4. Source C generation via Ollama
5. Corpus combination and post-processing

**Note:** Full regeneration of Sources B and C requires API access and Ollama setup. The generated corpora are included in `data/generated/` so this step can be skipped if you only want to reproduce the analysis.

### Step 2 - Analysis (`02_analysis.Rmd`)

This document covers:
1. Corpus loading and preprocessing
2. Descriptive analysis
3. Dimension 1: Stylometric Fingerprint
4. Dimension 2: Affective Register
5. Dimension 3: Social Identity Construction
6. Source classification experiment

**To run the analysis from scratch:**
1. Open `02_analysis.Rmd` in RStudio
2. Ensure the Python virtual environment is activated (the document uses `reticulate`)
3. Run all chunks in order from top to bottom

**Important:** The analysis document uses `reticulate` to run Python chunks within R Markdown. The Python virtual environment must be initialised before any other chunks run. The first chunk in the document handles this automatically:

```r
library(reticulate)
use_virtualenv("tfm_env", required = TRUE)
```

---

## Python Virtual Environment Setup

```bash
# create virtual environment
python -m venv tfm_env

# activate (macOS/Linux)
source tfm_env/bin/activate

# activate (Windows)
tfm_env\Scripts\activate

# install packages
pip install spacy textstat convokit pandas numpy
python -m spacy download en_core_web_sm
```

---

## Notes on Reproducibility

- **Random seed:** All stochastic processes use `set.seed(42)` in R for reproducibility
- **Model versions:** LLM outputs are sensitive to model version. Sources B and C were generated in May–June 2026. Future model updates may produce different outputs
- **Ollama models:** Uncensored model outputs may vary across hardware and Ollama versions
- **API rate limits:** Source B generation involves approximately 1,089 API calls per model. Be aware of rate limits and costs
- **ConvoKit:** The politeness analysis requires ConvoKit's TextParser which runs spaCy dependency parsing on all emails. This may take several minutes on the full corpus
- **MTLD:** koRpus MTLD computation is memory intensive. If R crashes, rerun using the batched approach documented in the analysis file

---

## Disclaimer
This project was developed exclusively for academic purposes to fulfil the TFM requirements for the Master in Computational Social Sciences at Universidad Carlos III de Madrid. The code and data provided are intended for research and educational use only. Commercial use is strictly prohibited.

## Contact

For questions about this project please open an issue in this repository.
