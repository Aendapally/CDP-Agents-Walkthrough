# CDP-Agents Walkthrough

An interactive walkthrough of the CDP-Agents architecture flow, from reading an application repository to a governed, provisioned cloud architecture. It includes three reference samples, one each for Signal, Sentry and Dify.

## What's in this folder

- `index.html`: the walkthrough. It's a single page with no build step.
- `samples/`: for each sample, the Req JSON (`requirements.json`), the architecture summary (`architecture_summary.md`), the architecture as code (`.yaml`) and the rendered diagram (`.png`).
- `.nojekyll`: tells GitHub Pages to serve the files as they are.

## Publish on GitHub Pages

1. Unzip, then open a terminal in this folder.
2. Create an empty repository on GitHub, without a README or licence, then push this folder to it:

   ```bash
   git init -b main
   git add .
   git commit -m "Add CDP-Agents walkthrough"
   git remote add origin <your-repository-url>
   git push -u origin main
   ```

3. In the repository, open **Settings → Pages**. Under **Build and deployment**, set **Source** to **Deploy from a branch**, choose branch **main** and folder **/ (root)**, then click **Save**.
4. After a minute or two, the page shows the site's URL.

Whether Pages is available for private repositories, and who can view the site, depends on your GitHub plan and your organisation's settings. If Pages is turned off for your organisation, ask your GitHub administrator.

## Presenting

| Key or control | What it does |
|---|---|
| → or Space | Next step |
| ← | Previous step |
| Esc | Back to the overview |
| F | Full screen |
| Click a box | Jump to the step where it appears |
| Click an artifact in the green column | Open its sample |
| **Sample artifacts** | Open the viewer; switch between Signal, Sentry and Dify at the top |
| **Build status** | Colour each component by what ships today, what is in progress and what is planned |
| **Zoom to step** | Follow the active step; turn it off to show the whole diagram |
| **Auto-play** | Advance every 8 seconds |

To link to a step directly, add `#step-6` (or any step number) to the URL.

The page also works offline: open `index.html` in a browser. Without an internet connection it uses system fonts.

## About the samples

The samples are reference architectures written in the CDP-Agents artifact formats, not the output of an end-to-end agent run. Several stages they illustrate (repo scanning, requirement scoring, specialist debate) are not built yet. Each sample follows the CDP-Agents flow against a public repository at a pinned commit, and every detected signal cites the file it came from.

- User counts, throughput and recovery targets are illustrative assumptions. Each `requirements.json` states them.
- The YAML passes the project's diagrams-as-code schema, and the diagrams were rendered with the project's own diagram tool.
- Completeness scores use a fixed rubric across ten non-functional categories, plus functional coverage and conflict resolution. The threshold is 0.85.

| Sample | Source scanned | Licence of the scanned project |
|---|---|---|
| Signal secure messaging | [signalapp/Signal-Server](https://github.com/signalapp/Signal-Server) @ `a15d5cd` | AGPL-3.0 |
| Sentry observability platform | [getsentry/self-hosted](https://github.com/getsentry/self-hosted) @ `d09ff0c` | FSL-1.1-Apache-2.0 |
| Dify AI agent platform | [langgenius/dify](https://github.com/langgenius/dify) @ `6d14cd5` | Apache 2.0 with additional conditions |

The samples describe and link to these projects; they don't include any of their code.

Project: [github.com/Aendapally/Agentic-CDP-Platform](https://github.com/Aendapally/Agentic-CDP-Platform)
