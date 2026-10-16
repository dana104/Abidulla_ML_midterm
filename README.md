# Task 3 — Midterm Project

Starter files for **Task 3** (graded, 50 points, project with defence, due Sunday 18 October 2026, 23:59 Canvas
time, UTC+5) of *Machine Learning (Python)*, MOP 3231, Narxoz University.

The task text, the pipeline blueprint, what to submit, the defence and the grading table are in the handout in the
**Week 07** module in Canvas. This repository holds only the files you start from; it has no instructions of its own
beyond the setup below.

## What is here

| File | What it is |
|---|---|
| `data/train.csv` | 32,940 labelled rows: `id`, 20 features (including `duration`) and the target `y` (`yes` / `no`) |
| `data/test.csv` | 8,236 rows to score: `id` and 19 features. It has no `y`, and no `duration` |
| `requirements.txt` | Pinned library versions your model is saved and loaded with. Do not change them |
| `.gitignore` | Keeps your virtual environment and notebook checkpoints out of git |

## How to start

You do not push to this repository. You make **your own** repository and submit the URL of its final commit in Canvas.

1. Download this repository as a ZIP (**Code → Download ZIP**) and unzip it into a new folder named
   `lastname_ML_task_3`.
2. Create the virtual environment and install the pinned libraries. You need **Python 3.11, 3.12, 3.13 or 3.14**
   (check with `python3 --version`; on Windows `python --version`):

   ```bash
   cd lastname_ML_task_3
   python3 -m venv .lastname_ML_task_3
   source .lastname_ML_task_3/bin/activate      # Windows PowerShell: .lastname_ML_task_3\Scripts\Activate.ps1
   pip install -r requirements.txt
   ```

3. Work in a notebook called `lastname_task3.ipynb` in the root of the folder, next to `data/`.
4. `git init`, commit as you go, push to your own GitHub repository and submit the final commit URL.
   What must be in the repository, and how the saved model is loaded, is in the handout.

## The data

The files are derived from `bank-additional-full.csv` of the **Bank Marketing** dataset from the UCI Machine Learning
Repository, licensed under
[Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/).

> Moro, S., Rita, P., & Cortez, P. (2014). Bank Marketing [Dataset]. UCI Machine Learning Repository.
> https://doi.org/10.24432/C5K306

Original paper: Moro, S., Cortez, P. & Rita, P. (2014), *A Data-Driven Approach to Predict the Success of Bank
Telemarketing*, Decision Support Systems, https://doi.org/10.1016/j.dss.2014.03.001

**Changes made to the original file:** the 12 exact duplicate rows were dropped; the rest was split at random
(stratified on `y`, fixed seed) into 80 % `train.csv` and 20 % `test.csv`; a shuffled integer `id` was added as the
first column; `y` and `duration` were removed from `test.csv`. Nothing else was changed: the `unknown` values, the
`pdays = 999` value and the column names are as in the source.

This is a teaching dataset, not evidence about real banks or clients.
