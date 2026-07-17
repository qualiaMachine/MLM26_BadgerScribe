# Challenge rules

## 1. Sponsor

This challenge is organized by the [ML+X](https://hub.datascience.wisc.edu/communities/mlx/) community at UW–Madison together with the UW Digital Collections Center, with support from ML+X sponsors.

## 2. Prizes

This is a non-monetary educational challenge. There are no cash or material prizes. Top teams may be invited to present at the ML+X showcase, and strong pipelines may be adopted for production transcription work at the UW Libraries.

## 3. Team limits

- Maximum team size: 5 participants.
- Team mergers are allowed as long as the merged team stays within the submission limits and the maximum team size.

## 4. Submission integrity

- **Open-weight models, within a 96 GB single-GPU VRAM budget.** Every model in your submitted pipeline — recognizer, layout detector, language router, post-corrector, all of it — must be open-weight, and the pipeline must run end-to-end on a single GPU with at most 96 GB of VRAM (RTX Pro 6000 class — the hardware available on campus). The budget applies to the run as it executes: loading and unloading models between stages is fine, and quantization is allowed and encouraged. Closed-weight API models (GPT, Claude, Gemini) are out of scope anywhere in the submitted pipeline, including "just the post-correction step." Development and prototyping on any model or hardware is fine — the rule binds the submitted run. The limit is honor-system during the challenge and verified by code review at the end.
- **No human transcription of evaluation pages.** Reported predictions must be produced by your pipeline. Manually transcribing (or manually correcting) evaluation pages defeats the purpose and disqualifies the entry. Humans building, tuning, and debugging the pipeline is of course fine — that's the challenge.
- **The evaluation pages are for measuring, not training.** Ground truth is released so you can score yourself; the deal is that you don't fine-tune on the evaluation pages or hand-tune prompts against individual pages' transcriptions. Tune toward them using auxiliary data; measure honestly.
- **External data is allowed and encouraged** for training, fine-tuning, and calibration, provided it is publicly available or lawfully licensed and documented in your writeup. Curating your own transcribed samples from the public UWDC image browsers is explicitly encouraged.
- **The Writeup is the submission.** A Kaggle Writeup without a filled-in submission card — CER numbers from your own `evaluation/score_local.py` run plus a public GitHub repo pinned to the exact commit or tag that produced them — is ineligible. [WRITEUP_TEMPLATE.md](WRITEUP_TEMPLATE.md) is a suggested structure, not a requirement. Checked pass/fail, not graded on prose.
- **There is no leaderboard and no automated scoring; reported numbers must be reproducible.** You score yourself with `evaluation/score_local.py` against the released evaluation set and report the numbers in your writeup — nothing is uploaded and nothing is auto-scored. Before a writeup is recognized, organizers verify it: pipeline re-run from the posted code with the declared models, reproduced CER checked against the reported one (LLM sampling is stochastic; normal run-to-run variation is expected and fine), VRAM budget and open-weights confirmed, and code reviewed for training on or hand-transcription of the evaluation pages. Fabricated numbers disqualify.

## 5. Submission limits

- One Writeup per team; edit and resubmit it as often as you like before the deadline.
- The submitted version at the deadline is your final result — draft or un-submitted Writeups are not considered.

## 6. Data

Evaluation images and their ground-truth transcriptions come from the University of Wisconsin–Madison Digital Collections and are provided for challenge use; respect any rights statements accompanying the source collections. Suggested external datasets listed in [RESOURCES.md](RESOURCES.md) are governed by their own upstream licenses — check them before use.

## 7. License

Submission repos must be public and licensed MIT or Apache 2.0.
