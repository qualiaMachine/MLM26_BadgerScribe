# Writeup template

This template is a suggested structure for those who want guidance, not a form to fill out. Organize yours however you like and fill it with whatever insights you learned. Explain your learning journey along the way — what you tried, what worked, what didn't, and where you ended up.

The one part that is **not optional** is the submission card — the block below, filled in with your values, at the top of your Writeup. It's what makes your work reproducible and your claims checkable. Everything after it is yours to shape.

---

### Submission card (required, at the top)

```
code_url: https://github.com/team/pipeline/tree/v1.0-submission
models: Qwen/Qwen2.5-VL-7B-Instruct; kraken blla.mlmodel (segmentation)
peak_vram: 18 GB
cer_overall: 0.183
cer_by_category: survey_notes 0.21 | kade_letters 0.24 | dominy_accounts 0.14 | treaties_microfilm 0.14
external_data: Bentham line pairs; 120 self-transcribed survey-notebook lines (fine-tuning)   # "none" is fine
hardware: RTX 4090 24 GB                # informational, not scored
eval_wall_clock: 38 min                 # informational, not scored
```

- `code_url` — your public repo at the exact tag or commit SHA that produced your reported numbers. Get it with `git tag v1.0-submission && git push origin v1.0-submission`; verify it loads in a private browser window. Include your predictions CSV in the repo.
- `models` — every model in the pipeline, with checkpoint names. Each must be open-weight ([RULES.md](RULES.md)).
- `peak_vram` — peak GPU memory during your evaluation run. Must be within the single-GPU 96 GB budget ([RULES.md](RULES.md)); quantization is allowed, so report what the run actually used.
- `cer_overall` and `cer_by_category` — exactly as printed by your own `evaluation/score_local.py` run against the released evaluation set. Lower is better. There's no leaderboard and nothing to upload — you measure yourself — but the numbers must be reproducible from your posted code; writeups in contention for recognition are re-run.
- `external_data` — using external data is optional; this field is a disclosure, not a score. If you trained, fine-tuned, or tuned on anything — public datasets, self-transcribed samples — list it here; otherwise write `none`. (Training on the evaluation pages themselves is against the rules.)
- `hardware` — **informational only, not scored.** It helps others judge deployability; there is no hardware requirement or advantage.
- `eval_wall_clock` — **informational only, not scored.** Total wall-clock time for your pipeline to process the full released evaluation set end to end (images in, predictions CSV out) on the listed hardware. Together with `hardware`, this is the deployability signal the Libraries care about.

Also add a **cover image** — Kaggle requires one to submit a Writeup; an evaluation page next to your transcription of it, or a pipeline diagram, both work.

The sections below are a starting point if a blank page is unhelpful:

---

### 1. Summary

> 3–5 sentences: what you built, the one or two ideas that mattered most, and your final CER (overall and the category that surprised you).

### 2. Pipeline architecture

> Walk one page through your system: what happens to the image (preprocessing, layout detection, segmentation), what the model(s) see, how outputs are assembled into the page transcription, and any post-processing. Name every checkpoint. A diagram is welcome but not required.

### 3. Data

> What you used for training/fine-tuning/calibration, where it came from, and how well it matched the test categories. If you curated your own samples from the UW collections, describe how — that experience is valuable to the Libraries in itself.

### 4. What we tried

> The experiments, roughly in the order you ran them — including the ones that failed or changed nothing. For each: what you changed, what you expected, what actually happened to the CER. Negative results are explicitly in scope; they save the next team a week.

### 5. Results

> Final overall CER and per-category CERs from `evaluation/score_local.py`, plus the exact command that produced your predictions file. Which categories are strong/weak, and why do you think that is?

### 6. Failure analysis

> Pick 2–3 pages your pipeline handles badly and dig into why. Segmentation miss? Wrong language routing? Hallucinated "corrections" of historical spelling? Table structure destroyed? This section is usually the most instructive part of a writeup.

### 7. Borrowed and built

> What you took from others — starter notebooks, other teams' published findings, papers, models — with credit, and what you added on top. Building on shared work is encouraged; claiming it isn't.

### 8. Deployability

> Could a library actually run this? Rough cost/time per 1,000 pages on your hardware, setup complexity, and what a non-ML archivist would need to operate it.

### 9. Limitations and next steps

> What doesn't work, what you'd do with another month, and anything you suspect but couldn't verify.

### 10. Team contributions

> One line per team member on who did what. Solo teams: one line is fine.
