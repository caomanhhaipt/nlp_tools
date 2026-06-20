# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Two standalone, single-file HTML annotation tools for NLP/speech data labeling. No build system, no dependencies, no server — open directly in a browser.

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

## Architecture

Both files follow the same pattern:
1. **CSS variables** at `:root` for the color palette
2. **State object** (plain JS object, serialized to `localStorage`)
3. **Mutation helpers** that modify state then call `render()`
4. **`render()`** is the single re-render entry point — it regenerates all DOM from state
5. **Event handlers** on the canvas/document for pointer interactions

There is no framework, no module system, and no external network requests. Everything runs offline.
