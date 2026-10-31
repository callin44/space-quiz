# Orbit repository instructions

## Running and focused checks

The app runs directly from `index.html` using a `file://` URL. Open that file in the integrated browser; on macOS, `open index.html` opens it in the default browser. Keep the app runnable offline as a single HTML file, with inline CSS, JavaScript, SVG, and GLSL. Do not introduce a server, external assets, package dependencies, or a bundling step.

For a focused correct-answer check, run this in the page's browser console:

```js
document.getElementById("restart").click();
document.querySelectorAll(".answer")[questions[0].correct].click();
console.assert(score === 1, "Correct answer awards one point");
console.assert(document.querySelectorAll(".answer:disabled").length === 4, "Answers lock");
console.assert(document.querySelector(".answer.correct"), "Correct feedback appears");
console.assert(document.querySelector(".progress").getAttribute("aria-valuenow") === "1", "Progress counts answered questions");
document.getElementById("next").click();
console.assert(current === 1 && !answered && score === 1, "Next question preserves score");
document.getElementById("restart").click();
```

Also exercise a wrong answer, finish all ten questions, and replay. Check score-dependent results at 0, 4, 7, and 10 points. For visual changes, inspect narrow screens, light/dark appearance, and reduced motion.

## Architecture

`index.html` contains two independent browser-side systems:

- **Quiz:** `questions` is the ordered question bank. Each entry has `category`, `question`, four `answers`, a zero-based `correct` index, and `explanation`. The top-level `current`, `score`, and `answered` variables drive DOM updates through `renderQuestion`, `selectAnswer`, `updateProgress`, and `showResults`. `$` resolves elements by ID. State is in memory; reloading starts a new quiz.
- **Illustration:** `startPlanet` adds a transparent WebGL canvas over the hero SVG. A full-screen triangle pair invokes a fragment shader that analytically intersects rays with a planet sphere, annular ring plane, seven ellipsoidal rocks, and a moon sphere. Nearest-hit selection supplies occlusion; shading, surface detail, and shadows are procedural. This is not a mesh scene or a Three.js renderer.

Quiz replay resets quiz state only. The illustration has its own elapsed time, rendering loop, resize handling, and visibility/motion listeners.

## Quiz and presentation invariants

- `answered` prevents duplicate scoring. Selecting an answer locks every answer button, reveals the correct answer even after a wrong selection, displays the explanation, and enables Next. Advancing before answering is blocked in both the UI and click handler.
- Progress measures **answered questions**, not the current question index. Keep the progressbar's `aria-valuenow` and the fill width synchronized.
- The quiz currently has ten questions and four choices per question. Counts are also embedded in HTML, question labels, result output, and the perfect-score branch; changing the bank length requires updating those surfaces together.
- Results use four reaction tiers: exactly 10, at least 7, at least 4, and below 4. Replay restores question zero, zero score, zero progress, and the quiz/results visibility.
- Question and answer copy uses `textContent`; answer button `innerHTML` supplies the fixed letter/text/mark structure. Feedback combines a generated heading with a text node.
- UI copy is English. Theme colors come from `:root` custom properties and the dark-scheme override. Keep the centered 480px maximum column, responsive illustration, and 15px sans-serif body.
- Correct answers use green `.correct` styling and `pop`; wrong selections use red `.wrong` styling and `shake`. Feedback also identifies correctness with text and marks.
- Preserve the `hidden`-attribute visibility rule, live score/feedback announcements, progressbar semantics, and focus movement to Next, the new question heading, or the results heading.

## Rendering invariants

- The SVG remains the fallback and provides background stars. Add `.has-3d` only after WebGL initialization succeeds; it hides both `.static-planet` and the SVG `.orbit` satellite. Unsupported WebGL, shader initialization failure, or context loss must leave/restore the SVG and emit the existing `console.warn` diagnostic.
- All 3D objects share the fragment shader's camera and nearest-hit comparison. Put new orbiting objects into that comparison rather than overlaying independent 2D elements, so the planet and rings can hide them.
- Ring detail uses `+ time * 0.07` in its angular sampling; rocks use `- time * 0.07` in their orbital positions. Those opposite signs keep visible ring features and rocks moving together. Rock self-spin and the moon's orbit are separate motions.
- `ballHit` handles non-unit ray directions. Preserve its quadratic coefficient when intersecting ellipsoids: `rockLocal` transforms rays into the rock frame and divides by `rockScale`.
- Keep shader loops compatible with WebGL 1 GLSL: the rock and crater loops have constant integer bounds. Shader source is compiled only in the browser; exercise the WebGL path after editing GLSL.
- Canvas backing dimensions follow the illustration's CSS size, with device pixel ratio capped at 2. The shader's `resolution` uniform and GL viewport must match those backing dimensions.
- `requestAnimationFrame` advances elapsed seconds with a capped delta. Hidden documents pause rendering; resume clears the previous timestamp. Reduced motion renders a still frame and stops continuous scheduling; CSS animations also honor the same preference.
- Camera scale, ring tilt, and orbit radii interact with perspective and the fixed illustration bounds. Inspect multiple animation phases when changing them so rocks, rings, and the moon remain inside the canvas.
