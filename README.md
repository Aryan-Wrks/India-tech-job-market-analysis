# Job Market Intelligence Dashboard

A data pipeline that collects real tech job postings from the Adzuna API, stores them in SQL, analyzes hiring trends with Pandas, and uses an LLM (Gemini) to extract the specific tools and technologies employers ask for.

**Full analysis:** open [`analysis.ipynb`](analysis.ipynb) (charts and written insights included).

## Key findings

- **Python (54) and SQL (44)** are the most requested skills, followed by T-SQL, Django, FastAPI and SQL Server.
- **Karnataka leads hiring** with 71 of 200 postings, about double Maharashtra (35).
- **107 of 200 postings** named at least one concrete tool; the rest used generic wording.
- Only **20% of postings include a salary**, so salary findings are a rough signal only.

![Top skills](chart_top_skills.png)

| Top states | Top companies |
|---|---|
| ![States](chart_top_states.png) | ![Companies](chart_top_companies.png) |

## How it works

```
Adzuna API -> raw JSON -> SQLite database -> Pandas analysis -> charts
                                  |
                          Gemini LLM extracts skills -> back into SQL
```

| Script | What it does |
|---|---|
| `fetch_jobs.py` | Calls the Adzuna API and saves raw JSON responses |
| `build_db.py` | Creates the SQLite tables (`jobs`, `skills`) |
| `load_data.py` | Cleans and loads jobs into SQL; safe to re-run, duplicates are skipped |
| `ai_extract.py` | Sends each description to Gemini and stores the extracted skills |
| `analyze.py` | Builds the charts with Pandas, Seaborn and Matplotlib |
| `analysis.ipynb` | The full write-up: data quality, charts, insights, limitations |

## Tech stack

Python, requests, SQLite, Pandas, Seaborn, Matplotlib, Jupyter, Gemini API

## Run it yourself

1. Install dependencies: `pip install -r requirements.txt`
2. Get a free key from [Adzuna](https://developer.adzuna.com) and one from [Google AI Studio](https://aistudio.google.com).
3. Copy `config_example.py` to `config.py` and fill in your keys.
4. Run in order:
```
   python fetch_jobs.py
   python build_db.py
   python load_data.py
   python ai_extract.py
```
5. Open `analysis.ipynb`.

Note: Gemini model names change often. If you get a "model no longer available" error, update `MODEL` in `ai_extract.py` to the model the error message suggests.

## Limitations

- Small single snapshot: 200 postings, one country, one point in time
- Results reflect my four search terms, not the whole job market
- Adzuna truncates descriptions to 500 characters, so some tools are never seen
- Skill names are not normalised (SQL, T-SQL and SQL Server count separately)
- Some postings show old creation dates (2019-2021), likely reposted listings

## What I learned

I learned that real data is messy and that every result needs checking: duplicate postings, inconsistent job titles, and a first AI prompt that returned only generic skills all taught me to verify my data before trusting my charts.