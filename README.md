# NLP Annotation Tools

Four lightweight, self-contained browser-based tools for annotating NLP and speech datasets. No installation, no server, no dependencies — just open the HTML file in any modern browser.

---

## Tools

### `aste.html` — Aspect Sentiment Triplet Extraction (ASTE)

Annotate text sentences with structured sentiment triplets of the form **(target span, opinion span, sentiment)**.

![ASTE layout: sidebar sentence list · token canvas with arc arcs · right inspector panel]

#### Data Format

Each line in the input `.txt` file follows this structure:

```
token1 token2 ...#### #### ####[([target_indices], [opinion_indices], 'SENTIMENT')]
```

- Tokens are separated by spaces.
- The separator `#### #### ####` divides the sentence from its annotations.
- Token indices are **0-based** positions in the token list.
- Multiple triplets per sentence are comma-separated inside `[...]`.
- Sentiments: `POS`, `NEG`, `NEU`.

**Example:**
```
The battery life is great but the screen is terrible#### #### ####[([2, 3], [4], 'POS'), ([7], [9], 'NEG')]
```

Exported files include an extra tab-separated status column (`edited` / `confirmed`) so you can resume exactly where you left off.

#### How to Use

1. Click **📂 Import .txt** to load your annotation file.
2. Select a sentence from the left sidebar.
3. **Highlight** a range of tokens → a popup asks whether to label them as **Target** or **Opinion**.
4. **Drag** from a Target span to an Opinion span → choose the sentiment (POS / NEG / NEU) to create a relation arc.
5. **Click** any span or arc to inspect, change its role/sentiment, or delete it in the right panel.
6. Click **✔ Confirm & next** (or press `Enter`) to mark a sentence done and advance.
7. Click **💾 Export .txt** regularly to save your work.

#### Keyboard Shortcuts

| Key | Action |
|---|---|
| `←` / `→` | Previous / next sentence |
| `Enter` | Confirm current sentence and advance |

#### Auto-save

Work in progress is automatically saved to `localStorage` and can be restored on next open. Exporting clears the draft.

---

### `ner.html` — Named Entity Recognition (NER)

Annotate text sentences with entity spans, each assigned one of three fixed labels: **ADDRESS**, **PERSON**, or **ACCOUNT**. Works like the ASTE tool, minus the relation arcs.

#### Data Format

Each line in the input `.txt` file follows this structure:

```
token1 token2 ...#### #### ####[([token_indices], 'LABEL')]
```

- Tokens are separated by spaces; the separator `#### #### ####` divides the sentence from its annotations.
- Token indices are **0-based**; multiple entities per sentence are comma-separated inside `[...]`.
- Labels: `ADDRESS`, `PERSON`, `ACCOUNT`.

**Example:**
```
Nguyễn Văn An sống ở Hà Nội số tài khoản 0123456789#### #### ####[([0, 1, 2], 'PERSON'), ([5, 6], 'ADDRESS'), ([9], 'ACCOUNT')]
```

Exported files include an extra tab-separated status column (`edited` / `confirmed`) so you can resume where you left off.

#### How to Use

1. Click **📂 Import .txt** to load your annotation file.
2. Select a sentence from the left sidebar.
3. **Highlight** a range of tokens → a popup asks for the label (ADDRESS / PERSON / ACCOUNT).
4. **Click** any entity to change its label or delete it in the right panel.
5. Click **✔ Confirm & next** (or press `Enter`) to mark a sentence done and advance.
6. Click **💾 Export .txt** regularly to save your work.

Entities may not overlap — the tool rejects a selection that covers an existing entity.

#### Keyboard Shortcuts

| Key | Action |
|---|---|
| `←` / `→` | Previous / next sentence |
| `1` / `2` / `3` | Assign ADDRESS / PERSON / ACCOUNT to the current selection |
| `Esc` | Clear the current selection |
| `Enter` | Confirm current sentence and advance |

#### Auto-save

Work in progress is automatically saved to `localStorage` (key `ner_label_state_v1`, separate from the other tools) and can be restored on next open. Exporting clears the draft.

---

### `ser.html` — Speech Emotion Recognition (SER)

Label `.wav` audio files with one of three emotion classes: **negative**, **neutral**, or **positive**.

#### Data Format

The list file is a plain `.txt` with one entry per line. Labels are optional (useful for resuming):

```
audio_001.wav	negative
audio_002.wav
audio_003.wav	positive
```

Exported as `ser_output.txt` using the same format (tab-separated filename and label).

#### How to Use

1. Click **📁 Audio folder** and select the folder containing your `.wav` files.
2. Click **📄 List file (.txt)** and select your annotation list (can be a previously exported `ser_output.txt`).
3. If a browser draft exists for this folder, you will be asked whether to restore it.
4. The tool opens the first unlabeled file automatically.
5. Listen to the audio, then click **Negative / Neutral / Positive** (or press `1` / `2` / `3`).
6. Press **Enter** or click **💾 Save** to confirm and advance to the next file.
7. Use **⏭ Chưa làm** (or press `N`) to jump to the next file that still needs work — unlabeled, or in verify mode still awaiting a choice. It skips confirmed files and wraps around to the start.
8. Click **💾 Export ser_output.txt** to download the labeled file.

#### Keyboard Shortcuts

| Key | Action |
|---|---|
| `Space` | Play / pause |
| `R` | Replay from the beginning |
| `1` | Label: Negative |
| `2` | Label: Neutral |
| `3` | Label: Positive |
| `Enter` | Save current label and advance |
| `J` / `K` | Next / previous file |
| `N` | Jump to the next unfinished file (unlabeled / awaiting verify) |

#### Audio Features

- Waveform visualization rendered on a Canvas element.
- Click anywhere on the waveform to seek to that position.
- Adjustable playback speed (0.5× – 2×).
- Volume slider supports **up to 300%** amplification via Web Audio API `GainNode`.

#### Auto-save

Labels are continuously saved to `localStorage` (keyed by folder name). Exporting clears the draft. Files missing from the selected folder are skipped but preserved in the export output.

#### Verify Mode

Tick the **✅ Verify task** checkbox in the toolbar to switch to adjudication mode. Here the list file holds the labels from two earlier annotators, and the current annotator picks one of the two.

Input list file is **3 columns** (tab-separated):

```
audio_001.wav	positive	neutral
audio_002.wav	negative	neutral
audio_003.wav	positive	positive
```

- Only the two reference labels are offered as choices for each file — you select one of them.
- Rows where both annotators **agree** (e.g. `audio_003.wav`) are auto-confirmed with that label; no action needed.
- Export adds a **4th column** with the chosen label: `filename	user1	user2	final`.
- On reload, a row is treated as done when its 4th column has a value.
- The tool detects a column-count mismatch between the checkbox state and the file, and offers to switch to the correct mode.

---

### `sentiment.html` — Text Sentiment Annotation

Read text sentences one at a time and label each with one of three classes: **negative**, **neutral**, or **positive**. This is the text counterpart of `ser.html` — instead of listening to a `.wav`, you read the sentence shown on screen.

#### Data Format

The input file is a plain `.txt` with **one sentence per line**. Labels are optional (useful for resuming):

```
Sản phẩm này dùng rất tốt, tôi rất hài lòng.	positive
Giao hàng hơi chậm so với dự kiến.
Chất lượng bình thường, không có gì đặc biệt.	neutral
```

Columns are tab-separated, so sentences must not contain tab characters. Exported as `sentiment_output.txt` using the same format.

#### How to Use

1. Click **📄 List file (.txt)** and select your text file (can be a previously exported `sentiment_output.txt`).
2. If a browser draft exists for this file, you will be asked whether to restore it.
3. The tool opens the first unlabeled sentence automatically.
4. Read the sentence, then click **Negative / Neutral / Positive** (or press `1` / `2` / `3`).
5. Press **Enter** or click **💾 Save** to confirm and advance to the next sentence.
6. Click **💾 Export sentiment_output.txt** to download the labeled file.

#### Keyboard Shortcuts

| Key | Action |
|---|---|
| `1` | Label: Negative |
| `2` | Label: Neutral |
| `3` | Label: Positive |
| `Enter` | Save current label and advance |
| `J` / `K` | Next / previous sentence |

#### Auto-save

Labels are continuously saved to `localStorage` (keyed by file name). Exporting clears the draft.

#### Verify Mode

Tick the **✅ Verify task** checkbox to switch to adjudication mode. The input file is **3 columns** (tab-separated) — sentence, user-1 label, user-2 label — and you pick one of the two:

```
Sản phẩm này dùng rất tốt.	positive	neutral
Giao hàng quá chậm.	negative	neutral
Đóng gói đẹp, sẽ mua lại.	positive	positive
```

- Only the two reference labels are offered as choices for each sentence — you select one of them.
- Rows where both annotators **agree** are auto-confirmed with that label; no action needed.
- Export adds a **4th column** with the chosen label: `sentence	user1	user2	final`.
- On reload, a row is treated as done when its 4th column has a value.
- The tool detects a column-count mismatch between the checkbox state and the file, and offers to switch to the correct mode.

---

## Running

No setup required. Open any file directly in a browser:

```bash
open aste.html
open ser.html
open sentiment.html
```

Works fully offline. Tested in Chrome and Firefox.
