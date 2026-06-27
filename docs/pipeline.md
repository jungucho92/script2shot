# Evaluation Pipeline & Metrics

Every baseline is evaluated on the **same** pipeline so that paradigms (sparse
text retrieval, contrastive encoders, temporal grounding, VLMs, fusion) are
compared fairly. Only the *similarity signal* changes; the alignment decoder
and metrics are shared.

```
screenplay scenes  ─┐
                    ├─► similarity matrix  S ∈ ℝ^{N_shots × M_scenes}
movie shots        ─┘            │
                                 ▼
                      Drop-DTW  (monotonic decode, + length prior)
                                 │
                                 ▼
                      shot → scene assignment
                                 │
                                 ▼
                   mSIoU · BndF1 · IoU_d / IoU_nd · R@k
```

This document describes the task, decode, and metrics. The reference
implementation (shared Drop-DTW decode + `compute_metrics`, with all baselines
on the same pipeline) is part of the evaluation toolkit — **coming soon** (see
the repo [Roadmap](../README.md#roadmap)).

## Similarity matrix

A method produces `sim[i, j]` = similarity between **shot `i`** and **scene `j`**.
The scene side is one of several text views (`prepare_scene_texts_*`): full text
(dialogue + narrative + heading) or visual-only text (narrative + heading), at
sentence or concatenated granularity. The shot side is subtitle text (for text
methods) or keyframe embeddings (for visual/VLM methods).

## Drop-DTW alignment decode

`drop_dtw(sim, sids, scids, dc)` finds a **monotonic** shot→scene assignment by
dynamic programming over `cost = -sim`, allowing shots to be *dropped* (not
assigned) at cost `dc`. Shots left unassigned are back-filled to the nearest
assigned scene. A Gaussian **length prior** (`build_length_prior`) biases the
diagonal using relative scene lengths. Monotonicity reflects that screenplay
order and shot order broadly agree.

## Metrics

Defined in `compute_alignment_metrics` / `compute_retrieval_metrics`.

### mSIoU — mean Scene IoU *(primary)*
For each scene with non-empty ground truth, IoU between its predicted shot set
and ground-truth shot set, averaged over scenes:

```
mSIoU = mean_j  |pred_j ∩ gt_j| / |pred_j ∪ gt_j|
```

### IoU_d / IoU_nd — dialogue vs. non-dialogue
The same scene-IoU restricted to scenes **with** dialogue (`IoU_d`) and
**without** dialogue (`IoU_nd`). The persistent **`IoU_d` − `IoU_nd` gap** is
the paper's central finding: current visual encoders under-ground non-dialogue
scenes.

### BndF1 — boundary F1
Treat the shot→scene labeling as a segmentation. A boundary is a position where
consecutive shots change scene. BndF1 is the F1 between predicted and
ground-truth boundaries with a **±1 shot** tolerance.

### R@1 / R@5 / R@10 / MedR
Standard shot-level retrieval of the correct scene, for reference.

## Cross-validation for fusion weight (α)

The adaptive fusion baseline combines BM25 and a visual encoder with a weight
α. To avoid tuning α on the test set, α is selected with **5-fold,
movie-level** cross-validation (added in the rebuttal). CV results:
[`leaderboard.md`](./leaderboard.md).

## Code

The evaluation toolkit and baseline scripts that implement this pipeline are
**coming soon** — see the repo [Roadmap](../README.md#roadmap). This initial
release ships the annotations and documentation only.
