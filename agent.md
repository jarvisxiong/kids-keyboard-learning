# Agent Notes

## Project Overview

This is a single-page kids learning tool. The app lives in `index.html` and runs as a static HTML page with plain CSS and JavaScript.

The current experience is a "keyboard playground" for young children:

- Press letters or numbers to show large visual feedback.
- Explore Chinese words, pinyin, English words, emojis, sounds, and particles.
- Use the big activity controls for letters, numbers, pinyin discovery, challenge, and sound effects.
- Track lightweight session rewards with stars, streak, and recent discoveries.

## Key Files

- `index.html`: Main app, styles, data, rendering, input routing, effects, audio, and speech.
- `docs/superpowers/specs/2026-05-18-keyboard-playground-interaction-design.md`: Interaction redesign spec.
- `docs/superpowers/plans/2026-05-18-keyboard-playground-interaction.md`: Implementation plan used for the redesign.

## Running Locally

Serve the project from the repo root:

```bash
python3 -m http.server 8081
```

Open:

```text
http://localhost:8081/
```

If port `8081` is busy, use another available port.

## Editing Guidelines

- Keep the app dependency-free unless there is a clear reason to add tooling.
- Prefer small, focused edits inside `index.html`.
- Preserve the child-friendly interaction style: large targets, immediate feedback, positive tone.
- Avoid punitive behavior in challenge mode.
- Keep mobile layout readable and tap-friendly.
- Do not remove existing learning data unless explicitly requested.

## Verification

Before handing off changes, run:

```bash
node -e "const fs=require('fs');const s=fs.readFileSync('index.html','utf8');const m=s.match(/<script>([\s\S]*)<\/script>/);new Function(m[1]);console.log('script syntax ok')"
```

Then verify in Chrome:

- Page loads without crashing.
- Letter keys update the stage and rewards.
- Number keys show counting and digit display.
- Activity buttons switch behavior.
- Pinyin discovery accepts typed pinyin such as `shan`.
- Challenge mode accepts correct and incorrect answers.
- Sound effect button cycles labels and effects.
- Narrow viewport keeps controls visible without text overlap.

## Notes

This directory is currently not a git repository, so commit and branch workflows may not be available unless the project is later initialized with git.
