# FAQ

**Can I use GPT / Claude / Gemini anywhere in my pipeline?**
Not in the submitted run — closed-weight API models are out of scope, including "just for post-correction." The point of the challenge is a pipeline a library can run on-prem; a solution that routes pages through a frontier API doesn't transfer. Prototype against anything you like; the submitted pipeline must be open-weight models throughout, running within the single-GPU 96 GB VRAM budget.

**Is the 96 GB limit per model or for the whole pipeline?**
It's a hardware budget, not a per-model cap: the submitted pipeline must run end-to-end on a single GPU with at most 96 GB of VRAM. Spend it however you like — one big quantized model, or a layout detector, two recognizers, and a post-corrector loaded and unloaded between stages — as long as the run executes within the budget.

**What do I train and tune on?**
Start with the released calibration set ([DATA.md](DATA.md)) — labelled UW pages from the same four collections that, unlike the evaluation pages, you can train and tune on freely. It's small by design: its main job is helping you pick the right models and pipeline scaffolding for this material, not feeding large-scale training. When you need volume, [RESOURCES.md](RESOURCES.md) maps public auxiliary datasets (Bentham, NARA RG 109, Alfred Escher, BLN600, IAM…) to the evaluation categories, and the UW collections are publicly browsable if you want to transcribe more pages yourself.

**Can I fine-tune?**
Yes, encouraged — even ~100 curated pages of the survey notebooks' drawn tables might yield significant gains on that category. The fine-tuned model must still run within the 96 GB VRAM budget at inference, and your weights must be public or reproducible from the public base plus your published adapter. Document your data in the writeup.

**The ground truth is public — what stops me from just copying it (or training on it)?**
Nothing but the rules, the code review, and the fact that there's nothing to gain — no prize, no leaderboard, no score anyone is ranked by. A copied number would only pollute your own writeup, and writeups considered for recognition are verified by re-running their posted code. Copying or hand-editing predictions, training on the evaluation pages, or tuning prompts against individual pages' transcriptions all disqualify. The evaluation set is for measuring; hand-transcribing *other* pages from the same collections to build training data is fine and encouraged.

**The Kade letters are in German — do I have to handle that?**
Yes, if you want a good score: the metric macro-averages across categories, so tanking the German category costs you a full quarter of the weight. Pre-1940 German handwriting (Kurrent) is a different script from English cursive; see the Alfred Escher dataset in [RESOURCES.md](RESOURCES.md).

**How exactly is my text compared to the ground truth?**
Verbatim — casing, punctuation, and historical spelling all count, and "corrections" of the original spelling count as errors. The only normalization is collapsing whitespace runs to single spaces before scoring. Read [`docs/transcription_conventions.md`](docs/transcription_conventions.md) and [`evaluation/metric.py`](evaluation/metric.py); you can score yourself locally with `evaluation/score_local.py`.

**I don't have a GPU.**
The starter notebooks target models that fit Kaggle Notebooks' free GPU quota, and hosted endpoints serving named open-weight checkpoints are fine for prototyping. See the Compute section of [RESOURCES.md](RESOURCES.md).

**My team is just me. / My team is five people.**
Both fine. Teams of 1–5. Reflect honestly on contributions in the writeup.

**I'm not at UW–Madison.**
Welcome — the challenge is fully open and everyone submits the same way. You won't have access to UW-only hosted compute, but nothing here requires it.

**Will there be a leaderboard?**
No — no leaderboard, live or otherwise, and no automated scoring. That's the nature of the hackathon format: there's no solution CSV to upload anywhere — everything is a writeup. You measure your own CER at any time with `score_local.py` against the released evaluation set and report the numbers in your writeup; make sure they're reproducible from your posted code, because writeups in contention for recognition get re-run. Writeups are public once submitted, so Discussion is where you'll see how other teams are doing.

**What happens to the winning pipelines?**
Organizers review the submitted writeups, re-run the code behind the ones in contention, then announce winners — and the real payoff: the UW Digital Collections Center is looking for a process it can adopt for production transcription of its collections, with minimal staff effort. The best outcome for this challenge is your pipeline running in an actual library.
