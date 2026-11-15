# AI use

What AI tools and models this project used, before and during NASA Space Apps 2026, and for what. Kept current as the work goes on.

## Tools and models

- **Claude Code** (Anthropic), model **Claude Opus 5.5** (`claude-opus-5-5`). Checked 3 Nov 2026.

No generative image, video or audio models are used. The two diagrams in the app are simple SVG drawings coded from basic shapes; each carries a visible note "Diagram made with AI assistance", and the team may redraw them.

## Before the event (to 14 Nov 2026, 09:00 local)

- **Planning and data lists.** Every commit up to 4 Nov 2026 (UTC) has Claude Opus 5.5 as co-author: the planning documents in `docs/`, the scene and reference lists in `data/`, the search and download tools in `prep/`, the quicklook images in `quicklooks/` (plotted from radar data by `prep/quicklook.py`, not generated) and the Claude Code prompts in `prompts/`. The team reviewed and directed the work.
- **Machine setup**, 3 Nov 2026. Claude Code ran `prompts/setup.md` and the pre-event part of `prompts/build.md` on the team's 3090 machine: WSL2 checks, Miniforge, the `dwts` conda env, Claude Code inside Ubuntu, and this repository's GitHub setup and placeholder page. People typed every password and login themselves.
- **Preliminary tests.** Claude Code runs `prompts/prelim-tests.md`; its code in `prelim/` is throwaway and is not reused in the submission.

## During the event (from Sat 14 Nov 2026, about 14:15 local; the build started late)

- **The whole build** is carried out by Claude Code (Claude Opus 5.5) following `prompts/build.md`: the pipeline in `dwts/`, the tests in `tests/`, the scripts in `scripts/`, the app in `app/`, the video renderer in `video/`, and the text of `SUBMISSION.md`, `STATUS.md` and `DECISIONS.md`. The model wrote the code and ran it on the team's machine; every number comes from `results.json`, written by the pipeline.
- **Subagents.** Claude Code started Claude Opus 5.5 subagents for independent parts: the app shell (HTML, JavaScript, CSS, sound mapping, the app check script) and the final independent review. The main session integrated and checked their work.
- **Diagrams.** The two diagrams in step 3 are SVG drawings coded from basic shapes by the model, each marked "Diagram made with AI assistance".
- **What a person did.** Logins, the video upload and the submission form, and listening to the sound once.
