# Capstone Project

This project brings together three separate pieces of work into one repository: a data pipeline, an analytics workflow, and a support assistant. It was built to show how a real end-to-end system can be structured around data collection, analysis, and a lightweight AI-style interface.

The repo is organized into:

- `data_pipeline/` — scrapes book data, cleans it, and stores it in SQLite
- `analytics/` — loads Titanic data, explores it, and trains classification models
- `support_assistant/` — a FastAPI-based policy assistant for Zepto-style queries

Everything is managed from a single root-level `requirements.txt`, and the support assistant also includes a Dockerfile for easy containerized deployment.

## Project structure

```text
.
├── README.md
├── COLAB_RUN.md
├── requirements.txt
├── data_pipeline/
│   ├── pipeline.py
│   ├── books.db
│   ├── clean_books.csv
│   ├── query_outputs.txt
│   └── README.md
├── analytics/
│   ├── analysis.py
│   ├── titanic.csv
│   ├── eda_report.txt
│   ├── classification_metrics.csv
│   ├── best_pipeline.joblib
│   ├── README.md
│   └── figures/
├── support_assistant/
│   ├── main.py
│   ├── Dockerfile
│   ├── README.md
│   └── docs/
│       ├── doc_01.txt
│       ├── doc_02.txt
│       └── ...
└── .gitignore
```

## What each part does

### Data pipeline

The data pipeline gathers book information from Books to Scrape, cleans the values, converts GBP prices to INR using the fixed rate of `1 GBP = 105.50 INR`, and saves the transformed data into a SQLite database. It also writes query evidence to `data_pipeline/query_outputs.txt` so the output can be reviewed and validated.

### Analytics pipeline

The analytics module loads Titanic data, performs exploratory data analysis, investigates data quality issues, compares a few classification models, and saves the best one using a joblib pipeline. It also creates charts and metrics files for reporting.

### Support assistant

The support assistant is an offline-first app that answers policy-related questions using local documents. It reads the policy text from `support_assistant/docs`, routes the request based on intent, retrieves relevant context, and returns a JSON response containing `answer`, `sources`, and `confidence`.

## Prerequisites

- Python 3.11 or newer
- Internet access for the web scrape and initial dataset loading
- Docker is optional, but useful for the support assistant container

## Setup

From the root of the project, run:

```powershell
py -m venv .venv
Set-ExecutionPolicy -Scope Process Bypass
.\.venv\Scripts\Activate.ps1
py -m pip install -r requirements.txt
```

## Running the project

### 1. Run the data pipeline

```powershell
py data_pipeline/pipeline.py
```

This creates the database and cleaned data outputs in the `data_pipeline/` folder.

### 2. Run the analytics pipeline

```powershell
py analytics/analysis.py
```

This produces the Titanic analysis report, metrics CSV, model artifact, and figures in the `analytics/` folder.

### 3. Start the support assistant

```powershell
uvicorn support_assistant.main:app --reload
```

Then test it with:

```powershell
curl http://127.0.0.1:8000/ask -H "Content-Type: application/json" -d "{\"query\":\"What is the delivery policy?\"}"
```

You should get a JSON response with the answer and supporting sources.

## Docker

The assistant can also be run in a container:

```powershell
docker build -f support_assistant/Dockerfile -t zepto-support .
docker run -p 7860:7860 zepto-support
```

This exposes the same API on port `7860`.

## Colab runbook

If you want to run everything in Google Colab, there is a step-by-step guide in [COLAB_RUN.md](COLAB_RUN.md).

## More detailed documentation

For module-specific notes, see:

- [data_pipeline/README.md](data_pipeline/README.md)
- [analytics/README.md](analytics/README.md)
- [support_assistant/README.md](support_assistant/README.md)

## Notes

- The scraping step depends on internet access.
- The analytics pipeline creates plots and model artifacts inside the `analytics/figures/` folder.
- The support assistant is designed to stay deterministic and offline-first unless a real LLM is explicitly enabled.
- Run commands from the repository root so that relative paths resolve correctly.
