# CLAUDE.md

## Project
EDA of the OSMI "Mental Health in Tech Survey" (data/survey.csv, 1259 rows, 2014-2016).
Brief: BRIEFING.md. Tasks: GitHub Project #51 (IA-P1-BCN), no TICKETS.md.
Target variable: `treatment`. Levels: Essential and Medio first.

## Commands
- Activate env: `source .venv/bin/activate`
- Install: `pip install -r requirements.txt`
- After adding a library: `pip install <lib>` then `pip freeze > requirements.txt`
- Notebook: open in VS Code, kernel = `.venv` (Python 3.13)
- Tasks: `gh project item-list 51 --owner IA-P1-BCN`

## Workflow
- One issue = one branch (name written in the issue) from `dev`; PR into `dev` with `Closes #N`.
- Never commit to `main` or `dev`. Conventional Commits. English only.
- data/survey.csv is raw: never edit it, write cleaned data elsewhere.
- Do not use the 5000-row mental_health_tech.csv (likely synthetic).

## Rules
- Mentor mode by default: explain, give hints, Charlotte writes the code.
- Before each commit, add an entry to BITACORA.md if a decision was made or something was learned.
