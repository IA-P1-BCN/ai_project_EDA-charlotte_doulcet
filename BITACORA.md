# BITACORA

Log of what we did and why. Newest entry first. Updated before each commit when a decision was made.
Entry format: what was done, decisions and why, alternatives rejected, where we stopped.

---

## 2026-11-05: Data cleaning

### What was done

- Made `df_clean = df.copy()`; all cleaning happens on the copy, `df` and `data/survey.csv` stay raw.
- Applied the data quality decisions: dropped `comments` and `state`, filled `work_interfere` with `No aplica` and `self_employed` with `Desconocido`. No empty cells left except the ones created below.
- `Age`: values outside 18-80 set to NaN (8 rows). Mean went from 7.9e+07 to 32.1, min 18, max 72, dtype float64.
- `Gender` cleaned and `identities` created: Male 994, Female 247, Trans / non-binary / other 14, NaN 4 (total 1259).
- Question 3 now compares `mental_health_consequence`, `supervisor` and `coworkers` by `anonymity` (was `tech_company`).
- Reorganised the notebook into fixed sections 1-9 (intro, questions, load, first look, data quality, cleaning, and placeholders for the rest), with a Spanish note after each step.

### Decisions and why

- **`Age` valid range 18-80 (Charlotte's choice).** People answering are working adults. Out-of-range values become NaN and the row is kept: the other answers are still useful and we do not invent an age.
- **`Gender`: keep the column, fix only what is clear.** Classify by what the person wrote, not by our guess.
  - Typos, case and `cis` variants become `Male` or `Female` (`cis male`, `male (cis)`, `cis man`, `cis female`, `female (cis)`, `cis-female/femme`, `make`, `maile`, `femail`...).
  - Hedged answers that contain the word male (`Male-ish`, `Guy (-ish)`, `something kinda male?`, `ostensibly male...`) go to `Male`: they wrote male.
  - Answers that are not a gender (`Nah`, `All`, `p`, `A little about you`) become NaN.
  - Trans, non-binary, fluid, queer, agender, etc. stay untouched in `Gender`, as written.
  - `male leaning androgynous` goes to the third group (Charlotte's choice): `androgynous` is the identity, `male leaning` only a tendency. So it stays untouched in `Gender`.
- **New column `identities` (name given by the teacher), 3 values:** `Male`, `Female`, `Trans / non-binary / other` (NaN where `Gender` is NaN). Analyses use `identities`; `Gender` stays for detail. Trans women and trans men go in the third group by choice, not because they are not women or men; to say so in the notebook.
- **Full words `Male` and `Female`, not `M` and `F`:** readable in plots and coherent with the other columns (`Yes`, `No`).
- **Question 3 by `anonymity`, no rows dropped:** about 18% of respondents are not in a tech company, but dropping those 228 rows loses real answers. Anonymity is what the question is really about. Only 65 people answered `No` to anonymity, so that group is small.
- **`Timestamp`: convert to datetime and keep, but no trend analysis.** 1190 of 1259 answers are from 2014 (1135 in August 2014), 68 from 2015 and 1 from 2016. Comparing years would not be reliable. We keep the column (the brief asks to fix types, and dropping it loses information); for the "trends or group comparison" requirement we compare groups instead. To state in the limits section.
- **Small groups:** 14 answers in the third group out of 1259, so results for the third group are indicative only.
- **Why not 3 categories with everything else in `Other`:** it mixed real identities with non-answers.

### Alternatives rejected

- Trans women in `Female`: clean on paper, but Charlotte preferred to keep trans people together in one visible group.
- Mapping non-answers to `Other`: would count `p` as an identity.
- Deleting rows with absurd ages: loses valid answers.

### Where we stopped

- Added section 5.5 (data types) and 6.5 (`Timestamp` to datetime) in the notebook.
- Saved `data/survey_clean.csv` (1259 rows, 26 columns, `index=False`). A CSV does not keep types, so reading it back needs `parse_dates=["Timestamp"]`.
- Issue done. Next: univariate analysis.
- To ask the teacher: drop `Gender` or keep it in the clean file? For now both columns are kept (dropping later is one line).
- The notebook runs top to bottom with no error (Run All).

## 2026-11-04: Data quality

### What was done

- Counted and plotted missing values (table and heatmap), checked duplicates (none), checked why the gaps exist.
- Wrote the decision table (column, % missing, decision, reason) in the notebook. Nothing is modified yet.

### Decisions and why

- **The gaps have causes, they are not random:** `state` is only asked in the US, the 18 empty `self_employed` are the first rows (question added later), 260 of 264 empty `work_interfere` are people without treatment.
- **`comments` and `state`: drop the column.** Free text almost empty; we compare countries, not states.
- **`work_interfere`: keep, fill with `No aplica`.** Dropping rows would remove 42% of the people without treatment and bias the comparison; pandas and seaborn also ignore NaN silently in plots.
- **`self_employed`: keep, fill with `Desconocido`.** Only 18 rows and we do not know the answer, so we do not invent one.
- Decisions are applied in the cleaning issue, not here.

### Alternatives rejected

- Filling `self_employed` with the most frequent value (`No`): probable but invented.
- Dropping rows with empty `work_interfere`: biased.

### Where we stopped

- Next: issue on cleaning (ages, gender, applying today's decisions).

## 2026-11-04: Refined questions

### What was done

- Question 1 now uses `Gender`, `Age`, `family_history`, `no_employees` and `remote_work` as factors of `treatment` (`Country` removed).
- Question 3 now compares `mental_health_consequence`, `supervisor` and `coworkers` by `tech_company` only.

### Decisions and why

- **Company size and remote work move to question 1:** Charlotte wanted to see whether they change who seeks treatment, next to family history. Employer support stays in question 2.
- **Percentages inside each group:** groups have very different sizes, so counts would mislead.
- **`Country` dropped from question 1:** about 60% of answers come from the US.

### Alternatives rejected

- A fourth question only about company size: employer support (question 2) already covers it.

### Where we stopped

- The hypothesis of question 3 is a first guess, to review when we look at the data. Next: data quality issue (missing values, duplicates).

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
