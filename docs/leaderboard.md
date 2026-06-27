# Leaderboard

Aggregate results over all **50 films**. Primary metric: **mSIoU** (mean Scene
IoU). `IoU_d` / `IoU_nd` are mSIoU on dialogue / non-dialogue scenes — the gap
between them is the benchmark's central diagnostic. Higher is better except
MedR. Full numbers: [`results/main_results.csv`](./results/main_results.csv).

## Main results (by paradigm)

| Paradigm | Method | mSIoU | BndF1 | IoU_d | IoU_nd | R@1 | R@5 | MedR |
|---|---|--:|--:|--:|--:|--:|--:|--:|
| Dialogue (text) | BM25 + Drop-DTW + prior | 0.399 | 0.543 | 0.505 | 0.190 | 0.389 | 0.573 | 5.8 |
| Dialogue (text) | TF-IDF + Drop-DTW + prior | 0.396 | 0.546 | 0.507 | 0.175 | 0.346 | 0.576 | 5.7 |
| Dialogue (text) | Cour et al. 2008 (DTW) | 0.324 | 0.480 | 0.412 | 0.156 | 0.446 | 0.545 | 6.1 |
| Visual / temporal | T-MASS (fuse) | 0.483 | 0.598 | 0.556 | 0.340 | 0.383 | 0.537 | 6.4 |
| Visual / temporal | MATR (fuse) | 0.354 | 0.501 | 0.449 | 0.163 | 0.346 | 0.454 | 15.9 |
| Visual / temporal | S3D+w2v (fuse) | 0.330 | 0.490 | 0.420 | 0.156 | 0.325 | 0.435 | 17.8 |
| Visual / temporal | TAN (fuse) | 0.323 | 0.476 | 0.406 | 0.159 | 0.314 | 0.424 | 20.7 |
| Visual (contrastive) | CLIP | 0.252 | 0.434 | 0.253 | 0.252 | 0.144 | 0.375 | 11.9 |
| Visual (contrastive) | InternVideo2 | 0.222 | 0.429 | 0.227 | 0.205 | 0.128 | 0.306 | 19.5 |
| Visual (contrastive) | SigLIP | 0.023 | 0.189 | 0.020 | 0.035 | 0.048 | 0.167 | 30.6 |
| **Fusion** | **BM25 + CLIP (α=0.5)** | **0.531** | **0.633** | **0.597** | **0.403** | **0.415** | **0.594** | **4.5** |

> Code availability: the evaluation toolkit and baseline code (text,
> contrastive, FG-CLIP 2, Qwen3-VL, fusion) are **coming soon** (see the repo
> Roadmap). TAN / MATR / T-MASS are run from their official repositories; their
> numbers are reported above for completeness.

Takeaways: dialogue retrieval collapses on non-dialogue scenes (IoU_nd 0.19);
contrastive encoders alone trail text matching; adaptive fusion wins overall yet
the dialogue/non-dialogue gap persists.

## Fusion weight (α) selected by 5-fold movie-level CV

To avoid tuning α on the test set, α is chosen by 5-fold, movie-level
cross-validation. CV-test mSIoU equals the full-sweep value, confirming no
test-set overfitting. Source: [`results/cv_alpha_selection.csv`](./results/cv_alpha_selection.csv).

| Encoder + BM25 | best α | full-sweep mSIoU | CV-test mSIoU (mean ± std) |
|---|--:|--:|--:|
| CLIP + BM25 | 0.5 | 0.531 | 0.531 ± 0.071 |
| FG-CLIP 2 + BM25 | 0.5 | 0.527 | 0.527 ± 0.067 |
| **Qwen3-VL-Embedding + BM25** | 0.7 | **0.599** | **0.599 ± 0.040** |

Stronger encoders lift the benchmark measurably: Qwen3-VL-Embedding + BM25
reaches **mSIoU ≈ 0.59–0.60**, showing the task rewards better visual grounding.
(FG-CLIP 2 and Qwen3-VL results were added in the rebuttal.)

> Per-encoder aggregates: [`results/fusion_fgclip2_aggregate.csv`](./results/fusion_fgclip2_aggregate.csv),
> [`results/fusion_qwen3vl_aggregate.csv`](./results/fusion_qwen3vl_aggregate.csv).
> The cross-validation code is **coming soon** (see the repo Roadmap).
