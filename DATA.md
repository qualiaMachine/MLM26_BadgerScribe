# Data

The evaluation set is a small, curated sample of pages from four UW Digital Collections, chosen for diversity across hands, layouts (prose vs. tables), scan media (direct scan vs. microfilm), and languages (English and German). **Images and ground-truth transcriptions are both released** — scores are self-computed with the repo's metric and reported in your writeup's submission card. Collection backgrounds are in [`docs/collections.md`](docs/collections.md). A separate **labelled calibration set** from the same collections is also released — unlike the evaluation pages, those are free to train and tune on. [RESOURCES.md](RESOURCES.md) adds public auxiliary datasets and the UWDC browsers when you need more volume.

## What you get

The Kaggle dataset attached to the competition contains:

```
calibration/
  images/
    <page_id>.jpg          labelled pages — free to train and tune on
  transcriptions.csv       page_id,text,category — ground truth, same conventions as solution.csv
eval/
  images/
    <page_id>.jpg          one image per evaluation page
  metadata.csv             page_id,category
  solution.csv             page_id,text,category — the ground-truth transcriptions
```

The calibration and evaluation sets are disjoint page samples from the same four collections. The calibration pages carry no usage restriction — fine-tune on them, tune prompts against them, or use them to compare candidate models and pipeline designs. They're what lets you calibrate a system to this material before measuring it on the evaluation set.

| `metadata.csv` column | meaning |
|---|---|
| `page_id` | unique page identifier, e.g. `survey_0012` |
| `category` | document category used for macro-averaging: `survey_notes`, `kade_letters`, `dominy_accounts`, `treaties_microfilm` |

`solution.csv` follows the conventions in [`docs/transcription_conventions.md`](docs/transcription_conventions.md), which you should read before building anything — it defines exactly what your pipeline is measured against (verbatim casing, punctuation, historical spelling; whitespace collapsed for scoring).

**The evaluation pages are for measuring, not training.** Ground truth is public because scores are self-reported — the deal is that you don't fine-tune on these pages or hand-tune prompts against their transcriptions. Build on the calibration set and auxiliary data; measure here. See [RULES.md](RULES.md).

## Predictions file and scoring

Run your pipeline over the evaluation images and write a CSV with one row per page:

| column | meaning |
|---|---|
| `page_id` | must match `metadata.csv` exactly, every page present |
| `text` | your predicted transcription for the page |

Line breaks inside `text` may be encoded either as real newlines (in a properly quoted CSV field) or as the two-character escape `\n` — the metric collapses all whitespace runs to single spaces before scoring, so both are equivalent. See [`evaluation/example_predictions.csv`](evaluation/example_predictions.csv) for a valid file. Then score it:

```bash
python evaluation/score_local.py --solution eval/solution.csv --submission my_predictions.csv
```

The overall macro CER and per-category CERs it prints are what go on the submission card in your writeup ([WRITEUP_TEMPLATE.md](WRITEUP_TEMPLATE.md)). Commit the predictions file to your submission repo alongside the code that produced it — it's part of what verification re-checks.

## Scoring definition

Per-page CER (Levenshtein distance over the ground-truth length, capped at 1.0), averaged within each category, then averaged across categories. Lower is better. Exact implementation: [`evaluation/metric.py`](evaluation/metric.py).

An empty or missing prediction scores CER 1.0 for that page; dumping unrelated text scores no worse than 1.0 (the cap), so there is nothing to game by padding.
