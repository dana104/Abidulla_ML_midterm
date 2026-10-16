# Model card: term-deposit call prioritisation

## Purpose
A Portuguese bank phones clients to sell a term deposit. The call centre can phone only 10% of the
clients on a list in one campaign wave. My model gives every client a score (the probability of
"yes"). The 10% of clients with the highest scores are called. Scoring is done before any call.

## Data
UCI Bank Marketing data calls made from 2008 to 2010.
`train.csv` has 32,940 rows with the answer `y` (11.3% "yes")
`test.csv` has 8,236 rows without the answer

- `test.csv` does not have `y` (hidden), and it has an `id` column that I do not use as a feature.
- `duration` (length of the call) is not in `test.csv`. It is known only after the call, so I didnt use it
- "unknown" appears as a missing value in `default` (6,871 rows, about 21%), 
`education` (1,401),
`housing` and `loan` (794 each) 
`job` (275)
`marital` (64)
- In `pdays`, the value 999 (96.2% of rows) means "not contacted before", not 999 days.
- The month changes 26,442 times in 32,940 rows (about 80% of rows), so the row order is shuffled.
  

## Pipeline
Everything is in one scikit-learn `Pipeline`, so all steps are fitted on training folds only
It accepts the raw columns of `test.csv` (without `id`).
- Numeric columns: `StandardScaler`
- Text columns: `OneHotEncoder` (`handle_unknown="ignore"`). "unknown" is kept as its own category.
  I compared it with filling "unknown" by the most frequent value: ROC-AUC 0.7971 vs 0.7973. This is
  inside the noise (fold std was about 0.007), so I kept the simpler option
- `pdays`: 999 is replaced by the median, and a 0/1 flag column "was 999" is added.
- Model: random forest, 200 trees, `max_depth=10`, `min_samples_leaf=20`, `max_features=0.3`,
  `class_weight="balanced_subsample"`. The parameters were chosen by 5-fold stratified
  cross-validation on 80% of `train.csv`.
- SEED = 3113 

## Metric and results
Accuracy is misleading here: a model that always says "no" has 88.7% accuracy but finds nobody.
I use ROC-AUC and average precision, and precision, recall and lift for the top 10%.

| Model | CV ROC-AUC (mean ± std, 5 folds) |
|---|---|
| Always "no" | 0.5000 ± 0.0000 (accuracy 0.887) |
| Logistic regression (tuned) | 0.7979 ± 0.0068 |
| KNN (tuned) | 0.7900 ± 0.0081 |
| Random forest (tuned, chosen) | 0.8083 ± 0.0067 |

- Random forest is better than LR and KNN by more than the fold spread. LR and KNN cant be
  separated: their difference (0.0079) is inside the noise
- Class weights did not help (differences of 0.0006 and 0.0012, inside the noise).

**Hold-out**: ROC-AUC 0.7842, average precision 0.4619
This is lower than the CV score (0.8083). I chose the model on the same folds, so CV was a little
optimisti and the hold-out has only 742 "yes" clients

**Top 10% rule** (659 of 6,588 clients called): precision 0.513, recall 0.456, lift 4.55x.
338 called clients said "yes", 321 said "no", and 404 "yes" clients werent called
Random calling would give only about 74 "yes" clients from 659 calls

## Limits and ways it can fail
1. **Random split flatters the result.** The data are from 2008-2010, and the strongest features are
   economic (`nr.employed`, `euribor3m`, `emp.var.rate`). In real use we train on the past and call in
   the future when rates and employment are different. The real result will probably be lower
   than the hold-out result
2. **Data quality changes.** `default` has about 21% "unknown". If the bank collects data in a
   different way the scores can become wrong without any error message.
3. **Old data from one country.** New products or other kinds of clients can behave differently.
4. The model gives score not reasons 

## What to monitor after launch
- The real share of "yes" among called clients  compared with the expected precision of about 51%.
- Drift of the score distribution and of the economic features compared with the training data.
- Lift by month and the share of "unknown" values in new data.
- If precision or lift falls retrain on recent data.