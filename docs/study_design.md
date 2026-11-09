# Study design

Status: Agreed with open questions · Date: 2026-11-09

Based on `docs/project_brief.md`. **Agreed** items come from the brief or the researcher's explicit choices. **Provisional** items were accepted as starting values and are to be confirmed during checking or as listed in section 9.

## 1. Research question, objectives, and intended claims

- **Question (Agreed):** can one interactive visual example make the divergence between training and test error clear during a live lecture?
- **Objective:** give the instructor a dependable demonstration for undergraduates with little mathematical background.
- **Intended claims:** the app *demonstrates* a known statistical phenomenon. Whether the demonstration is clear is the instructor's professional judgement.
- **Not supported:** claims that students learn from it, or that it beats other teaching methods.

## 2. Study approach and rationale

This is the design of a teaching material using a controlled simulation; it is not an empirical study. Simulation suits the purpose because the true pattern is known, so students can compare any fit with the truth, and no real data are needed.

**Lesson students should leave with:** fitting the data you have more closely is not the same as predicting new data better. The app shows this through three stages: underfitting at low flexibility, a good fit in the middle, and overfitting at high flexibility.

## 3. Population, setting, and data

- **Setting:** instructor-led undergraduate lectures. Students watch and do not use the app themselves.
- **Data:** simulated in the browser; there are no participants or external sources. Each point is one simulated observation.
- **True pattern (Agreed):** a smooth sine-like wave, with one input variable and random noise added to the outcome.
- **Training and test data (Provisional):** the two sets are drawn independently from the same pattern. The test set is larger and fixed in size, so that test error is less erratic than training error.
- **Sample size (Provisional):** a small default training set, around 20–30 points, adjustable by the instructor. Overfitting appears readily at this size.

## 4. Concepts, measurements, and research materials

| Concept or material | How it appears | Rationale or uncertainty |
|---|---|---|
| Model flexibility | Slider labelled "model flexibility", polynomial degree 1 to about 15, with the degree shown (Agreed) | Upper limit to be confirmed against fit stability |
| Prediction error | Root mean squared error, labelled "typical prediction error", formula hidden (Agreed) | Same units as the outcome, and matches what the fit minimises |
| Noise | Control with plain labels | Range set so the pattern stays visible (Provisional) |
| Sample size | Training-point control | Larger samples should visibly reduce overfitting |
| Sampling variation | Fixed opening dataset plus a "new sample" button (Agreed) | Shows that flexible fits change greatly between samples |

## 5. Analysis strategy and justification

Each polynomial is fitted by ordinary least squares to the training data only. Test data never influence the fit.

**Two linked views (Provisional):**

1. The data, the fitted curve and, optionally, the true curve.
2. Training and test error against flexibility, with the current degree marked.

**Expected behaviour:** training error never rises as degree increases, while test error typically falls and then rises. A single sample may give a jagged test curve, and the new-sample button makes that visible.

The opening dataset is chosen deliberately because it shows the pattern clearly, so the instructor should present it as an illustration rather than as a typical result, and should use the new-sample button in class. Test error can reach very large values at high degrees, so the error axis needs a sensible limit (Provisional), with the display making clear when values go off the scale.

## 6. Quality checks, uncertainty, and robustness

- **Data and materials:** confirm the simulated data follow the specified pattern and noise settings.
- **Implementation correctness:** check fits and error calculations on small examples with known answers, such as data lying exactly on a polynomial.
- **Scientific credibility:** across many simulated samples, confirm that average training error never rises with degree and average test error shows the expected U-shape. Report both mean and median test error, because a few extreme high-degree fits can dominate the mean.
- **Sensitivity:** repeat that check across the allowed sample-size and noise settings, and limit any combination that behaves unstably. Degree must stay well below the number of training points.
- **Classroom suitability (researcher review):** legibility from the back of a room, responsiveness, and the deployed page working on a lecture-room computer. Agent checks do not replace this review.

## 7. Ethics, access, and participant protection

No participants, personal data, or student data are involved, so ethics review does not appear to be needed; the researcher's institutional rules take precedence. Only code and simulated data go to GitHub. AI assists with development and does not produce research observations. AI use will be recorded in `README.md`.

## 8. Expected outputs and reproducibility

| Output | What it contains | Connection to the question |
|---|---|---|
| Published marimo app | Linked views and controls | The demonstration itself |
| Check results | Evidence for section 6 | Shows the demonstration behaves as claimed |

To preserve: the app code, fixed random seed, dependency versions, and agreed settings.

## 9. Limitations, decisions, and agreement

- The app uses one input, one model family, and one simulated pattern, so it does not show how overfitting appears with many predictors.
- Polynomial instability at the edges is a particular way of overfitting, and students may wrongly take it as the general form.
- The test set is an idealised large sample from the same pattern. Real new data may differ, so this shows best-case generalisation.
- Without cross-validation or regularisation (excluded by the brief), the app shows the problem but not its remedies; the instructor covers those separately.
- Results are simulated and are not evidence about any real population.
- Learning is not evaluated.

| Decision | Choice and rationale | Status / information needed |
|---|---|---|
| True pattern, randomness, error measure, flexibility range | As above | Agreed; upper limit to be confirmed by checks |
| Sample sizes, noise range, linked views, axis limit | As above | Provisional; confirm during checks |
| Repository and page visibility | Public: simplest for GitHub Pages and no sensitive content | Provisional; researcher confirms before publishing |
| Language | English only | Provisional; affects on-screen text |
| Unreliable lecture-room internet | Open the page before the lecture and keep the tab open; no separate offline version | Provisional; depends on the researcher's knowledge of the room |

Sections for surveys, experiments, and qualitative research are omitted because they do not apply.

**Researcher agreement:** the researcher agreed this design on 2026-11-09, on the condition that the provisional items above remain to be confirmed. When methods change, explain why and update the affected sections.
