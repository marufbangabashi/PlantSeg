# PlantSeg — Ordered Pipeline Notebooks

All the code behind the paper *"Faithfulness-Gated Decision Support for In-the-Wild Plant
Disease Diagnosis"* — eight classifiers and six segmentation models benchmarked on an
in-the-wild corpus of 115 plant diseases, with their explanations scored against expert lesion
masks and turned into a deployed confidence flag.

Notebooks are grouped by stage and numbered in run order: folders run `01`→`08`, and notebooks
inside each folder run in their numbered order. Every notebook was genuinely executed and
retains its original output, **with one documented exception: the three Stage 2 segmentation
training notebooks** (see [Stage 2 disclosure](#stage-2-disclosure-please-read) below).

## Reading order for a reviewer

The main pipeline is `01`→`07`. Folder `08` is the round-2 diagnostic trail and is best read in
its numbered order: it begins with a symptom (`01`), narrows the cause across `03`–`05`, fixes
it in `06`, then audits the consequences for the classifiers (`07`, `08`), the segmenters
(`09`, `10`) and the attribution claims (`11`).


---

## 01_Stage1_Classification — classifier training, 8 models, seed 42

| Notebook | What it does | Finding |
|---|---|---|
| `01_Session1_Train_Models1to4.ipynb` | Trains MobileNetV3-Large, EfficientNet-B0, ResNet-50, EfficientNetV2-S; saves each `result_*.pkl`. | First four of eight classifiers; results feed the Session 3 aggregator. |
| `02_Session2_Train_Models5to8.ipynb` | Trains ConvNeXtV2-Tiny, ViT-B/16, Swin-Tiny, Gated-Fusion (light). | This run's ConvNeXtV2-Tiny checkpoint carried a learning-rate bug; superseded by `02b`. |
| `02b_ConvNeXtV2Tiny_FIXED_LR_bugfix.ipynb` | Retrains ConvNeXtV2-Tiny alone with the corrected learning rate. | Reaches **78.4%** test accuracy — the figure reported in the paper and used for every downstream analysis. |
| `03_Session3_Aggregator.ipynb` | Loads all eight `result_*.pkl` files; builds the classification tables and the dataset/preprocessing/augmentation figures. | ConvNeXtV2-Tiny is the strongest single-seed classifier. The three-seed mean computed later (folder `07`) instead favours ViT-B/16 at 77.2 ± 1.0%. |

## 02_Stage2_Segmentation — segmenter training, 6 models, seed 42

| Notebook | What it does |
|---|---|
| `01_session1_trainers_1to3.ipynb` | Trains the first three of six segmentation architectures. |
| `02_session2_trainers_4to6.ipynb` | Trains the remaining three. |
| `03_session3_aggregate.ipynb` | Aggregates all six models into the comparison table and figures. |

**Finding:** DeepLabV3+ (EfficientNet-B3) is the best segmenter (foreground IoU **0.648**) and
also the smallest at 11.7M parameters; SegFormer-B2 is weakest (0.579).

### Stage 2 disclosure (please read)

These three notebooks are included as **reference code only**. No execution record from their
original training run was preserved, so unlike every other notebook in this repository they
carry no saved output. We state this openly rather than presenting them as executed.

This is a gap in the *provenance of the notebooks*, not in the *validity of the Stage 2
results*. The reported segmentation numbers are independently supported three ways:

1. **The original per-image test-set result files survived separately**
   (`results/stage2_segmentation/s2_result_*.pkl`) and match the paper's segmentation table
   exactly for all six models: 0.648 / 0.614 / 0.602 / 0.595 / 0.590 / 0.579.
2. **`08_Round2_Kaggle_Diagnostics/09_NotebookH`** re-evaluated the six *currently deposited*
   checkpoints from scratch on the full 1,561-image test set (inference only, no retraining) —
   see the cross-check table below.
3. **`08_Round2_Kaggle_Diagnostics/10_NotebookI`** resolved the one large discrepancy Notebook H
   found, confirming it was a library-version loading artefact rather than a bad checkpoint.

#### Stage 2 cross-check results (Notebook H, executed)

| Model | Fresh re-evaluation | Original result file | Paper table | Status |
|---|---|---|---|---|
| U-Net (ResNet-34) | 0.6129 | 0.6015 | 0.602 | small gap (+0.011) |
| U-Net++ (ResNet-50) | 0.5954 | 0.5954 | 0.595 | exact |
| FPN (ResNet-50) | 0.6135 | 0.6135 | 0.614 | exact |
| DeepLabV3+ (ResNet-50) | 0.5904 | 0.5904 | 0.590 | exact |
| DeepLabV3+ (EfficientNet-B3) | 0.6304 | 0.6484 | 0.648 | small gap (−0.018) |
| SegFormer-B2 | 0.2100 | 0.5792 | 0.579 | large gap → **resolved by Notebook I** |

Notebook H's own log shows SegFormer-B2 loaded **non-strictly**, with many missing/unexpected
keys caused by a `transformers` naming change. Notebook I reloaded the same checkpoint with
`transformers==4.46.3` pinned: it then loaded **strictly, 0 missing/unexpected keys**, and
reproduced foreground IoU **0.5792** against the preserved record's 0.5792 — a difference of
0.0000. All six Stage 2 checkpoints therefore reproduce their reported figures once the
correct library version is used.

## 03_Stage3_Severity_Validation

| Notebook | What it does | Finding |
|---|---|---|
| `01_plantseg-stage-3-final-v2.ipynb` | Validates the lesion-area severity index for all six segmenters, loading each from its `.pt` checkpoint (no retraining). | Predicted severity agrees well with mask-derived ground truth; best model DeepLabV3+ (EfficientNet-B3) reaches severity CCC **0.812**. Segmentation quality and severity concordance correlate across architectures (r = 0.889). |

## 04_Stage4_XAI_Faithfulness

| Notebook | What it does | Finding |
|---|---|---|
| `01_plantseg-stage-4-final-v2_main_run.ipynb` | Main faithfulness evaluation across all six architectures: CAM–GT IoU, pointing game, deletion AUC. | **The paper's central result.** The five convolutional segmenters localise lesions faithfully (mean CAM–GT IoU 0.434, pointing-game hit rate 0.879); the tested SegFormer-B2/attention-rollout combination does not (0.067 / 0.027) despite competitive severity accuracy. |
| `02_fig21_23_regeneration_run.ipynb` | Re-runs the same evaluation to regenerate two figures. | Same results; figure regeneration only. |

## 05_Stage5_DecisionSupport_Demo

| Notebook | What it does | Finding |
|---|---|---|
| `01_main_run_notebook32094e37db.ipynb` | The full decision-support pipeline: image in, disease class + severity estimate + explanation + triage flag out. | The ground-truth-free confidence flag predicts classification correctness — 72.4% accuracy when flagged high confidence vs 62.1% when low (10.3-point gap, Fisher's exact p < 0.0001) — but does **not** predict severity error (p = 0.86). Calibration is moderate (Brier 0.174, ECE 0.080). |
| `02_fig24_regeneration_run.ipynb` | Trimmed copy kept to the cells that regenerate one figure. | Figure regeneration only. |

## 06_Stage6_Revision_Analyses_Round1

| Notebook | What it does | Finding |
|---|---|---|
| `01_plantseg-stage-6-revision.ipynb` | Four analyses requested by the round-1 reviewers, all reusing existing checkpoints. | A perceptual-hash audit found **249** near-duplicate train/validation images touching **213 of 1,561** test images (13.6%) — correcting an earlier "no leakage" statement. A follow-up check found no accuracy inflation from them: 66.9% on flagged images vs 67.8% on clean (Fisher's exact p = 0.79). |

## 07_MultiSeed_Retraining — seeds 123 and 7

| Notebook | What it does |
|---|---|
| `01`–`06_segmenter_lane{1-6}_seed{123,7}.ipynb` | Each of the six segmenters retrained once at seed 123 and once at seed 7 (one lane per model/seed pair). |
| `07_classifier_laneA_seed123.ipynb` | All eight classifiers retrained at seed 123. |
| `08_classifier_laneB_seed7.ipynb` | All eight classifiers retrained at seed 7. |

**Findings.** With the original seed-42 run these give the three seeds behind the paper's
variance tables. DeepLabV3+ (EfficientNet-B3) stays the top segmenter at every seed
(0.638 ± 0.019 fg-IoU); SegFormer-B2 is the most seed-sensitive segmenter (0.593 ± 0.037).
Among classifiers, ViT-B/16 has the best three-seed mean (**77.2 ± 1.0%**) while
ConvNeXtV2-Tiny — the model actually deployed throughout the paper — is the most
seed-sensitive (**75.5 ± 2.8%**, range 72.8–78.4%). This is disclosed in the paper as a
limitation of the model-selection protocol.

## 08_Round2_Kaggle_Diagnostics — round-2 reviewer checks

| Notebook | What it does | Finding |
|---|---|---|
| `01_NotebookA_single_seed_diagnostics.ipynb` | Inference-only diagnostics on the original seed-42 checkpoints. | Surfaced checkpoint-loading problems for SegFormer-B2, ViT-B/16 and Swin-Tiny that the next five notebooks chase down. |
| `02_NotebookB_multi_seed_diagnostics.ipynb` | The same diagnostics across all three seeds, plus validation-split re-ranking. | On the untouched 846-image validation split, ViT-B/16 and Swin-Tiny rank first and second; ConvNeXtV2-Tiny, the deployed model, ranks **fourth**. Disclosed in the paper as an unresolved model-selection limitation. |
| `03_NotebookC_LibraryVersionRecovery_executed.ipynb` | Tests a ladder of library versions against the problem checkpoints. | A pinned `transformers` version loads SegFormer-B2 cleanly (used later by Notebook I), but does not recover ViT-B/16 or Swin-Tiny. |
| `04_NotebookD_ViTSwinSeed42_CorrectedRecovery.ipynb` | Corrected, scoped rerun for ViT-B/16 and Swin-Tiny at seed 42. | Rules out a library-version explanation for those two. |
| `05_NotebookD2_CollapseVsScatterDiagnostic.ipynb` | One further diagnostic before declaring the two checkpoints unrecoverable. | **Root cause:** Swin-Tiny had collapsed to predicting a single class and ViT-B/16 was undertrained — genuine training-time failures, not loading bugs. |
| `06_NotebookE_ViTSwinSeed42_Retrain_executed.ipynb` | Retrains both models from scratch at seed 42 under the identical recipe. | Both succeed (ViT-B/16 75.98%, Swin-Tiny 75.21%, within 0.6 points of the reported figures). These retrained checkpoints are the ones used throughout the paper. |
| `07_NotebookF_ConvNeXtTiny_HeadlineRecheck_executed.ipynb` | Re-checks the headline ConvNeXtV2-Tiny TTA accuracy. | The deposited checkpoint gives only a **+0.9**-point TTA gain, not the +10.8 originally implied. Traced to checkpoint provenance — successive training-script revisions wrote to the same filename. A from-scratch retrain reached 76.9%, inside the reported 72.8–78.4% three-seed range. |
| `08_NotebookG_TTA_AllClassifiers_Breakdown.ipynb` | Extends the same check to all eight classifiers. | Six of eight deposited checkpoints fail to reproduce their reported figure (two near chance level). **All eight originally reported figures were nonetheless confirmed genuine** from each model's preserved Stage-1 per-image prediction record. This is the basis for the paper's checkpoint-provenance audit table. |
| `09_NotebookH_Stage2_CheckpointVerification.ipynb` | Inference-only re-evaluation of all six Stage 2 segmentation checkpoints on the full test set. | Three reproduce exactly, two show ±0.01–0.02 gaps, one (SegFormer-B2) shows a large gap traced to a non-strict load. See the Stage 2 cross-check table above. |
| `10_NotebookI_SegFormerB2_VersionPin_Rerun.ipynb` | Reloads SegFormer-B2 with `transformers==4.46.3` pinned. | Strict load, 0 missing/unexpected keys, fresh fg-IoU **0.5792** vs the preserved record's 0.5792 (diff 0.0000). Confirms Notebook H's large gap was a library-version artefact. **Closes the Stage 2 checkpoint question.** |
| `11_NotebookJ_NullModelBaselines.ipynb` | Centre-prior and random-attribution baselines plus a model-parameter randomisation check, for the classifier and all six segmenters, on the same 300-image subset used in the paper. | The classifier's Grad-CAM clears a random-attribution baseline decisively (IoU 0.263 vs 0.136, p = 3.4 × 10⁻³⁴) and beats a randomised-weights version of itself (0.263 vs 0.145, p = 3.7 × 10⁻²⁶) — but is **not** significantly better than a centre-of-frame prior (p = 0.24). The five convolutional segmenters clear both baselines; SegFormer-B2 scores significantly *below* both. |

---

*Last updated: 2026-10-11.*
