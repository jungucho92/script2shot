# Data Format

The benchmark covers **50 feature films** identified by their IMDb ID
(`ttXXXXXXX`). The authors' original annotations — **screenplay** (parsed),
**alignment**, and **linkage** — ship in this repository. **Posters and
keyframes** contain copyrighted media and are *not* redistributed; they are
obtained from MovieNet (see [`../data/README.md`](../data/README.md)).

```
data/
├── movie_ids.txt            # 50 IMDb IDs, one per line
├── screenplays/{imdb}.json  # parsed screenplay, scene-level (shipped)
├── alignments/{imdb}.json   # scene → shot alignment (shipped)
├── linkages/{imdb}.json     # subtitle → shot timing/metadata (shipped, text stripped)
└── posters/{imdb}.jpg        # movie poster (from MovieNet)
```

All indices are **0-based**. Shot IDs index MovieNet shots
(`0 .. total_shots-1`). Scene IDs index parsed screenplay scenes.

---

## 1. `alignments/{imdb}.json` — the benchmark labels

Human-verified mapping from each screenplay **scene** to the set of movie
**shots** that realize it. This is the ground truth used by every metric.

```json
{
  "alignments": {
    "0": [0, 1, 2, 3],
    "1": [4, 5, 6, 7, 8, 9],
    "9": [],
    "10": [31, 32, 33, 34, 35, 36, 37]
  }
}
```

- **Key** — scene ID (string-encoded integer), aligned to the screenplay's
  scene order.
- **Value** — list of shot IDs assigned to that scene. Order is not
  significant; treat as a set.
- An **empty list** (`"9": []`) marks a scene that has **no** corresponding
  shots in the film (e.g. a scene cut from the final edit). Roughly **15.6%**
  of scenes are unmatched; metrics skip scenes with empty ground truth.

Across the 50 films: **7,248 scenes**, **6,117 matched scenes**, and
**53,448 scene→shot annotation pairs**.

## 2. `linkages/{imdb}.json` — subtitle-to-shot timing

Per-film timing metadata linking subtitle cues to shots, plus the
shot-timeline calibration used to build the alignment.

```json
{
  "imdb_id": "tt0032138",
  "fps": 23.976061,
  "fps_method": "story_anchors",
  "fps_anchors": 16,
  "srt_offset": 1.8762,
  "total_shots": 672,
  "total_subtitles": 1486,
  "shots_with_subtitle": 547,
  "subtitle_coverage": 0.814,
  "ann_covered_shots": 421,
  "validation_accuracy": 0.9845,
  "validation_total": 905,
  "mappings": [
    { "shots": [3], "start_time": 126.167, "end_time": 128.001 }
  ]
}
```

Top-level fields:

| field | meaning |
|---|---|
| `fps`, `fps_method`, `fps_anchors` | estimated frame rate and how it was derived (used to convert subtitle timestamps to shot indices) |
| `srt_offset` | seconds added to subtitle timestamps to align with the shot timeline |
| `total_shots`, `total_subtitles` | counts for the film |
| `shots_with_subtitle`, `subtitle_coverage` | shots that carry dialogue and their fraction |
| `ann_covered_shots` | shots covered by the alignment annotation |
| `validation_accuracy`, `validation_total` | accuracy of the linkage on a manually checked validation set |
| `mappings` | per-subtitle-cue records |

Each `mappings` entry:

| field | meaning |
|---|---|
| `shots` | shot IDs the cue overlaps |
| `start_time`, `end_time` | cue timing in seconds (on the shot timeline) |
| `text` | **the verbatim subtitle string — removed from this release** (copyright). Obtain from MovieNet `subtitle/` `.srt`; see [`../data/README.md`](../data/README.md). |

> Subtitle/dialogue baselines pair each shot with its subtitle `text`; restoring
> the `text` field from MovieNet enables them. Vision-only and alignment metrics
> work from `shots`/timings alone.

## 3. `screenplays/{imdb}.json` — parsed screenplay *(shipped)*

A list of **scene** objects, in screenplay order. Index *i* in this list is
scene ID *i* used by the alignment. Produced from MovieNet scripts by the
screenplay parser (parser code coming soon — see the repo Roadmap).

```json
[
  {
    "tag":      ["S", "N", "N"],
    "text":     ["INT. DINER - DAY", "Enid sits alone.", "She stares out."],
    "range":    [[1, 1], [3, 5], [7, 8]],
    "dialogue": [],
    "character": []
  }
]
```

Each scene object holds parallel lists (`tag[k]` describes `text[k]`):

| `tag` | element type |
|---|---|
| `S` | scene heading / slugline (`INT. DINER - DAY`) |
| `N` | narrative / action description |
| `C` | character cue (speaker name) |
| `D` | dialogue line |
| `T` | transition (`CUT TO:`, `DISSOLVE TO:`) |
| `E` | parenthetical / sound cue (`(APPLAUSE)`) |
| `M` | other / unclassified token |

- `text[k]` — the element's text.
- `range[k]` — `[start_line, end_line]` of the element in the source script
  (1-based line numbers).
- `dialogue`, `character` — auxiliary parser fields (may be empty).

Per scene, dialogue is the joined `D` elements, narrative the joined `N`
elements, and the heading the first `S`. The number of elements per scene
(`L_j`) is determined by the parser; see [`pipeline.md`](./pipeline.md).

## 4. `posters/{imdb}.jpg` *(obtain from MovieNet)*

One movie poster per film, used only for dataset visualizations. Not
redistributed (copyright); fetch from MovieNet `meta/` poster URLs — see
[`../data/README.md`](../data/README.md).
