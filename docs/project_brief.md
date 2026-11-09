# Project brief

Status: Agreed, with the provisional items listed below · Date: 2026-11-09

## Research intention

Help undergraduates with little mathematical background understand overfitting: a more flexible model can fit its training data better while predicting new data worse.

This is a teaching resource, not an empirical study. The guiding question is: *can one interactive visual example make the divergence between training and test error clear during a live lecture?*

## Intended outputs

- A marimo app that the instructor drives in a live lecture, published as a web page.
- **Content:** one simulated polynomial regression example. The instructor changes model complexity, sample size and noise, and the class sees the fitted curve and the training and test errors update.
- **Useful means:** the main idea is readable from the back of a lecture hall, with large visuals, minimal text and almost no notation. Controls respond with no noticeable delay.

## Available data and resources

- **Data:** none needed. The app generates simulated data in the browser, so `data/raw/` stays unused and no real or sensitive data are involved.
- **Software:** marimo exported to WebAssembly (WASM), which runs Python inside the viewer's browser without a server. GitHub Actions builds and publishes the page to GitHub Pages.
- **Existing work:** the project template only; no app code yet.

## Scope and practical constraints

- **Included:** the single example above, and automatic deployment.
- **Excluded:** regularisation, cross-validation, other model families, exercises and self-study material, and any collection of student data or evaluation of learning. The project therefore makes no claim that the app improves learning.
- **Constraints:** the app must work in a browser on the lecture-room computer. Only packages that run in browser-based Python can be used, and the first load downloads that Python environment, so it may take some time.

## Responsibilities and boundaries

- **Researcher decides:** teaching content and messages, any change of audience or scope, and whether the app is ready for class.
- **Agent may:** implement agreed steps locally and run checks.
- **Agent asks first:** commits, pushes, enabling GitHub Pages, publishing, or using any paid service.
- **Permitted services:** GitHub, GitHub Actions and GitHub Pages for simulated data and code only.

## Open questions

- Will the repository and page be public? GitHub Pages sites are generally publicly reachable, and availability on private repositories depends on the GitHub plan.
- Should the app be in English only, or also in another language?
- Must it work without reliable lecture-room internet? If so, the design needs a fallback, such as loading the page ahead of time.

## Agreement

The researcher agreed this brief on 2026-11-09, including the audience, live-lecture use, polynomial content, absence of evaluation, and the boundaries above. The three open questions remain provisional and should be resolved in the study design. Update this brief if the project's purpose or scope changes.
