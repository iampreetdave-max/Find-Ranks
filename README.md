# Find-Ranks

A Streamlit app that extracts student marks from exam PDFs, aggregates results across multiple exams, and produces cumulative rankings you can download as CSV.

![Python](https://img.shields.io/badge/python-3.x-blue)
![Streamlit](https://img.shields.io/badge/Streamlit-app-FF4B4B)

## Overview

Find-Ranks turns a stack of exam mark-sheet PDFs into a clean, ranked leaderboard. Upload one or more PDFs and the app parses each page for student enrollment numbers, names, and section-wise marks, combines the records by student across all uploaded exams, and computes cumulative totals, per-exam averages, and ranks. Results are presented as interactive tables and simple charts, and every view can be exported to CSV.

It is built for a specific mark-sheet layout (12-digit enrollment numbers with Section A / Section B / Total columns) and uses tolerant pattern matching to handle real-world PDF text extraction, including absent (`AB`) students.

## Key Features

- **Multi-PDF upload** and batch processing with per-file progress and status feedback
- **Automatic extraction** of enrollment number, name, Section A, Section B, and total marks via text pattern matching
- **Marks validation** — totals are sanity-checked against the sum of sections (1-mark tolerance)
- **Absent handling** — students marked `AB` are recorded with zero marks and an `Absent` status
- **Cumulative rankings** across all uploaded exams, including exam count and average-per-exam
- **Exam-wise comparison** view that pivots each student's score per source file side by side
- **Visualizations** — top-10 bar chart, score distribution, and average performance per exam
- **CSV export** for rankings, exam-wise comparison, and raw extracted records
- **Keep-alive script** to periodically wake a hosted Streamlit deployment

## How It Works

1. `extract_marks_from_pdf` opens each PDF with `pdfplumber` and scans line by line, matching enrollment-number, name, and marks patterns with regular expressions.
2. `process_multiple_pdfs` runs the extractor over every uploaded file and concatenates the records into a single DataFrame.
3. `calculate_cumulative_rankings` groups records by enrollment number, sums marks, counts exams, computes averages, and ranks students by cumulative total.
4. `create_exam_wise_view` pivots the data so each exam appears as its own column for side-by-side comparison.
5. The Streamlit UI renders statistics, ranked tables (top 3 highlighted), charts, and download buttons.

`keep-alive.py` is a standalone utility that uses Playwright to load a deployed Streamlit URL headlessly and hold the session open, preventing the hosted app from sleeping.

## Tech Stack

- **Framework:** Streamlit
- **Data:** pandas
- **PDF parsing:** pdfplumber (with PyPDF2 available)
- **Keep-alive:** Playwright

## Getting Started

### Prerequisites

- Python 3.x
- pip

### Install

```bash
pip install -r requirements.txt
```

### Run

```bash
streamlit run app.py
```

Then open the URL Streamlit prints (default `http://localhost:8501`), upload your exam PDFs, and click **Process Files & Calculate Rankings**.

### Keep-Alive (optional)

The keep-alive script targets the hosted app URL defined inside the file (`APP_URL`). Update that value to your own deployment before running:

```bash
pip install playwright
playwright install chromium
python keep-alive.py
```

## Expected PDF Format

The extractor is tuned for mark sheets containing:

- A **12-digit enrollment number** (pattern `2[34]14XXXXXXXX`)
- A **student name**
- **Section A** and **Section B** marks
- A **total marks** value (validated against Section A + Section B)

## Project Structure

```
Find-Ranks/
├── app.py              # Streamlit app: extraction, ranking, UI, exports
├── keep-alive.py       # Playwright script to keep a hosted deployment awake
├── requirements.txt
└── LICENSE
```

## License

See the [LICENSE](LICENSE) file in this repository.
