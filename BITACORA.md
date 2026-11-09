# BITACORA

Log of what we did and why. Newest entry first. Updated before each commit when a decision was made.
Entry format: what was done, decisions and why, alternatives rejected, where we stopped.

---

## 2026-11-09: Correlation matrix and heatmap (section 7.6)

### What was done

- Added section 7.6: `df_num` (all Yes/No columns as 0/1), correlation matrix, heatmap (lower triangle only) and a written interpretation with limits.
- 15 variables: 5 pure Yes/No, 8 multi-answer columns as "Yes = 1, rest = 0", `is_female` and `Age`.

### Decisions and why

- **Separate table `df_num`:** `df_clean` keeps its text columns.
- **Multi-answer columns as "is Yes":** same rule as `support_score`, so "Don't know", "Not sure", "Maybe" and "Some of them" count as 0. Simple, but it merges them with "No"; written as a limit.
- **`is_female` instead of `identities`:** one 0/1 column for the biggest gap found in 7.2.1. The third group (14 people) falls in the 0.
- **Left out:** `support_score` (sum of columns already in the matrix), `self_employed` (3 values, 146 Yes), columns with many categories (`Country`, `no_employees`, `leave`, `work_interfere`).
- **Pearson on 0/1 columns** (phi coefficient) and a simple reading rule: <0.1 almost nothing, 0.1-0.3 weak, 0.3-0.5 moderate, >0.5 strong.

### Results

- With `treatment`: `family_history` 0.38, `care_options` 0.27, `benefits` 0.21, `is_female` 0.18, `obs_consequence` 0.16, `anonymity` 0.13; the rest below 0.1. No variable passes 0.4.
- Strongest pairs: `wellness_program`-`seek_help` 0.60, `benefits`-`seek_help` 0.45, `supervisor`-`coworkers` 0.43, `benefits`-`care_options` 0.43. `mental_health_consequence`-`supervisor` is -0.35 because "Yes" means opposite things.
- Confirms 7.2 and shows that the support variables overlap (the reason for `support_score`).

### Limits found (for section 9)

- Correlation is not cause (reverse direction possible).
- 0/1 correlation depends on group sizes (rare "Yes" caps the value); only linear, pairwise relations; "Yes = 1, rest = 0" loses the "Don't know" vs "No" difference.
- `Age` correlation is weak (0.07) although 7.2.1 showed a soft trend.

### Where we stopped

- Issue #14 done (waiting for review and merge). Next: README (#10, waiting), #15 trends or group comparison, #16 executive report.

---

## 2026-11-09: Feature engineering (section 7.5)

### What was done

- Added section 7.5 with the three engineered variables of issue #13: `age_group` (created in 7.2.1), `identities` (created in 6.4) and the new `support_score`. Each has a short "why", its limit, and the chart where it is used.
- `support_score`: number of "Yes" in `benefits`, `care_options`, `wellness_program`, `seek_help` and `anonymity` (0 to 5).

### Decisions and why

- **Reuse two existing columns as engineered variables:** `age_group` and `identities` were already built for earlier issues, so 7.5 explains them and shows their chart again instead of duplicating work. Only one column was missing to reach three.
- **Third variable = `support_score`:** the four support variables of 7.2.2 overlap (same topic), so one score summarises them and answers question 2 with a single chart.
- **Only "Yes" counts 1; "No", "Don't know" and "Not sure" count 0.** Simple and easy to explain. Consequence: score 0 mixes "no support" and "does not know". Written as a limit.
- **`anonymity` added to the four variables of 7.2.2:** it is also something the employer offers.
- **`support_score` is not saved in `survey_clean.csv`**, same as `age_group`: it is an analysis choice. `identities` is saved because it comes from the cleaning.

### Alternatives rejected

- `company_size_group`: 7.2.1 showed no clear trend with company size, so the chart would be flat.
- `openness_score` (supervisor, coworkers, mental_health_consequence): "Yes" means opposite things in these columns, hard to explain.
- Year or month from `Timestamp`: 95% of the answers are from 2014.
- 0.5 for "Don't know": more complex without a clear reason.

### Results (% with `treatment = Yes`, by `support_score`)

- 0: 36.8% (n=517), 1: 53.0% (251), 2: 58.6% (203), 3: 70.4% (115), 4: 65.2% (92), 5: 66.7% (81).
- Clear rise from 0 to 3 (about 34 points), then flat around 65-70%.

### Limits found (for section 9)

- Scores 4 and 5 have 92 and 81 people: indicative only.
- A relation is not a cause: people who seek treatment may know the company resources better (reverse direction).
- Score 0 is the biggest group partly because 819 people answered "Don't know" on `anonymity`.

### Where we stopped

- Issue #13 done. Next: README (#10, waiting), #14 correlation heatmap, #15 trends or group comparison, #16 executive report.

---

## 2026-11-06: Group and summarize (section 7.4)

### What was done

- Added section 7.4 with three summary tables: age by identity (`groupby` + `agg`), treatment rate by company size (named aggregation), treatment rate by age group and family history (`pivot_table`, plus a count pivot).
- Created the helper column `treatment_yes` (1/0) in `df_clean`, so a mean gives a percentage.

### Decisions and why

- **`treatment_yes` is a numeric copy of `treatment`:** `mean()` of a 0/1 column is the share of "Yes", which makes the tables short. It is reusable for feature engineering (#13).
- **Count pivot next to the rate pivot:** a rate means little without its group size.
- **`age_group` and `treatment_yes` are not saved in `survey_clean.csv`:** they are analysis choices and live in `df_clean` in the notebook.

### Results

- With family history the treatment rate is about 74% at every age; without it the rate rises with age (31.0% to 50.0%). The age trend comes from people without family history.

### Limits found

- The 46+ cells are small (n=38 with family history, n=18 without): indicative only.

### Where we stopped

- Next: README (#10, waiting), then #13 feature engineering, #14 correlation heatmap, #15 trends or group comparison, #16 executive report.

---

## 2026-11-06: Visualization review

### What was done

- Audited the 21 charts of the notebook against the checklist of issue #8 (title, named axes, legend when needed). The brief asks for at least 5.
- Fixed the two gaps: axis names on the missing-values heatmap (5.1) and `plt.tight_layout()` on the first stacked-bar chart of 7.2.3, so its legend is not cut off.

### Decisions and why

- **No extra checklist table in the notebook:** the audit result is kept here and in the PR; the charts themselves already comply.
- **Single-series bar charts have no legend:** it would repeat the axis title. Stacked bars have one.
- **Boxplot of `Age` has no y label:** its vertical axis measures nothing.

### Where we stopped

- Next: README (#10); then the medium-level issues still open: group and summarize (#12), feature engineering (#13), correlation heatmap (#14), trends or group comparison (#15), executive report (#16).

---

## 2026-11-06: Written answers and conclusions (sections 8 and 9)

### What was done

- Section 8: one block per starting question (hypothesis, numbers taken from the 7.2 summary tables, answer, precautions).
- Section 9: three main conclusions, the idea that links them, and 8 limits.

### Decisions and why

- **Own branch (`feature/written-answers`, issue #9)**, not inside `feature/bivariate-analysis` (issue #7): one issue = one branch. It was started from the bivariate branch because sections 8 and 9 read the 7.2 results; it must be rebased onto `dev` once PR #7 is merged.
- **Answers: P1 partly confirmed, P2 confirmed, P3 confirmed with different strength.** P1: family history (39 points) and women vs men (23 points) hold; the trans group is higher but has 14 people, so "Female is the highest" cannot be confirmed; company size and remote work show nothing clear. P3: clear for fear of consequences (49 points) and supervisor (33 points), small for coworkers (10 points).
- **Section 8 answers each question; section 9 looks at the whole project** (main findings, limits). Limits are all in one place (9.2).

### Limits found (for section 9)

- All listed in 9.2: relation is not a cause, `treatment` is not who suffers, small groups (14, 65, 56, 60), mostly men and US, answers from one month (Aug 2014), voluntary survey, rows lost in cleaning, overlapping variables.

### Where we stopped

- Next: push `feature/bivariate-analysis` and open PR #7 into `dev`; after the merge, rebase `feature/written-answers` and open PR for #9. Then README (#10) and executive report (#16).

---

## 2026-11-06: Bivariate analysis, question 3 (7.2 done)

### What was done

- 7.2.3 done: `mental_health_consequence`, `supervisor` and `coworkers` by `anonymity`, each with a crosstab in percent and a 100% stacked bar chart, plus the summary table. Section 7.2 is complete (12 relations).

### Decisions and why

- **100% stacked bars** (`plot(kind="bar", stacked=True)`): the question is how the answers are split inside each anonymity group, not a single "Yes" rate.
- **Legend outside the plot** (`bbox_to_anchor`): inside, it hid the first bar.
- **"Yes" does not always mean the same.** `mental_health_consequence`: Yes = fears negative consequences (bad sign). `supervisor` / `coworkers`: Yes = would talk (good sign). Each note says which one it reads.
- **Columns ordered from least to most open** (`No`, `Some of them`/`Maybe`, `Yes`) so the stacks read the same way.
- **`Don't know` kept as its own group** (819 people), never merged with `No`.

### Results (question 3, by `anonymity` Yes / No / Don't know)

- `mental_health_consequence`, fears consequences: 15.2% / 64.6% / 23.6% (49 points).
- `supervisor`, would talk: 54.4% / 21.5% / 36.4% (33 points).
- `coworkers`, would talk: 22.7% / 12.3% / 16.1% (10 points); "Some of them" is the main answer in all groups (47.7% to 63.0%).

### Limits found (for section 9)

- `anonymity = No` has only 65 people (8 say Yes for coworkers): strong gaps but indicative.
- A relation is not a cause.

### Where we stopped

- Next: section 8 (answer each starting question using the summary tables of 7.2), then section 9.

---

## 2026-11-06: Bivariate analysis, question 2 (in progress)

### What was done

- 7.2.2 done: `treatment` by `benefits`, `care_options`, `wellness_program` and `seek_help`. Same method as 7.2.1 (crosstab in percent, bar chart, short note), plus the summary table of the block.

### Decisions and why

- **"Don't know" / "Not sure" is kept as its own group**, never merged with "No": someone who does not know may simply never have needed the resource.
- **Each variable is read for what it asks:** `benefits` = the company offers it; `care_options` = the person *knows* the options (knowledge, not offer); `wellness_program` and `seek_help` = a program / resources exist. The notes use the right verb for each.
- **Notes describe one column only** (% with `treatment = Yes` inside each group); the "No" column is its complement.

### Results (question 2, % with `treatment = Yes`)

- `benefits`: Yes 63.9%, No 48.4%, Don't know 37.0% (15 points).
- `care_options`: Yes 69.1%, No 41.3%, Not sure 39.2% (28 points, the biggest).
- `wellness_program`: Yes 59.4%, No 49.9%, Don't know 43.1% (9.5 points; groups of 229 / 842 / 188).
- `seek_help`: Yes 59.2%, No 50.0%, Don't know 45.7% (9 points).

### Limits found (for section 9)

- A relation is not a cause: people who seek treatment may learn more about the company's resources (reverse direction).
- The four variables overlap (same topic), so they are not four independent proofs.

### Where we stopped

- Next: 7.2.3 (`mental_health_consequence`, `supervisor`, `coworkers` by `anonymity`, 100% stacked bars).

---

## 2026-11-06: Bivariate analysis, question 1 (in progress)

### What was done

- Section 7.2 structure: one block per starting question (7.2.1, 7.2.2, 7.2.3), one block per relation, and a summary table at the end of each block. 12 relations in total, as many as the 3 questions need (the brief asks for at least 3).
- 7.2.1 done: `treatment` by `family_history`, `identities`, `Age` (in groups), `no_employees` and `remote_work`. Each has a crosstab in percent, a bar chart, and a short note ("Qué vemos" / "Interpretación").

### Decisions and why

- **Number of relations = what the questions need.** Not more just to have more, not fewer than the 3 required.
- **Crosstab with `normalize="index"`:** the percentage is computed inside each group, so groups of different sizes can be compared.
- **Conclusions per question go to section 8, not to 7.2.** In 7.2 each relation only describes what the chart shows; the summary table collects the numbers for section 8.
- **Age groups: 18-25, 26-35, 36-45, 46+** (column `age_group`, `pd.cut`). 4 groups keep every group big enough and the chart readable. The 8 NaN ages are left out.
- **`age_group` is created in 7.2, not in section 6.** It is an analysis choice for question 1, not a fix of wrong data (unlike `identities`, which came with the messy `Gender`). It is not saved in `survey_clean.csv`.
- **Notebook outline:** per-relation labels stay in bold (same style as 7.1); the summary of each question is a level-5 heading under its block.

### Results (question 1, % with `treatment = Yes`)

- `family_history`: No 35.5%, Yes 74.2% (39 points, the biggest gap).
- `identities`: Male 45.5%, Female 68.8%, Trans / non-binary / other 78.6% (14 people only).
- `Age` groups: 48.4%, 48.6%, 55.2%, 58.9% (soft upward trend).
- `no_employees`: between 44.1% (6-25) and 56.2% (1-5), no clear trend.
- `remote_work`: No 49.7%, Yes 52.7% (3 points, almost nothing).

### Alternatives rejected

- 5 age groups of 5 years: same trend, more bars, nothing more to say.
- Moving `age_group` to the cleaning section: would change sections 5 and 6 and the saved CSV for one column used once.

### Limits found (for section 9)

- `Age` 46+: 56 people; relation is weak and only indicative.
- A relation is not a cause. Idea not tested: `work_interfere` could explain part of who seeks treatment (needs 3 variables).

### Where we stopped

- Next: 7.2.2 (`benefits`, `care_options`, `wellness_program`, `seek_help`), then 7.2.3 (`anonymity`).

---

## 2026-11-05: Univariate analysis and outliers

### What was done

- Section 7.1: one chart and one short Spanish note per variable. `Age` (histogram + boxplot), `treatment`, `identities`, `family_history`, `no_employees`, `anonymity` (count plots) and `Country` (top 5 countries).
- Section 7.3: outliers in `Age` with the IQR rule (limits 13.5 and 49.5): 32 values (2.6%), all real ages from 50 to 72.

### Decisions and why

- **Chart chosen by variable type** (guide: data-to-viz.com): histogram and boxplot for a number, count plot for categories.
- **Extra variables beyond the 3 required:** `family_history`, `no_employees`, `anonymity` are the factors of questions 1 and 3, so we check group sizes before comparing; `Country` only to state a limit.
- **`Country`: show only the top 5.** 48 countries, most with 1-5 answers, so a full chart is unreadable. The top 5 hold 85.8% of the answers (US alone 59.7%). Data stays in `df_clean`.
- **`no_employees` and `anonymity` use a fixed `order=`:** sizes have a natural order, and the answers read better as Yes / No / Don't know.
- **Outliers: keep the 32 values.** They are possible ages (older workers), not errors; removing them would lose valid answers and bias towards younger people. The impossible ages (8) were already set to NaN in 6.3. IQR finds 40 outliers before cleaning and 32 after.

### Alternatives rejected

- Chart of all 48 countries: unreadable.
- Capping or removing ages above 49.5: would hide real respondents.

### Limits found (for section 9)

- `Trans / non-binary / other`: 14 people; `anonymity = No`: 65 people; `no_employees = 500-1000`: 60 people. Results for these groups are indicative only.
- About 60% of the answers come from the US.
- `treatment` is balanced (50.6% Yes) but measures who sought treatment, not who suffers.

### Where we stopped

- Next: section 7.2 (bivariate analysis), issue #7.

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
