# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Three standalone, single-file HTML annotation tools for NLP/speech data labeling. No build system, no dependencies, no server — open directly in a browser. `index.html` is a landing page linking to all three.

## Tools

### `aste.html` — ASTE (Aspect Sentiment Triplet Extraction) Annotation

Labels text sentences with (target span, opinion span, sentiment) triplets.

**Input/output format** (one sentence per line):
```
token1 token2 ... #### #### #### [([target_indices], [opinion_indices], 'SENTIMENT')]
```
- Separator is the literal string `#### #### ####`
- Token indices are 0-based positions after space-splitting
- Sentiments: `POS`, `NEG`, `NEU`
- Export appends a tab + status column (`edited` / `confirmed`)

**Interaction model:**
- Highlight tokens → assign as TARGET or OPINION span
- Drag from a TARGET span to an OPINION span → creates a relation arc with sentiment
- Click a span/arc → inspect/edit/delete in the right panel
- State autosaves to `localStorage` key `aste_label_state_v1`

### `ser.html` — SER (Speech Emotion Recognition) Annotation

Labels `.wav` audio files with emotion: `negative`, `neutral`, or `positive`.

**Input:** A folder of `.wav` files + a `.txt` list file (one filename per line, optionally tab-separated with an existing label).

**Output format** (`ser_output.txt`):
```
filename.wav\tnegative
filename.wav\tneutral
```

**Interaction model:**
- Select audio folder and list file separately (two picker buttons)
- Waveform rendered via Canvas + Web Audio API (`AudioContext.decodeAudioData`)
- Volume slider uses a `GainNode` graph to allow >100% amplification
- Draft autosaves to `localStorage` keyed by folder name (`ser_label::<folderName>`)
- Export clears the localStorage draft

### `sentiment.html` — Text Sentiment Annotation

The text counterpart of `ser.html`: read a sentence on screen and label it `negative`, `neutral`, or `positive`. No audio, no waveform.

**Input:** A single `.txt` file, one sentence per line (optionally tab-separated with an existing label). Verify mode takes 3 columns (sentence, user-1 label, user-2 label).

**Output format** (`sentiment_output.txt`):
```
sentence text\tnegative
sentence text\tneutral
```
Verify mode appends a 4th column with the adjudicator's chosen label.

**Interaction model:**
- Select one `.txt` list file (single picker button) — no folder needed since text is inline
- Sentence shown in a large read-only box; pick a label and Save (`Enter`), `1/2/3` to label, `J`/`K` to navigate
- Verify mode offers only the two reference labels; agreeing rows auto-confirm
- Draft autosaves to `localStorage` keyed by file name (`sentiment_label::<fileName>`, map keyed by sentence text)
- Export clears the localStorage draft

## Architecture

All files follow the same pattern:
1. **CSS variables** at `:root` for the color palette
2. **State object** (plain JS object, serialized to `localStorage`)
3. **Mutation helpers** that modify state then call `render()`
4. **`render()`** is the single re-render entry point — it regenerates all DOM from state
5. **Event handlers** on the canvas/document for pointer interactions

There is no framework, no module system, and no external network requests. Everything runs offline.
