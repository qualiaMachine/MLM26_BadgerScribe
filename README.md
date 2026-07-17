# BadgerScribe: Archival Document Transcription Challenge

Faithfully transcribe handwritten and scanned archival documents into machine-readable text — scored on real collections from the UW–Madison Libraries. Hosted by [ML+X](https://hub.datascience.wisc.edu/communities/mlx/) at UW–Madison with the [UW Digital Collections Center](https://www.library.wisc.edu/dcc/). Kickoff Fall 2026 (dates TBD). Open to everyone.

---

## Overview

Institutional archives contain vast collections of documents that remain difficult to access at scale: handwritten manuscripts, scanned reports, typed historical records, forms, and mixed-format pages. Modern vision-language models and OCR systems have improved significantly, but faithful transcription of archival documents remains challenging across a wide range of conditions — handwriting variability, degraded scans, complex layouts, and multi-format documents.

This challenge focuses on **faithful transcription of archival document images into machine-readable text**. The UW Libraries' digital collections provide the motivating use case and the evaluation data — nineteenth-century surveyors' field notes, German immigrant correspondence, craftsmen's account books, and treaty-era government documents. Participants are evaluated on how accurately they convert document images into text under the following constraints:

- Transcription must be faithful: verbatim text in reading order, original spelling and punctuation preserved, no summarizing, no cleanup — accuracy is measured as character error rate against ground truth.
- The pipeline must execute end-to-end on a single GPU with at most 96 GB of VRAM.
- Every model must be open-weight: no closed-weight APIs (GPT, Claude, Gemini) anywhere in the submitted pipeline.

This is an **educational, collaborative challenge**. There are no cash prizes and no reason to hoard ideas. The point is to surface and share transcription pipelines that libraries and archives can actually deploy — share repos early, post findings to the Discussion tab, and build on each other's approaches. Every improvement one team publishes moves real archival collections closer to being readable, searchable, and accessible.

---

## Description

### Background

The UW Digital Collections Center holds many collections of handwritten and scanned historical documents, almost all digitized as high-resolution TIFF scans, and most with no transcription at all. Without text, these materials can't be searched, can't be read by screen readers, and can't be studied at scale. Staff time for manual transcription is scarce, so what the Libraries need is not a magic one-stop tool but a **repeatable, low-staff-effort process** for transcribing a given set of documents — exactly the kind of pipeline this challenge asks you to build and share.

There is no consensus on what that pipeline should look like. A traditional OCR pipeline (layout detection → line segmentation → recognition), an assembly of open-source tools like Kraken and PyLaia, a small vision-language model prompted page-by-page, or any of those fine-tuned on a small curated sample — all are viable, and they behave very differently across handwriting styles, languages, and layouts. How you build the pipeline matters as much as which model you pick.

### The task

Given a document image (a single page or scan), produce a faithful transcription.

Inputs may include:

- Handwritten archival manuscripts (the primary anchor use case)
- Printed and scanned institutional records
- Typed historical documents
- Mixed-format archival pages (forms, reports, long tables)
- Degraded or historical documents, including microfilm scans

Outputs must be:

- A full text transcription of the page
- In natural reading order where applicable
- Minimally normalized — no summarization, paraphrasing, or "cleanup" of historical spelling

This is strictly a transcription task — no question answering, no reasoning over content, no summarization.

**Hardware constraint: your submitted pipeline must run end-to-end on a single GPU with at most 96 GB of VRAM, using open-weight models only.** The budget covers the pipeline as it executes: sequential model loading is fine, and quantization is allowed and encouraged. Closed-weight API models (GPT, Claude, Gemini) are out of scope for the submitted run; see [RULES.md](RULES.md).

### Hard cases

The evaluation set emphasizes realistic conditions where current systems struggle:

- Handwriting variability across writers and decades (the core anchor use case)
- Degraded, faded, or low-contrast scans, including microfilm
- Irregular layouts and reading-order ambiguity
- Mixed handwritten and printed content on the same page
- Dense pages, hand-drawn tables, and structured field notes
- Historical spelling and formatting variation
- Multiple languages and scripts — including nineteenth-century German handwriting (Kurrent), which is a genuinely different script from English cursive

Strong solutions demonstrate robustness across document types rather than optimizing for one clean category.

### The evaluation data

The evaluation set is built **entirely from UW Digital Collections materials** with verified ground-truth transcriptions — a deliberately small, curated, rights-cleared sample chosen for diversity across what actually varies in the archives: different hands, tabular vs. prose layouts, microfilm vs. direct scans, English and German. **Images and ground truth are both released** — you score yourself with the repo's metric and report the numbers in your writeup (see [Evaluation](#evaluation)). The source collections:

| Collection | What it is | Why it's hard |
|---|---|---|
| [Wisconsin Land Survey Field Notes](https://search.library.wisc.edu/digital/ASurveyNotes) | Original surveyors' field notebooks, 1830s–1860s | Hand-drawn tables, diagrams, terse abbreviations, pencil on weathered paper |
| [Max Kade Institute German letters](https://digital.library.wisc.edu/1711.dl/KadeLetters) | Immigrant correspondence in German, 19th–early 20th c. | Kurrent script — a different alphabet from modern German cursive |
| [Dominy Craftsmen account books](https://digital.library.wisc.edu/1711.dl/Dominy) | Woodworkers' and clockmakers' account books | Tabular ledger layouts, names, currency, dense entries |
| [Native American treaty documents](https://digital.library.wisc.edu/1711.dl/TreatiesMicro) | 19th-c. handwritten documents relating to Wisconsin treaties | Microfilm scans — low contrast, artifacts |

Public OCR/HTR benchmarks are well represented in VLM training data; a set of real institutional documents that has never circulated with transcriptions is what tells you whether a system actually generalizes. See [DATA.md](DATA.md) for the exact data format and [`docs/collections.md`](docs/collections.md) for collection details.

**All of the UW ground truth is in the evaluation set — training data comes from elsewhere.** [RESOURCES.md](RESOURCES.md) maps labelled auxiliary datasets (Bentham, BLN600, NARA record groups, IAM, the Alfred Escher German correspondence, and more) to the UW collections; fine-tuning and calibrating on those, then measuring against the evaluation set, is the expected loop. The public UWDC browsers are also open if you want to transcribe additional UW pages yourself. The evaluation pages themselves are measuring-only: tune *toward* them, never *on* them (see [RULES.md](RULES.md)).

---

## Starter materials

Three starter notebooks in [`notebooks/`](notebooks/) demonstrate the main solution families end-to-end, from document image to scored CER:

- [`01_traditional_pipeline.ipynb`](notebooks/01_traditional_pipeline.ipynb) — a from-scratch traditional OCR pipeline: layout detection → language routing → line recognition/transcription.
- [`02_open_source_tools.ipynb`](notebooks/02_open_source_tools.ipynb) — the same pipeline assembled from open-source components: [Kraken](https://kraken.re/) for segmentation, [PyLaia](https://gitlab.teklia.com/atr/pylaia) for recognition.
- [`03_vlm_transcription.ipynb`](notebooks/03_vlm_transcription.ipynb) — a small open-weight vision-language model prompted for page-level transcription.

If you're ambitious, fine-tune: even ~100 curated pages of the land-survey drawn tables could yield significant gains on that category. [`evaluation/`](evaluation/) has the official scoring code — the numbers it prints are the numbers you report in your writeup.

---

## Data

The evaluation set (images + released ground truth) and the predictions-file format are described in [DATA.md](DATA.md). Ground-truth transcription conventions — what "faithful" means character-by-character, and how line breaks and whitespace are handled — are pinned down in [`docs/transcription_conventions.md`](docs/transcription_conventions.md).

---

## Evaluation

### Scoring

The primary metric is **Character Error Rate (CER)**, macro-averaged across document categories — lower is better:

```
page CER      = levenshtein(prediction, ground_truth) / len(ground_truth)   (capped at 1.0)
category CER  = mean of page CERs within the category
your score    = mean of category CERs
```

Macro-averaging across categories means a system has to perform across the board — acing clean English prose while failing the German letters or the microfilm scans will not produce a good score. Word Error Rate (WER) and per-category CER are printed by the scorer as diagnostics alongside the macro CER.

**Scoring is against the verbatim ground-truth transcription.** The whole point is faithful extraction, so casing, punctuation, and original historical spelling all count — no lowercasing, no punctuation stripping, no cleanup. The one normalization applied to both prediction and reference before comparison: runs of whitespace (spaces, line breaks) collapse to a single space. That makes scoring insensitive to how you encode line breaks, while everything else stays verbatim. Full details and the exact implementation: [`evaluation/metric.py`](evaluation/metric.py).

### Computing your score

**There is no leaderboard, no automated scoring, and nothing to upload — you measure yourself.** Ground truth for the evaluation set is public, so at any point you can run your pipeline over the evaluation images, write a predictions CSV, and score it with the released tooling:

```bash
python evaluation/score_local.py --solution data/eval/solution.csv --submission my_predictions.csv
```

The macro CER it prints, plus the per-category breakdown, are what you report in your writeup — a documented pipeline with a measured CER is the deliverable. **One rule keeps the numbers meaningful: the evaluation pages are for measuring, not training.** Don't fine-tune on them or hand-tune prompts against individual pages' ground truth; build on auxiliary data ([RESOURCES.md](RESOURCES.md)) and measure honestly.

### Verification

CER numbers in writeups are self-run, so they must be reproducible: organizers may clone the repo at your submitted commit and re-run the pipeline over the evaluation set with the declared models before recognizing a writeup, checking the reproduced CER against the reported one (sampling is stochastic; normal run-to-run variation is fine). The review also confirms the pipeline runs within the 96 GB single-GPU VRAM budget, that no human hand-transcribed or trained on the evaluation pages, and that every model is open-weight. That review is the real backstop; the rules up front are deliberately lightweight.

---

## Submission Requirements

Submissions are **Kaggle Writeups** — there is no file upload, no automated scoring, and no leaderboard. Create one with the "New Writeup" button on the competition page; after you save, a "Submit" button appears in the top right corner. **Your final Writeup must be submitted before the deadline — draft or un-submitted Writeups are not considered.** You can edit and resubmit as your pipeline improves; the submitted version at the deadline is what counts.

### The Writeup (project report)

Title, subtitle, cover image, and your report, **≤2,500 words**. (Kaggle requires the cover image to submit — one of your evaluation pages next to your transcription, or a pipeline diagram, both work.) [WRITEUP_TEMPLATE.md](WRITEUP_TEMPLATE.md) is a suggested structure if you want guidance — otherwise organize it however you like. The one expectation: explain your learning journey — what you tried, what worked, what didn't, and where you ended up. For this challenge the writeup isn't paperwork on top of the real submission; it *is* the submission — a documented, reproducible transcription process is exactly the artifact the Libraries need.

Open the report with your **submission card** — copy this block and fill in your values:

```
code_url: https://github.com/team/pipeline/tree/v1.0-submission
models: Qwen/Qwen2.5-VL-7B-Instruct
peak_vram: 18 GB
cer_overall: 0.183
cer_by_category: survey_notes 0.21 | kade_letters 0.24 | dominy_accounts 0.14 | treaties_microfilm 0.14
external_data: Bentham line pairs; 120 self-transcribed survey-notebook lines (fine-tuning)   # "none" is fine
hardware: RTX 4090 24 GB                # informational, not scored
eval_wall_clock: 38 min                 # informational, not scored
```

`code_url` must be a public repo pinned to the exact tag or commit you ran (`git tag v1.0-submission && git push origin v1.0-submission`). Open the link in a private browser window before submitting — if it 404s, your repo is private or the commit isn't pushed. `cer_overall` and `cer_by_category` come straight from your own `evaluation/score_local.py` run — there's no leaderboard and nothing to upload, but the numbers must be reproducible from your posted code. `external_data` is a disclosure, not a requirement — external data is optional, and `none` is a perfectly good answer. `hardware` and `eval_wall_clock` (total wall-clock time to process the full evaluation set) are informational only, never scored — together they're the deployability signal the Libraries care about. Field-by-field details: [WRITEUP_TEMPLATE.md](WRITEUP_TEMPLATE.md).

During the challenge, share early and often via the Discussion tab: post progress, share your repo, describe what's working and what isn't. Think of Discussion as an open lab notebook for the cohort.

---

## Tracks and Awards

**Open track** — the only track; select it when submitting your Writeup. There is no metric leaderboard: writeups are reviewed on the documented pipeline and its measured results — the reported CERs — and writeups in contention for recognition are verified from the posted code.

There are no cash or material awards — this is a non-monetary educational challenge (Kaggle Kudos only). Top teams may be invited to present at the ML+X showcase, and strong pipelines may be adopted by the UW Digital Collections Center for production transcription work — which is a better trophy anyway.

---

## Organizers

BadgerScribe was designed collectively by its organizers:

- **Chris Endemann** (endemann@wisc.edu), Research Cyberinfrastructure / DoIT, UW–Madison — challenge lead; evaluation and coordination
- **Scott Prater**, UW Digital Collections Center, UW–Madison Libraries — domain lead; collections, ground truth, and what "correct" means
- **Kevin Chovanec**, Division of Extension, UW–Madison — datasets, baselines, and starter notebooks

Hosted by [ML+X](https://hub.datascience.wisc.edu/communities/mlx/) at the University of Wisconsin–Madison. Sponsor info: https://hub.datascience.wisc.edu/communities/mlx/sponsorship/

---

## Citation

```
Christopher Endemann, Scott Prater, Kevin Chovanec. BadgerScribe: Archival Document Transcription Challenge.
https://kaggle.com/competitions/badgerscribe. Kaggle.
```
