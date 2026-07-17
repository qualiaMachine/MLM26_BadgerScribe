# Maintainer notes

Internal working notes for organizers (Chris, Scott, Kevin). Participants can ignore this file — nothing here changes the rules.

## Decision log

Decisions settled in the planning doc + email thread (June–July 2026):

- **Challenge dataset is UW samples only.** External public datasets moved from "benchmark components" to a suggested-resources section participants pull themselves. Rationale: effort savings, and assembling a calibration set becomes part of the challenge itself. Fallback if in-house sample prep falls flat: pivot back to building in auxiliary data (Bentham/IAM subsets).
- **Ground truth strategy:** at least one transcribed sample from as many collections as staff time allows this year; Sternberger & Seifert letters already have MKI Kurrent-group transcriptions (free GT for the Kade category). Sample selection aims for diversity: different hands, tabular vs. prose, microfilm vs. direct scans. Scoring may weight UW docs more heavily if needed — currently moot since the eval set is 100% UW.
- **Resources list:** Bentham, BLN600, NARA RG 109 as best matches; IAM kept as calibration-only with its modern-handwriting limitation noted; Alfred Escher correspondence added for period German (Kurrent).
- **Metric:** CER primary, verbatim (no normalization), whitespace collapsed; macro-average across categories; WER as diagnostic only. Cap per-page CER at 1.0.
- **Rules:** open-weight <70B per model, honor-system up front, code review of writeups in contention before winners announced; code link (public GitHub, pinned commit) required with every submission; no prize, collaborative framing.
- **Starter notebooks (Kevin):** (1) traditional pipeline from scratch (layout detection → language routing → recognition), (2) open-source tools (Kraken segmentation + PyLaia recognition), (3) small open-weight VLM. Optional fine-tuning angle: ~100 curated drawn-table pages from the survey notebooks.
- **Name: BadgerScribe (July 2026, Chris).** Full title "BadgerScribe: Archival Document Transcription Challenge" — Wisconsin identity + the task, subtitle keeps it discoverable. Runner-up considered: OpenAgent OCR (series branding with OpenAgent Coding, but "agent" overpromised autonomy and "OCR" undersold handwriting).
- **A labelled UW calibration set IS provided (July 2026, Chris).** Revises the "participants assemble all their own data" stance above: a small sample of GT-transcribed archival pages ships alongside the evaluation set, free to train/tune on. Purpose is calibration — letting teams find the best models and the surrounding code/harness/scaffold for faithful transcription on this material — not large-scale training; RESOURCES.md auxiliary sets still cover volume. Consequence: staff GT effort now covers two disjoint samples (calibration + evaluation) per collection.
- **Kaggle Hackathon format, not a scored competition (July 2026, Chris).** The Writeup is the submission — same format Efficient Coder landed on. Rationale: the writeup/documented-process is the whole point for the Libraries; no prize means no need for adversarial-grade scoring infrastructure. Consequence: the UW GT sample is **released** (images + solution.csv) as the public evaluation set teams tune toward with auxiliary data. No hidden test set, no submission CSV upload. New rule carrying the weight: evaluation pages are for measuring, not training.
- **Format revisit condition (July 2026, Chris).** Hackathon format is the call *given expected GT scarcity* — with a small evaluation set, a hidden test set is statistically weak (macro CER over a handful of pages per category is noise) and hiding data buys little protection that code review doesn't already provide. If GT transcription goes much better than expected over the next month (enough pages to split into a real public dev set + hidden test set with meaningful per-category counts), reconsider a scored-competition format before launch — that's the one configuration where a hidden set earns its infrastructure.
- **No leaderboard, no standings post, no CSV submission (July 2026, Chris).** Refines the hackathon decision: nothing is uploaded and nothing is auto-scored — everything is a writeup. Teams still measure CER themselves with `score_local.py` and report the numbers on the submission card; reported numbers must be reproducible from the posted code (re-run when a writeup is in contention for recognition). What's gone is any ranking infrastructure: no leaderboard and no organizer-maintained standings post.

## Open questions

- [ ] Reading-order convention for marginalia (survey notebooks) — Chris, with Scott
- [ ] Whether `#` illegibility markers count toward reference length in scoring — Chris
- [ ] Final collection list: do all four categories get enough GT pages this year, or does a category drop? — Scott
- [ ] GT volume check (~1 month out): if page counts come in well above plan, revisit hackathon vs. scored competition per the format revisit condition above — Chris
- [ ] If GT budget allows, hold back 5–10 pages (GT'd late, never released) to re-run leading pipelines on during end-of-challenge verification as a generalization check — reported not as a ranking but as a robustness note in the wrap-up — Chris + Scott
- [ ] Confirm Kaggle slug (`badgerscribe` assumed in the citation — sweep links if Kaggle assigns something else) + launch dates (kickoff Fall 2026) — Chris
- [ ] License file for this repo (code MIT? UW image derivatives need their own rights statement) — Chris + Scott
- [ ] Whether hosted endpoints serving named open-weight checkpoints count for the *submitted* run or prototyping only (FAQ currently says prototyping; on-prem spirit suggests submitted runs should at least be reproducible on-prem) — Chris

## Launch checklist

- [ ] GT pages transcribed + double-checked per `docs/transcription_conventions.md` — two disjoint samples per collection (calibration + evaluation); conventions doc updated with per-collection quirks and de-drafted
- [ ] Package Kaggle dataset (`calibration/images/` + `transcriptions.csv`, `eval/images/`, `metadata.csv`, `solution.csv`) — well under the 20 GB cap; JPEG derivatives of the TIFFs
- [ ] Sanity-check the released `solution.csv` through `score_local.py` (perfect submission scores 0.0; per-category counts look right)
- [ ] Set up the Kaggle Hackathon: Writeup submission flow, Open track, cover-image note
- [ ] Kevin: fill in the three starter notebooks against the released evaluation pages; run end-to-end on Kaggle Notebooks free tier; record baseline CERs per category (gives us the difficulty read and a baselines post for Discussion)
- [ ] Baseline comparison table (Tesseract / Kraken+PyLaia / small VLM) posted to Discussion at launch
- [ ] Copy README sections to the Kaggle Overview/Data tabs; RULES.md to the rules section
- [ ] Everyone joined the ML+X Kaggle organization (invite link in the planning doc — not committed here)
- [ ] Announce soft launch; official UW–Madison kickoff Fall 2026

## Repo conventions

- README.md mirrors the Kaggle home-page section layout (same convention as the Efficient Coder repo); DATA.md is the Data tab; RULES.md the rules section.
- `evaluation/metric.py` is the single source of truth for scoring — `score_local.py` wraps it, participants self-score with it (optional; no leaderboard), and verification re-runs it; it carries a self-test (`python evaluation/metric.py`).
- Placeholders that must be resolved before launch are marked `TBD` or tracked above.
