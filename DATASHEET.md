# Datasheet — From Script to Shot

Following *Datasheets for Datasets* (Gebru et al., 2021).

## Motivation
- **Purpose.** Provide a controlled benchmark for **grounding screenplay scenes
  to movie shots** — aligning structured narrative text (dialogue + visual
  direction + narrative cues) to long-form video. Existing video-text resources
  target short clips or subtitle overlap only; none isolate the contributions of
  dialogue vs. visual information to alignment quality.
- **Created by** the paper's authors (see `CITATION.cff`); presented at ECCV 2026.

## Composition
- **Instances.** 50 feature films, each with: parsed screenplay scenes,
  shot-level scene→shot alignment labels, and subtitle→shot linkage metadata.
- **Scale.** 7,248 scenes (6,117 matched; ~15.6% unmatched), 53,448 scene→shot
  annotation pairs (~55K), spanning nine decades.
- **Labels.** Human-verified shot-level scene alignments
  (`data/alignments/`). Linkage `validation_accuracy` records per-film
  subtitle-alignment accuracy on a manually checked validation set.
- **Splits.** No fixed train/test split — the benchmark is for **evaluation**.
  Hyperparameters (fusion α) are selected by 5-fold **movie-level**
  cross-validation, not on the test set.
- **Source.** Derived from **MovieNet** (Huang et al., ECCV 2020): scripts,
  subtitles, shot keyframes, and metadata.

## Collection & processing
- Screenplays parsed into tagged elements (S/N/C/D/T/E/M) with source line
  ranges. Subtitles linked to shots via estimated fps + offset calibration
  (`data/linkages/`). Scene→shot alignments produced and **verified by human
  annotators**.
- See `docs/data_format.md` and `docs/pipeline.md`.

## Distribution & licensing
- **License: CC BY-NC 4.0** for all original material (alignment + linkage
  annotations, toolkit, baselines, docs). See `LICENSE`.
- **Initial release = annotations + documentation.** This release ships the
  parsed screenplays, scene→shot alignment labels, subtitle→shot linkage
  metadata, and docs. The evaluation toolkit, baselines, and data-preparation
  pipeline (parser + shot-subtitle mapping) are **forthcoming** — see the repo
  Roadmap.
- **Hybrid release (copyright).** Subtitle text (stripped from linkages),
  posters, and movie frames are **not** redistributed. They are obtained from
  MovieNet under MovieNet's terms; see `data/README.md`.
- **Hosting.** GitHub.

## Uses
- **Intended.** Non-commercial research on long-form multimodal video
  understanding: screenplay-to-shot alignment, and downstream zero-shot scene
  segmentation and screenplay-guided video generation.
- **Out of scope / limitations.**
  - Requires an available screenplay for the target video (not applicable to
    arbitrary videos without scripts).
  - **Single source (MovieNet)** — screenplay styles and filming conventions may
    not generalize to other corpora.
  - ~15.6% of scenes are unmatched and excluded from scene-IoU metrics; reported
    scores reflect matched scenes.
  - Films skew toward English-language, Western cinema (MovieNet composition).

## Maintenance
- Maintained by the authors via the GitHub repository (issues / PRs).
- Errata and additional baselines may be added; the alignment/linkage schema is
  versioned through the repo history.
