# Orbit

A space exploration quiz for curious minds, with ten questions and a slowly rotating 3D planet.

**[Play Orbit](https://callin44.github.io/space-quiz/)**

## Features

- Ten multiple-choice questions with a live score and progress bar.
- Animated green feedback for correct answers, a red shake for incorrect answers, and explanations after each answer.
- Score-dependent emoji results and a replay button.
- A procedural WebGL planet with rings, seven orbiting asteroids, and a cratered moon. An SVG illustration is used when WebGL is unavailable.
- A narrow, responsive layout with automatic light/dark appearance, keyboard navigation, and reduced-motion support.

## Run locally

Download or clone this repository and open `index.html` directly in a browser:

```sh
git clone https://github.com/callin44/space-quiz.git
cd space-quiz
```

On macOS:

```sh
open index.html
```

On other platforms, double-click `index.html` or use your browser's **Open File** command. You can also open the file in Copilot's integrated browser.

The app works offline. All styles, scripts, illustrations, and shaders are contained in `index.html`; no server, package installation, or build step is required.

## Deployment

GitHub Pages publishes the repository root from the `main` branch. GitHub's built-in Pages build and deployment workflow runs after changes are pushed or merged into `main`.

Monitor deployments in the repository's **Actions** tab. The publishing source is configured under **Settings → Pages**.

## Browser checks

See [.github/copilot-instructions.md](.github/copilot-instructions.md) for a focused answer-check snippet and the quiz and rendering invariants. Exercise correct and incorrect answers, all ten questions, and replay; inspect the illustration with light/dark appearance and reduced motion.
