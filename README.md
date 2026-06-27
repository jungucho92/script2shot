# From Script to Shot: A Benchmark for Grounding Screenplays in Movies

[![License: CC BY-NC 4.0](https://img.shields.io/badge/License-CC%20BY--NC%204.0-lightgrey.svg)](./LICENSE)
[![Venue: ECCV 2026](https://img.shields.io/badge/ECCV-2026-blue.svg)](#citation)

Aligning **screenplay scenes** to **movie shots** is a foundational task for
narrative video understanding. Unlike subtitles or captions, screenplays
combine dialogue, visual direction, and narrative cues in a single document.
**From Script to Shot** is a benchmark of **50 feature films spanning nine
decades**, with **53K+ human-verified shot-level scene alignments**.

> **Key finding.** Adaptive multimodal fusion (BM25 + a visual encoder) wins
> overall, but a persistent gap between **dialogue** (`IoU_d`) and
> **non-dialogue** (`IoU_nd`) scenes exposes the limits of current visual
> encoders for narrative grounding.

> **This is the initial release — data + documentation.** The evaluation
> toolkit, baselines, and the data-preparation pipeline (screenplay parser +
> shot-subtitle mapping) are **coming soon**; see [Roadmap](#roadmap).

## Highlights

- **Benchmark:** 50 films · 7,248 scenes · 53,448 scene→shot pairs · 9 decades.
- **Annotations shipped here:** scene→shot **alignment** labels + subtitle→shot
  **linkage** timing/metadata.
- **Metrics:** `mSIoU` (primary), `BndF1`, dialogue/non-dialogue
  `IoU_d`/`IoU_nd`, retrieval `R@k` — see [`docs/pipeline.md`](./docs/pipeline.md).
- **Baselines (in the paper; code coming soon):** BM25/TF-IDF ·
  CLIP/SigLIP/InternVideo2 · FG-CLIP 2 · Qwen3-VL · TAN/MATR/T-MASS · adaptive
  fusion.

## Results (summary)

| Paradigm | Best method | mSIoU | IoU_d | IoU_nd |
|---|---|--:|--:|--:|
| Dialogue (text) | BM25 + Drop-DTW | 0.399 | 0.505 | 0.190 |
| Visual / temporal | T-MASS (fuse) | 0.483 | 0.556 | 0.340 |
| Fusion | BM25 + CLIP (α=0.5) | 0.531 | 0.597 | 0.403 |
| Fusion *(rebuttal)* | **BM25 + Qwen3-VL-Emb (α=0.7)** | **0.599** | — | — |

Full tables, FG-CLIP 2, and the 5-fold movie-level CV for α:
[`docs/leaderboard.md`](./docs/leaderboard.md).

## Repository layout

```
.
├── data/            # alignment + linkage annotations (shipped) + data guide
│   └── README.md    # MovieNet source, what ships vs. what to obtain
├── docs/            # data_format (schema) · pipeline/metrics · leaderboard · results/
├── DATASHEET.md     # datasheet for datasets
├── CITATION.cff · LICENSE
```

## Data access

This benchmark is derived from **[MovieNet](https://movienet.github.io/)**. To
respect copyright, it follows a **hybrid release**:

- **Shipped here (CC BY-NC 4.0):** the scene→shot **alignment** labels and
  subtitle→shot **linkage** timing/metadata — the actual annotation
  contribution (`data/alignments/`, `data/linkages/`).
- **Obtain from MovieNet:** verbatim screenplay text, subtitle text, posters,
  and movie frames are **not** redistributed. Get MovieNet under its own terms.

How the annotations relate to MovieNet, the source components, and the data
schema: [`data/README.md`](./data/README.md) and
[`docs/data_format.md`](./docs/data_format.md).

## Roadmap

Planned for upcoming releases:

- [ ] **Evaluation toolkit & baselines** — shared Drop-DTW pipeline + metrics,
      and the main/rebuttal baselines (text, contrastive, FG-CLIP 2, Qwen3-VL,
      fusion).
- [ ] **Data-preparation pipeline** — (1) screenplay parser → scene JSON,
      (2) shot-subtitle mapping → linkage JSON, plus helpers to regenerate
      screenplays/keyframes/posters from MovieNet.
- [ ] Parser source + clarification of how per-scene element count `L_j` is
      determined (reviewer request).

## License & citation

Released under **CC BY-NC 4.0** (see [`LICENSE`](./LICENSE)). The license covers
only the authors' original material; underlying MovieNet-derived content remains
under its own terms.

## Citation

```bibtex
@inproceedings{cho2026scripttoshot,
  title     = {From Script to Shot: A Benchmark for Grounding Screenplays in Movies},
  author    = {Cho, Jungu and Park, Young-Jae and Ha, Seong Jong and Kim, Siyeol and
               Park, Seungho and Shin, Jisu and Lee, Junmyeong and Hwang, Chaemin and
               Jung, Hyunjun and Lee, Sangeyl and Jeon, Hae-Gon},
  booktitle = {Proceedings of the European Conference on Computer Vision (ECCV)},
  year      = {2026}
}
```

Please also cite **MovieNet** (Huang et al., ECCV 2020), the source corpus.
