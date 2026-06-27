# Data: source, what ships here, and how to obtain the rest

This benchmark is **derived from [MovieNet](https://movienet.github.io/)**. We
annotated **50 feature films** on top of MovieNet's raw material. To respect the
copyright of screenplays, subtitles, posters, and movie frames, this repository
follows a **hybrid release**: our original annotations are shipped here; the
underlying copyrighted assets are obtained from MovieNet by each user.

The 50 films are listed in [`movie_ids.txt`](./movie_ids.txt) (IMDb IDs).

---

## 1. Source data — MovieNet

Obtain MovieNet from its official sources and agree to its terms of use:

- OpenDataLab: <https://opendatalab.com/OpenDataLab/MovieNet>
- Project site: <https://movienet.github.io/>

MovieNet provides, per movie, the raw material this benchmark builds on:

| MovieNet component | What it is | Used for |
|---|---|---|
| `240P_frames` | per-**shot** keyframes (240P), several frames per shot | the visual side of each shot |
| `subtitle` | subtitles in `.srt` (timecode + dialogue) | subtitle→shot linkage timing |
| `script` / `screenplay` | screenplay as text (scene headings, action, dialogue) | parsed into scene units |
| shot boundary detection | shot segmentation result (text) | shot indices / `total_shots` |
| `meta` | metadata JSON (poster URL, fps/frame_count, …) | fps calibration, posters |

## 2. What we add — annotations (shipped here, CC BY-NC 4.0)

From the source above we produced, for each of the 50 films, two artifacts that
**are included in this repository**:

| Asset | In this repo? | What it is |
|---|---|---|
| `alignments/{imdb}.json` | ✅ shipped | scene→shot **alignment** labels (human-verified) |
| `linkages/{imdb}.json` | ✅ shipped | subtitle→shot **linkage** timing/metadata |
| `screenplays/{imdb}.json` | ✅ shipped | **parsed screenplay**, scene-level tagged elements |

These are the benchmark's contribution. The subtitle **`text`** field inside
`linkages/` is removed (it is MovieNet/source-copyrighted); all timing, shot
indices, and calibration metadata remain. See
[`../docs/data_format.md`](../docs/data_format.md) for the full JSON schema.

## 3. What you obtain from MovieNet (not redistributed)

| Asset | In this repo? | How to get it |
|---|---|---|
| subtitle `text` in linkages | ❌ stripped | from MovieNet `subtitle/` (`.srt`) |
| posters | ❌ not shipped | from MovieNet `meta/` poster URLs |
| keyframes (shot frames) | ❌ not shipped | from MovieNet `240P_frames/` |

## How the annotations were prepared (pipeline overview)

Two preprocessing steps turn MovieNet raw material into the units used here:

1. **Screenplay parsing** — the raw screenplay text is parsed into **scene
   units**, each scene a list of tagged elements (heading / action / character /
   dialogue / …). Output: the scene-level `screenplays/{imdb}.json` that
   `alignments/` indexes into (**shipped here**).
2. **Shot-subtitle mapping** — `.srt` subtitle cues are aligned to shots using
   an estimated frame rate and offset, producing the shot-level
   `linkages/{imdb}.json` (which shot each cue falls in, with timing and
   calibration metadata).

> **Coming soon.** The preprocessing **scripts** (screenplay parser,
> shot-subtitle mapping, and helpers to rebuild keyframes/posters from MovieNet)
> are not part of this initial data release. They will be published in an
> upcoming release together with the evaluation toolkit — see the repo
> [Roadmap](../README.md#roadmap). The shipped `screenplays/`, `alignments/`,
> and `linkages/` are the benchmark contribution and do not depend on those
> scripts.

## Target layout (once you add MovieNet-derived assets)

```
data/
├── movie_ids.txt
├── screenplays/{imdb}.json    (shipped)
├── alignments/{imdb}.json     (shipped)
├── linkages/{imdb}.json       (shipped; subtitle text removed)
├── keyframes/{imdb}/*.jpg      (from MovieNet 240P_frames)
└── posters/{imdb}.jpg          (from MovieNet meta)
```
