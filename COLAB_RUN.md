# Google Colab Runbook

This is the easiest way to run the full project in a Colab notebook without setting up anything locally. Just open a new notebook and run each cell in order.

## 1. Clone the repository

```python
!git clone https://github.com/shanmukh-29/Capstone_Project.git
%cd Capstone_Project
```

## 2. Install the project dependencies

```python
!pip install -q -r requirements.txt
```

This installs the packages needed for the data pipeline, analytics scripts, and the FastAPI support assistant.

## 3. Run the data pipeline

```python
!python data_pipeline/pipeline.py
```

After it finishes, you can confirm the expected files were created:

```python
from pathlib import Path

for path in [
    Path("data_pipeline/books.db"),
    Path("data_pipeline/clean_books.csv"),
    Path("data_pipeline/query_outputs.txt"),
]:
    print(path, path.exists())
```

This step scrapes book data, cleans it, converts the prices, and stores the results in SQLite.

## 4. Run the analytics workflow

```python
!python analytics/analysis.py
```

To check that the analytics outputs were generated successfully:

```python
for path in [
    Path("analytics/titanic.csv"),
    Path("analytics/eda_report.txt"),
    Path("analytics/classification_metrics.csv"),
    Path("analytics/best_pipeline.joblib"),
]:
    print(path, path.exists())

print("\n".join(sorted(str(p) for p in Path("analytics/figures").glob("*.png"))))
```

This script explores the Titanic dataset, builds and compares models, and saves the best pipeline along with charts and a summary report.

## 5. Run the support assistant directly

```python
!python support_assistant/main.py
```

This prints a quick example of the assistant answering a policy-style question.

## 6. Start the API in the notebook

```python
!uvicorn support_assistant.main:app --host 0.0.0.0 --port 8000 &
```

Once the app starts, you can send a request to the `/ask` endpoint:

```python
import requests

response = requests.post(
    "http://127.0.0.1:8000/ask",
    json={"query": "What is the delivery policy?"},
)
print(response.json())
```

You should get a JSON response with the answer, sources, and confidence score.

## Notes

Colab has internet access, so the book scraping and the initial Titanic dataset download should work without extra setup. The generated outputs remain in the notebook runtime until you download them or save them back to GitHub.

If you want to run the project again later, just start from the repository clone step and rerun the cells in order.
