# Implementation plan

Status: Agreed, with the conditions in section 7 · Date: 2026-11-09

Based on `docs/study_design.md` (agreed 2026-11-09). Paths are relative to the project root. A unit is one numbered task, sized for about 5–15 minutes of review. This plan contains no implementation code.

## 1. Project overview

- **Project:** an interactive marimo app that an instructor drives in a live lecture to show how training and test error diverge as a polynomial model becomes more flexible.
- **Study design:** `docs/study_design.md`, agreed 2026-11-09 with provisional items.
- **Language and software environment:** Python, in a `uv` environment recorded with `pyproject.toml` and a lockfile. Dependencies: marimo, NumPy and matplotlib only. Browser compatibility is confirmed in unit 4.1. Documented at planning time: no environment file exists yet, `uv` 0.11.25 is available, and the system Python is 3.9.6.
- **Data and documentation:** simulated in the app; `data/raw/` stays unused. No real or sensitive data are involved.
- **Final deliverables:** the published marimo app, check results for section 6 of the design, and rerun instructions in `README.md`.

### Working principles

- Complete one requested unit at a time, in dependency order.
- Verify substantive correctness against the agreed requirements, not merely whether the code runs.
- Keep code readable and dependencies minimal. Preserve unrelated content.
- Do not silently resolve open methodological choices. Ask before changing the agreed design.
- Create commits, push, enable GitHub Pages, or publish only on explicit instruction.

## 2. Project organisation

| File or folder | Purpose |
|---|---|
| `01_overfitting_app.py` | The app, including the simulation and fitting functions |
| `02_check_behaviour.py` | Repeatable checks; imports the app's functions so the checks test the code the app runs |
| `results/tables/`, `results/figures/` | Check results |
| `.github/workflows/` | Automatic build and publishing |
| `pyproject.toml`, lockfile | Declared environment |

The app stays in one file because that simplifies the browser export; `src/` stays empty unless that changes. Run order: `02_check_behaviour.py`, then the app or its export. `.venv/` and local test builds stay out of Git.

## 3. Implementation overview

| Unit | Purpose and output | Depends on | Status |
|---|---|---|---|
| 1.1 | Set up and record the environment | Decisions A, B | Agent checks passed; awaiting researcher review; not committed |
| 2.1 | Simulate training and test data | 1.1, Decision C | Not started |
| 2.2 | Fit polynomials and calculate errors | 2.1 | Not started |
| 2.3 | Check behaviour across many samples; propose limits | 2.2 | Not started |
| 3.1 | Data view with controls | 2.3, Decision D | Not started |
| 3.2 | Linked error view | 3.1 | Not started |
| 3.3 | Opening dataset and new-sample button | 3.2, Decision E | Not started |
| 3.4 | Lecture-hall layout and speed | 3.3 | Not started |
| 4.1 | Browser export, tested locally | 3.4 | Not started |
| 4.2 | Automatic build workflow | 4.1 | Not started |
| 4.3 | Publish to GitHub Pages | 4.2, Decision F | Not started |
| 5.1 | Full rerun and final review | All | Not started |

### Researcher decisions

| | Decision | Current position | Blocks |
|---|---|---|---|
| A | Approved software sources | Agreed: PyPI and uv's standard Python distribution | 1.1 |
| B | Plotting library | Agreed: matplotlib | 1.1 |
| C | Starting values | Agreed as starting values: 25 training points (adjustable 15–100), 500 test points, x from 0 to 1, one full sine wave of height 1, noise standard deviation 0.1–0.6 (default 0.3) | 2.1 |
| D | Final degree limit, noise and sample ranges, axis cap | Open: decide from the unit 2.3 evidence | 3.1 |
| E | Opening dataset; whether changing sliders redraws the sample | Opening dataset open. Agreed default: the same points are kept when only flexibility changes | 3.3 |
| F | Public repository and page; English only; no offline version | Recommended as in the design; researcher confirms before publishing | 4.3 |

### Completion criteria for every unit

- [ ] Changes have been reviewed and can be explained.
- [ ] The unit runs without unexplained errors.
- [ ] Required checks have been run and their results inspected.
- [ ] Failures and blocking questions have been resolved; remaining limitations are recorded.
- [ ] Completion evidence is recorded in the unit, keeping agent checks separate from researcher review and stating whether changes are committed.

## 4. Implementation units

### Stage 1 — Setup

**Stage objective:** a recorded environment that everything else runs in.

#### Unit 1.1 — Set up and record the environment

- **Research purpose:** make the work reproducible (design section 8).
- **Dependencies and inputs:** Decisions A and B.
- **Implementation requirements:** declare the Python version and the three dependencies. Explain any version requirement. Add a short environment and run section to `README.md`, including where commands execute.
- **Deliverable:** `pyproject.toml`, lockfile, `README.md` update.
- **Verification requirements:** the Python path and version come from the project environment; all three packages import at the locked versions; `.venv/` is not tracked by Git.
- **Researcher decision point:** None remaining.
- **Completion evidence:** Agent checks run 2026-11-09 on the researcher's computer (macOS 27.0.1, arm64) inside the Claude Code sandbox. Awaiting researcher review; changes are not committed.
  - **Changes:** `pyproject.toml` and `uv.lock` added; "Project environment and running" section added to `README.md`.
  - **Version requirement:** Python `>=3.12,<3.13`. The locked NumPy 2.5.4 requires Python 3.12 or later; the upper limit keeps one Python version throughout.
  - **Python path and version — passed:** `uv run --locked` used `.venv/bin/python3`, Python 3.12.13; `sys.prefix` is the project's `.venv`.
  - **Locked imports — passed:** marimo 0.25.2, NumPy 2.5.4 and matplotlib 3.11.2 imported from `.venv`; `__version__`, installed metadata and `uv.lock` agree. `uv lock --check` passed.
  - **`.venv/` not tracked — passed:** `git ls-files` lists 0 files under it; `git check-ignore` matches `.gitignore` line 4. `.venv-python/` (below) is also ignored.
  - **Sandbox deviation:** uv's shared folders were blocked, so the cache was in `$TMPDIR` and Python was installed in the project-local `.venv-python/` (66 MB). `.venv` links to that Python. Matplotlib warned that `~/.matplotlib` is not writable and used a temporary folder; imports still succeeded.
  - **Not checked:** setup outside the sandbox with the researcher's default uv folders; whether these locked versions and Python 3.12 work in browser-based Python (unit 4.1). The dependencies may need adjusting then.
  - **Researcher review:** pending.

### Stage 2 — Simulation and statistics

**Stage objective:** correct calculations, and evidence for the settings the app will allow.

#### Unit 2.1 — Simulate training and test data

- **Research purpose:** generate data following the specified pattern (design sections 3 and 6).
- **Dependencies and inputs:** 1.1; Decision C.
- **Implementation requirements:** a seeded function draws independent training and test sets from the sine-like pattern plus noise. The test set is larger and fixed in size.
- **Deliverable:** the data function in `01_overfitting_app.py`; checks in `02_check_behaviour.py`.
- **Verification requirements:**
  - The same seed gives identical data; different seeds differ.
  - With noise at zero, points lie exactly on the true curve.
  - In a large sample, the noise has mean near 0 and the requested standard deviation.
  - Training and test sets have the requested sizes.
- **Researcher decision point:** None remaining.
- **Completion evidence:** Not started.

#### Unit 2.2 — Fit polynomials and calculate errors

- **Research purpose:** implement the analysis in design section 5.
- **Dependencies and inputs:** 2.1.
- **Implementation requirements:** fit each degree by ordinary least squares on training data only, and calculate root mean squared error for both sets. Rescale the input if needed for numerical stability at high degrees.
- **Deliverable:** fit and error functions; known-answer checks in `02_check_behaviour.py`.
- **Verification requirements (small examples with known answers):**
  - Data lying exactly on a degree-3 polynomial are recovered, with error near 0, at degree 3 and above.
  - Errors of 3 and 4 give a root mean squared error of √12.5 ≈ 3.54.
  - Fitted values match a separate least-squares routine.
  - Changing the test data leaves the fit unchanged.
- **Researcher decision point:** None.
- **Completion evidence:** Not started.

#### Unit 2.3 — Check behaviour across many samples

- **Research purpose:** provide the credibility and sensitivity evidence in design section 6, and evidence for the final limits.
- **Dependencies and inputs:** 2.2.
- **Implementation requirements:** repeat the simulation 1,000 times per setting across the grid of allowed sample sizes and noise levels. Report mean and median test error by degree.
- **Deliverable:** a table in `results/tables/`, a figure in `results/figures/`, and proposed final limits.
- **Verification requirements:**
  - Training error never rises with degree within any single sample, apart from rounding tolerance.
  - Mean and median test error fall and then rise; any difference between them is reported.
  - Larger samples reduce overfitting.
  - Any setting with unstable fits is listed.
- **Researcher decision point:** Decision D, after reviewing this evidence. Blocks Stage 3.
- **Completion evidence:** Not started.

### Stage 3 — App

**Stage objective:** the two linked views and controls, readable in a lecture hall.

#### Unit 3.1 — Build the data view with controls

- **Research purpose:** show the fit against the data and truth (design sections 4 and 5).
- **Dependencies and inputs:** 2.3; Decision D.
- **Implementation requirements:** sliders for flexibility, sample size and noise; data plot; fitted curve; true-curve toggle; the plain labels from the design.
- **Deliverable:** the first app view in `01_overfitting_app.py`.
- **Verification requirements:** each control changes the right element; the degree is shown; degree stays below the safe limit for the current sample size, including at the smallest sample size.
- **Researcher decision point:** None.
- **Completion evidence:** Not started.

#### Unit 3.2 — Add the linked error view

- **Research purpose:** show the divergence of training and test error, the app's central message.
- **Dependencies and inputs:** 3.1.
- **Implementation requirements:** plot training and test "typical prediction error" against flexibility, mark the current degree, cap the axis using Decision D, and show clearly when values go off the scale.
- **Deliverable:** the second view in `01_overfitting_app.py`.
- **Verification requirements:** plotted values equal those from the unit 2.2 functions; the marker follows the slider; an off-scale value is visibly flagged in a forced example.
- **Researcher decision point:** None.
- **Completion evidence:** Not started.

#### Unit 3.3 — Add the opening dataset and new-sample button

- **Research purpose:** give a clear opening illustration while showing sampling variation.
- **Dependencies and inputs:** 3.2; Decision E.
- **Implementation requirements:** present a few candidate seeds where underfitting, good fit and overfitting are clear; add the new-sample button; keep the same points when only flexibility changes.
- **Deliverable:** fixed opening seed and button in `01_overfitting_app.py`.
- **Verification requirements:** the page opens with the same data on every reload; the button changes the data; changing flexibility alone leaves the points unchanged.
- **Researcher decision point:** approve the opening dataset. It is presented in class as an illustration, not a typical result.
- **Completion evidence:** Not started.

#### Unit 3.4 — Refine lecture-hall layout and speed

- **Research purpose:** meet the brief's legibility and responsiveness requirements.
- **Dependencies and inputs:** 3.3.
- **Implementation requirements:** large type, thick lines, minimal text, almost no notation.
- **Deliverable:** refined layout in `01_overfitting_app.py`.
- **Verification requirements:** agent checks are screenshots at projector resolution and measured update time (target: under 200 ms locally).
- **Researcher decision point:** researcher reviews whether the text and message suit the students.
- **Completion evidence:** Not started.

### Stage 4 — Deployment

**Stage objective:** a working page on GitHub Pages.

#### Unit 4.1 — Export and test in the browser locally

- **Research purpose:** confirm the app works as browser-based Python.
- **Dependencies and inputs:** 3.4.
- **Implementation requirements:** export to WebAssembly and open it from a local web server. The test build is not committed.
- **Deliverable:** export command in `README.md`; recorded load and update times.
- **Verification requirements:** all controls work in the browser; no console errors; first-load and update times are recorded; displayed results match the native run.
- **Researcher decision point:** None.
- **Completion evidence:** Not started.

#### Unit 4.2 — Write the automatic build workflow

- **Research purpose:** make publishing repeatable.
- **Dependencies and inputs:** 4.1.
- **Implementation requirements:** a GitHub Actions workflow that builds with the locked dependencies.
- **Deliverable:** a workflow file in `.github/workflows/`.
- **Verification requirements:** the workflow is reviewed and its build command run locally. The real workflow can only be verified in unit 4.3.
- **Researcher decision point:** None.
- **Completion evidence:** Not started.

#### Unit 4.3 — Publish to GitHub Pages

- **Research purpose:** deliver the page for lecture use.
- **Dependencies and inputs:** 4.2; Decision F.
- **Implementation requirements:** with explicit permission for each step, commit on a feature branch, push, open a pull request, enable GitHub Pages and publish.
- **Deliverable:** the public page; its URL in `README.md`.
- **Verification requirements:** the workflow succeeds; the URL loads in a clean browser session; all controls work there.
- **Researcher decision point:** Decision F and permission for each outward-facing step.
- **Completion evidence:** Not started.

## 5. Full rerun and final review

Unit 5.1 covers this section.

- **Execution:** from a fresh clone and environment, run the known-answer and behaviour checks, regenerate results, and rebuild the export.
- **Expected outputs:** the check table and figure in `results/`, and the browser export.
- **Checks across units:** the settings in the app, the checked settings and the design document match; fixed-seed results reproduce; the rebuilt export behaves like the published page.
- **Reproducibility:** seed, lockfile versions, agreed settings and run commands in `README.md`.
- **AI-use records:** add the AI-use section to `README.md`: tasks assisted, extent of automation, what the researcher reviewed, provider, model, application (Claude Code), access date, memory or context use, and instructions and skills by Git revision. Unavailable details are marked unknown. AI assists with development only and produces no research observations.
- **Scientific review:** move provisional items to agreed in the design, review the sensitivity results, and confirm claims stay within scope (no claims about learning).
- **Researcher review, which agent checks do not replace:** on the lecture-room computer, check legibility from the back of the room, responsiveness, loading on the room's internet, and readiness for class.

### Final completion criteria

- [ ] All required units meet their completion criteria.
- [ ] The full rerun works in the documented order and produces the expected outputs.
- [ ] Results and sensitivity analyses have been reviewed against the agreed design.
- [ ] Another researcher has sufficient instructions to rerun the work.
- [ ] Required AI-use records are present, with unavailable or inapplicable details identified.
- [ ] Remaining limitations and incomplete checks are clearly reported.
- [ ] Researcher has completed the lecture-room review.
- [ ] Changes have been reviewed; Git checkpoints have been made if requested.

**Final evidence:** Not started.

## 6. Open issues and revisions

| Affected units | Issue or proposed change | Reason | Researcher decision |
|---|---|---|---|
| 2.3 | Report both mean and median test error | A few extreme high-degree fits can dominate an average | Agreed |
| 1.1 | Approved software sources remain a placeholder in `AGENTS.md` | Decision A is recorded here; `AGENTS.md` was not changed | Open: update `AGENTS.md` if wanted |

When a revision is needed, explain its consequences for the design and completed work, obtain agreement for substantive changes, and revisit any affected units.

## 7. Plan agreement

- [x] The plan covers the agreed study design and intended outputs.
- [x] Units are manageable, with clear requirements, deliverables, and dependencies.
- [x] Each unit has concrete verification requirements.
- [x] Open methodological decisions have explicit researcher decision points.
- [x] The final rerun and scientific review are included.

**Researcher agreement:** the researcher agreed this plan on 2026-11-09 with the recommended defaults. Conditions: Decisions D and E remain open until their evidence is reviewed, and Decision F must be confirmed before publishing.
