# Status

Updated Sat 14 Nov 2026, 15:47 America/Los_Angeles (`EVENT_TZ=America/Los_Angeles`).

**Phase:** Phases 1 to 3 done and Phase 4 (Tier 2) done locally; the 18:00 gate is judged early below. **Blocked on one thing: pushing to GitHub** (see Blockers). The build started late (about 14:15), so Tier 3 is cut (DECISIONS.md, 14:28). Sun 13:00 freeze and Sun 21:00 ready-to-submit are unchanged.

How to resume: in an Ubuntu window, `cd ~/PROJECT_DWTS && claude --continue --permission-mode auto`, then "Continue prompts/build.md from STATUS.md". Pipeline: `python -m dwts.run <stage>` (grid, db, series, ancillary, refs, s1, pairs, claims, listen, tiles); background runs with `bash scripts/run_bg.sh <tag> <stage> [args]`, logs in `$DWTS_OUT/logs/`. When waiting on a job, match it with `pgrep -f "[d]wts.run <stage>"` (a plain pattern matches the waiting shell itself).

## Blockers

- **GitHub push, denied by Claude Code's auto-mode safety check** at 15:33 and again at 15:35. `scripts/push_tiles.sh` creates the orphan `tiles` branch on GitHub (296 MB of PMTiles and the 19 MB listening grid, every file under 50 MB), and `git push github main` then deploys the app with the new Pages workflow. build.md asks for both pushes, but the check stops them, so a person must allow them: either run them yourself (below), or add a Claude Code permission rule for `git push` to the `github` remote. Until then the deployed site is still the placeholder, and the new workflow must not reach `main` on GitHub before the `tiles` branch exists.
  - `bash scripts/push_tiles.sh` (first push: creates the branch, no force)
  - `git push github main`
- The second tile push (Sun 13:00) replaces the orphan branch, which needs `bash scripts/push_tiles.sh --force`. That is a force-push of the `tiles` branch only; build.md authorizes it, but the safety check will likely stop it too.

## Gate 18:00 (judged at 15:47): Tier 1 boxes

- [x] HH and HV in dB for 16 and 28 Sep on the grid with the valid mask; the Gandak channel is the darkest feature on both dates. Evidence: `$DWTS_OUT/figures/hh_lead_bagaha.png` (Bagaha crop, valid pixels, -25 to 0 dB: the channel is black on 16 Sep, and the dark floodplain is several km wide on 28 Sep).
- [x] Calibration offsets and threshold tiles for the lead and control pairs. `$DWTS_OUT/rasters/gandak_{20260916-20260928,20260823-20260904}_{calib,thresholds}.json`, summarised in `results.json` (calibration, thresholds). Lead: per-part branch, HH -0.335 / +0.343 dB, HV -0.917 / -1.207 dB, 398,398 stable px; control: per-part, HH -0.449 / +0.165 dB, HV -0.536 / -0.370 dB, 399,409 stable px. Every part has at least 15 passing tiles, so no part falls back to the log-ratio cut alone.
- [x] Log-ratio and two-sided flood maps for both pairs; the lead pair's flooded area lines up with Sentinel-2 on 29 Sep. `gandak_{pair}_flood_{lr,twosided}.tif`; side by side in `$DWTS_OUT/figures/nisar_vs_s2_20260929.png` (40 km window at 82.79 E, 27.28 N: NISAR's new open water matches the Sentinel-2B water patches closely).
- [x] Claim 1 table: F1 with interval, precision, recall and pixel count for each method on the same pixels, and the paired difference against the margin. `app/data/results.json` claim1: 230,621 px, 11,288 new water, 253 blocks. NISAR two-sided F1 0.051 (0.014 to 0.121), Sentinel-1 two-sided 0.172 (0.078 to 0.267), difference -0.121 (-0.204 to -0.030): **non-inferiority not passed**, reported with the change log's fallback wording. Plus the log-ratio rows, DSWx-S1 (0.064), DSWx-NI null with its reason, per land cover rows, claim 1a (NISAR F1 0.676, HV adds +0.002, interval +0.001 to +0.004, passes) and the 4 Sep check pair (-0.126).
- [x] Claim 2: excess km² with interval for vegetation and cropland, and the secondary note. Vegetation 17.04 km² (15.02 to 19.46), **passes**; cropland -105.27 km² (-146.3 to -60.0), no signal; sensitivity with offsets shifted by half the tile spread: 10.93 and 23.88 km²; secondary test dropped by the team's change log (recount flagged, DECISIONS 15:24). `gandak_hidden.tif` written.
- [ ] The app loads on a phone with steps 1 and 2 on real tiles. **Locally yes, deployed no (blocked).** `scripts/check_app.py --strict http://localhost:8765/` (real tiles through `scripts/serve.py`): iPhone 13 first draw 2.0 s, desktop 1.7 s, 0 console errors, 0 page errors, every step and Explore visited, slider dragged, tap and chart on step 4, audio only after the Listen click. Screens: `$DWTS_OUT/screens/{iphone13,desktop}_step{1..5}.png`, `_step5_naive.png`, `_explore.png`.
- [ ] Video and links (due Sun 19:00). A draft rendered from the local site works: `$DWTS_OUT/video/dwts_30s.mp4`, 30.0 s, 1920 x 1080 H.264, captions burned in, the flooded field's sound mixed in at 20.5 s (peak -12 dBFS). The final cut must be recorded from the deployed site.

Result numbers come only from `results.json`; `scripts/check_results.py` passes.

## Tier 2 (done locally, needs the deploy to count)

- Step 3: hidden-steps layer, the km² figure, both SVG diagrams with the "Diagram made with AI assistance" note, "evidence, not verified".
- Step 4: tap, chart, Listen button, mute; three places from `dwts/places.py` (`$DWTS_OUT/places.json`): a flooded field 84.0116 E 27.1930 N, a flooded grove 84.0221 E 26.9546 N, a dry town 84.3299 E 27.1534 N. Sound checks: `tests/test_sound.py` and `scripts/render_sounds.py` (13 clips in `$DWTS_OUT/sound/`, peaks at most -10.3 dBFS, 3.19 s each, pitch follows dB in both channels). **A person should listen to them once.**
- Step 5: both numbers with intervals, claim 1a, the naive-difference toggle with hatched gaps, three failure examples from the failure gallery (`$DWTS_OUT/gallery.csv`; 90% of the referee's new water NISAR missed was already dark on 16 Sep), and the limits box word for word.
- The tour: every step linkable as `#step=N`, Explore with 16 layers and the Areraj pin.
- DSWx-NI: its drop and reason are in results.json and SUBMISSION.md.

## Done earlier today

- Gate 12:00 (late, judged 14:50): flood visible along the Gandak (`$DWTS_OUT/figures/gate12_flood.png`).
- Phase 1: grid, seven dates of HH and HV dB, ancillary layers, references, Sentinel-1 mosaics (54 bursts per date), listening grid (84 chunks, 19.3 MB). Times and peaks in `$DWTS_OUT/logs/run.jsonl`.
- `SUBMISSION.md` written by `scripts/make_submission.py` from results.json; the video link line waits for the upload.
- Tests: `pytest -q` 19 passed.

## Next

1. A person allows or runs the two pushes; then check the deployed site with `scripts/check_app.py https://dmanpark.github.io/dancing-with-the-sars/`.
2. Polish within Tier 2 until Sun 13:00 (tour on a phone, card text), then the freeze.
3. Sun: final tile push, the video from the deployed site, CITATIONS link check, README, independent review, ready to submit by 21:00.

## Pre-event notes (still true)

- Pre-event prep is in this file's git history (eb358c3) and DECISIONS.md: 990 files, 67.2 GB downloaded and verified; public repo https://github.com/dmanpark/dancing-with-the-sars (remote `github`).
- Memory: at most two heavy jobs at once, each process under 8 GB (today's peak 4.5 GB, the pairs stage).
- `~/dwts-main` is a second worktree on a detached HEAD so `main` can live in `~/PROJECT_DWTS`.
