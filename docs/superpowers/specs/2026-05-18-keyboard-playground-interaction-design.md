# Keyboard Playground Interaction Redesign

Date: 2026-05-18
Project: kids-learning-tool

## Goal

Refactor the single-page kids learning tool into a playful "keyboard playground" experience. The app should be immediately understandable for young children: press a key, tap a big control, or click the surprise area and get a large visual, audio, and reward response.

The redesign prioritizes free exploration over structured lessons. Existing learning content for letters, numbers, pinyin, and quiz interactions remains, but the interface should feel like a toy first and a lesson second.

## Current Context

The project currently contains one file, `index.html`. It already implements:

- Free keyboard exploration for A-Z and 0-9.
- Chinese words, pinyin, English labels, number counting, and digit drawing.
- Pinyin input mode.
- Quiz mode.
- Three effect modes using particles and Web Audio.
- Basic mobile hidden-input support.

The main interaction issues are:

- Mode controls are small and placed at the bottom, so the available activities are not obvious.
- Free play, pinyin, quiz, and effects feel like separate modes rather than one coherent playground.
- Mobile input is hidden behind a small keyboard button.
- Rewards are momentary effects only; there is no lightweight sense of progress or discovery.
- Incorrect quiz answers trigger feedback, but the overall tone should avoid punishment.

## Recommended Direction

Use the "Playground Home + Light Rewards" approach.

The default screen becomes a keyboard playground with:

- A large central feedback stage.
- Big activity controls for letters, numbers, challenge, and sound effects.
- Lightweight reward indicators: stars, streak, and recent discoveries.
- A clear mobile keyboard/tap entry point.

The app should keep its existing playful dark background and colorful effects, but reorganize the interface so children can discover the interaction without reading instructions.

## Interaction Model

### Default Free Play

Free play remains the primary state. Pressing a letter or number immediately updates the central stage.

For letters:

- Show the large uppercase letter.
- Show the emoji.
- Show the Chinese word, pinyin, and English word.
- Play the selected effect and pronounce the letter.
- Add the item to recent discoveries.
- Increment the streak and stars.

For numbers:

- Show the digit, Chinese numeral, and digit stroke animation.
- Count using repeated emoji items.
- Play the selected effect and number narration.
- Add the item to recent discoveries.
- Increment the streak and stars.

Clicking or tapping the central stage should act as a "surprise" action, choosing a random letter or number. This keeps mouse and touch play intuitive.

### Activity Controls

Replace the small bottom mode bar with a prominent control dock.

Controls:

- Letters: random letter exploration.
- Numbers: random number exploration.
- Challenge: starts the existing quiz flow with friendlier framing.
- Sounds: cycles or opens effect choices.

On desktop, controls may live as a right-side dock when space allows. On smaller screens, they should move to a bottom dock with large tap targets.

### Challenge

The quiz mode becomes "小挑战" instead of a separate serious test.

Behavior:

- Show an emoji and Chinese word.
- Ask the child to press the matching first letter.
- Correct answers give stars, a cheerful animation, and quickly advance.
- Incorrect answers say "再试试" without resetting streak harshly.
- A "换一个" or surprise action should allow skipping.

### Pinyin Discovery

Pinyin mode should remain available, but not dominate the first screen.

Behavior:

- Keep the existing pinyin buffer and lookup logic.
- Present it as "拼一拼" or a secondary discovery activity.
- Keep Backspace support.
- When a syllable matches, show the character, emoji, and pronunciation.

### Sound Effects

Keep the three existing effect modes:

- Burst.
- Notes.
- Violin/bow effect.

Change the UI from text-heavy pills to more touch-friendly controls. The active effect should be obvious. The effect switch should not cover or compete with the learning content.

## Reward Model

Rewards are intentionally lightweight and local to the current page session.

State:

- `stars`: increases on exploration and correct challenges.
- `streak`: increases on repeated interactions and correct challenges.
- `recentDiscoveries`: a short list of recently seen letters/numbers/words.

Rules:

- Free exploration gives small positive feedback.
- Correct challenge answers give a larger reward.
- Incorrect challenge answers do not create a punitive reset.
- No account, persistence, leaderboard, or complex collection system in this redesign.

## Layout

Desktop layout:

- Header: app title, short friendly prompt, stars, and streak.
- Main stage: large visual feedback area.
- Control dock: large activity/effect buttons.
- Discovery strip: recent keys/items.

Mobile layout:

- Header stays compact.
- Main stage remains first and largest.
- Dock moves to the bottom with large controls.
- Mobile keyboard button becomes visible, large, and clearly labeled with an icon.
- Text must remain inside controls and avoid overlapping the stage.

## Component Boundaries

Because the app is a single `index.html`, implementation can stay in one file for now, but logic should be grouped clearly:

- Data: letter, number, pinyin, palette, and effect definitions.
- State: current activity, current effect, stars, streak, recent discoveries, quiz answer, speech generation.
- Rendering: functions for stage, controls, rewards, recent discoveries, pinyin result, and quiz.
- Input routing: keyboard, click/touch, mobile input, and control buttons.
- Effects: particles, notes, bow strokes, audio, and speech.

These boundaries are organizational. No new build tooling is required.

## Error Handling And Edge Cases

- Ignore unsupported keys.
- Avoid repeated keydown spam by preserving the existing `e.repeat` guard.
- Cancel speech before starting new speech, preserving the existing generation guard.
- If speech synthesis is unavailable, visual and audio effects should still work.
- If Web Audio cannot start until user gesture, fail quietly as it does now.
- On mobile, hidden input should keep working, but the visible trigger must be easier to discover.

## Testing And Verification

Manual verification is sufficient for this single-file app.

Verify in Chrome:

- Page loads with no console errors.
- Pressing letters updates the stage and reward state.
- Pressing numbers shows count items and digit animation.
- Activity controls are clickable.
- Challenge accepts correct and incorrect keyboard answers.
- Effect switching changes the animation/audio style.
- Mobile viewport keeps controls visible and text contained.
- Speech cancellation prevents old narration from continuing over new interactions.

Use screenshots or browser inspection for desktop and mobile viewport checks after implementation.

## Out Of Scope

- User accounts.
- Persistent progress storage.
- Leaderboards.
- Full curriculum sequencing.
- New framework or build system.
- Large data expansion beyond the existing learning set.
