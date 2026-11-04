# BITACORA

Log of what we did and why. Newest entry first. Updated before each commit when a decision was made.
Entry format: what was done, decisions and why, alternatives rejected, where we stopped.

---

## 2026-11-04: Load and first look

### What was done

- Imported pandas, matplotlib, seaborn and loaded `data/survey.csv` with `na_values=["NA"]`.
- First look: `head`, `shape`, `info`, `isna().sum()`, `describe` (numeric and text), `columns`, with a short Spanish note after each output.
- Wrote a personal column dictionary (`COLUMNAS_PERSO.md`), kept out of git through `.git/info/exclude`.

### Decisions and why

- **`na_values=["NA"]` at load time:** otherwise pandas reads "NA" as text and the missing values are invisible.
- **Age outliers only spotted, not fixed:** -1726, -29, -1, 5, 8, 11, 329 and 99999999999. They break the mean (7.9e+07) but not the median (31). Cleaning belongs to the data quality issue.
- **`describe(include="str")`:** pandas 3 deprecates `include="object"` for text columns.
- **`treatment` is balanced** (about 51% Yes), so no need to rebalance.
- **Notebook merged with known typos** in a few Spanish notes and 3 empty Markdown cells; to be fixed later.

### Alternatives rejected

- None: only exploration, no modelling choice yet.

### Where we stopped

- Idea to refine the questions: move `no_employees` and `remote_work` from question 3 to question 1 (factors of `treatment`, together with `family_history`). Next: branch `docs/refine-questions`, then the data quality issue.

## 2026-11-04: Starting questions

### What was done

- Chose the 3 starting questions and wrote them in the notebook (Spanish).

### Decisions and why

- **Questions:** who seeks treatment, does employer support matter, who fears talking about it. They use different column families and all revolve around `treatment`.
- **Notebook, README and report in Spanish:** the teacher reads them. Code, commits, issues and the bitácora stay in English.
- **Not used as a main question:** `work_interfere` (264 empty values, better handled during cleaning) and country comparison (almost all answers come from the US, UK and Canada).

### Where we stopped

- Next: issue "Notebook setup, load the data, first look" (`feature/load-and-explore`).

## 2026-11-04: Project setup

### What was done

- Read the brief (BRIEFING.md, renamed from README.md so the repo can have its own README).
- Chose the dataset: OSMI "Mental Health in Tech Survey" (`data/survey.csv`).
- Connected the repo to Charlotte's own GitHub repo (`origin`), kept the bootcamp repo as `upstream`.
- Created `dev` and the first feature branch; merged the setup PR into `dev`.
- Created the Python venv, `requirements.txt` and `.gitignore`.
- Created 15 issues (Essential and Medio) in GitHub Project #51, with level labels and a branch name in each.

### Decisions and why

- **Dataset: OSMI survey.** Real answers, 1259 rows, 27 columns, dirty on purpose (ages like -1726, 49 spellings of gender, many missing values). Fits the brief, which asks for real, messy data.
- **Target variable: `treatment`.** All three starting questions revolve around it, so the story stays simple.
- **Dependencies: only what we use now** (pandas, matplotlib, seaborn, ipykernel). Easier to understand and to explain in the README. plotly, requests, streamlit, pytest only when a level needs them.
- **Tasks in the GitHub Project only.** No TICKETS.md and no OpenSpec: one list to keep up to date, and OpenSpec is heavy for a one-week solo EDA.
- **Branches:** `main` stable, `dev` for work in progress, one `feature/...` or `docs/...` branch per issue, PR into `dev`. One branch at a time because the whole analysis lives in one notebook (avoids merge conflicts).
- **Python 3.13** instead of the bootcamp's 3.12: the brief only asks for Python 3.

### Alternatives rejected

- `mental-health-in-tech-survey-20142025` (abbas829): same data as the OSMI file (identical hash), only a misleading title.
- `mental_health_tech.csv` (5000 rows, 2019-2024): probably synthetic. Only 13 distinct comments, 4 clean genders, no absurd ages, nothing missing except comments. Not real data.
- An `.Rmd` found online: someone else's EDA in R, used only as inspiration, not as a base.
- Other themes considered: fertility (INE births in Spain, World Bank rates), Stack Overflow survey, Inside Airbnb Barcelona.

### Where we stopped

- Notebook kernel being set to `.venv` in VS Code.
- Next: issue "Define the starting questions" (`feature/starting-questions`), then "Notebook setup, load the data, first look".
