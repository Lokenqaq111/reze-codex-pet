# Reze — Codex Digital Pet

A tiny **Codex digital pet** sprite mascot named **Reze**, with a full set of
hand-directed animation states. This repository contains the complete design
project — generation prompts, layout guides, raw frames, the packed
spritesheet, and QA artifacts — so the pet can be reused or the whole pipeline
reproduced.

> Reze is a fan-made mascot **inspired by** the character Reze from *Chainsaw
> Man* (© Tatsuki Fujimoto / Shueisha). It is reinterpreted as a small,
> pixel-art-adjacent chibi pet sprite — not manga key art, and with no gore or
> weapons. This is an unofficial, non-commercial fan design and is not
> affiliated with or endorsed by the original rights holders. See
> [Attribution](#attribution) below.

## Preview

The packed spritesheet lives at `final/spritesheet.webp` (and `.png`). A QA
contact sheet of every frame is at `qa/contact-sheet.png`, and short preview
clips for each state are in `qa/videos/`.

## Spritesheet spec

| Property      | Value                          |
|---------------|--------------------------------|
| Atlas size    | 1536 × 1872 px                 |
| Grid          | 8 columns × 9 rows             |
| Cell size     | 192 × 208 px                   |
| Format        | WebP / PNG, RGBA (transparent) |
| Chroma key    | `#FF00FF` (magenta, used during generation, keyed out in final) |

Each row is one animation state. Frames are packed left-to-right starting at
column 0; unused trailing cells in a row are transparent.

| Row | State           | Frames | Purpose                                        |
|-----|-----------------|--------|------------------------------------------------|
| 0   | `idle`          | 6      | Neutral breathing / blinking loop              |
| 1   | `running-right` | 8      | Rightward locomotion loop                      |
| 2   | `running-left`  | 8      | Leftward locomotion loop                       |
| 3   | `waving`        | 4      | Greeting gesture, raised wave and return       |
| 4   | `jumping`       | 5      | Anticipation, lift, peak, descent, settle      |
| 5   | `failed`        | 8      | Sad / failed / deflated reaction               |
| 6   | `waiting`       | 6      | Patient waiting loop with small motion         |
| 7   | `running`       | 6      | Generic in-place running loop                  |
| 8   | `review`        | 6      | Focused inspecting / review loop               |

## Using the pet

The ready-to-use package is in `package/reze/`:

```
package/reze/
├── pet.json          # id, displayName, description, spritesheet path
└── spritesheet.webp  # the packed atlas
```

`pet.json`:

```json
{
  "id": "reze",
  "displayName": "Reze",
  "description": "A tiny Codex pet inspired by Reze's gentle, mysterious bomb-girl look.",
  "spritesheetPath": "spritesheet.webp"
}
```

To animate a state, slice the atlas into 192 × 208 cells and play the frame
count listed in the table above for the corresponding row.

## Repository layout

```
reze-codex-pet/
├── pet_request.json        # full design spec: atlas, rows, style notes, chroma key
├── imagegen-jobs.json      # generation job graph (base + per-row strips) with provenance
├── prompts/
│   ├── base-pet.md         # prompt for the canonical base reference sprite
│   └── rows/*.md           # per-state row-strip generation prompts
├── references/
│   ├── canonical-base.png  # canonical identity reference
│   └── layout-guides/*.png # per-state frame-spacing guides (input only)
├── decoded/*.png           # decoded base + per-state generated strips
├── frames/
│   ├── frames-manifest.json
│   └── <state>/NN.png      # individual extracted frames, one folder per state
├── final/
│   ├── spritesheet.webp    # packed atlas (primary)
│   ├── spritesheet.png     # packed atlas (PNG)
│   └── validation.json     # per-cell validation report
├── qa/
│   ├── contact-sheet.png   # all frames on one sheet
│   ├── review.json         # per-frame QA metrics (bbox, pixel counts, etc.)
│   └── videos/*.mp4        # per-state preview clips
└── package/reze/           # ready-to-use distributable
```

## How it was made

1. **Spec** — `pet_request.json` defines the atlas geometry, the nine states
   and their frame counts, the style contract, and the magenta chroma key.
2. **Base sprite** — `prompts/base-pet.md` generates a single canonical
   full-body reference (`references/canonical-base.png`).
3. **Row strips** — each state is generated as a horizontal strip using its
   prompt in `prompts/rows/` plus a layout guide from
   `references/layout-guides/` for frame spacing.
4. **Frame extraction** — strips are keyed against `#FF00FF`, split into
   individual `192 × 208` frames under `frames/<state>/`, and recorded in
   `frames/frames-manifest.json`.
5. **Packing** — frames are packed into the 8 × 9 atlas at `final/`.
6. **QA** — `qa/` and `final/validation.json` capture per-frame and per-cell
   checks (frame counts, bounding boxes, transparency, chroma bleed).

`imagegen-jobs.json` records the full job graph and generation provenance.

## Attribution

This is a derivative fan design. The character **Reze** originates from
*Chainsaw Man* by **Tatsuki Fujimoto**, published by **Shueisha**. All rights
to the original character belong to the respective rights holders. This
project reinterprets the character as an original low-resolution pet sprite
for non-commercial, hobbyist use. If you are a rights holder and would like
this taken down, please open an issue.

The MIT license below applies to the **packaging, structure, prompts, and
pipeline metadata** in this repository. It does not, and cannot, grant rights
to the underlying *Chainsaw Man* character IP.

## License

[MIT](./LICENSE) © 2026 yuxiang
