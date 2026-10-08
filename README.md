# PlantSeg — Genuinely-Run Notebooks, Ordered to Match the Paper

This folder is a read-only reference copy. Every file here is a **copy** of a notebook that
was actually executed (on Kaggle) and produced the real results/figures/checkpoints cited in
the paper — not a hand-written template or a superseded draft. Originals are untouched in their
original locations; nothing was moved or deleted.

How each one was picked, where more than one candidate existed: I inspected every candidate
notebook's own saved `execution_count`/`outputs` fields (not just file size or folder name) to
tell a genuinely-executed copy from an unexecuted template, then cross-checked against the
project's own working notes for which copy is "authoritative" when two executed versions exist.

## 01_Stage1_Classification — classifier training (8 backbones, seed 42)
- `01_Session1_Train_Models1to4.ipynb` ← `Stage 1/notebooks/PlantSeg_S1_Session1_Train_Models1to4.ipynb`
- `02_Session2_Train_Models5to8.ipynb` ← `Stage 1/notebooks/PlantSeg_S1_Session2_Train_Models5to8.ipynb`
- `03_Session3_Aggregator.ipynb` ← `Stage 1/notebooks/PlantSeg_S1_Session3_Aggregator.ipynb`

**Caveat:** none of the saved copies of these three (nor their near-duplicates in
`Revision_1_Notebooks/`) carry embedded cell outputs — Kaggle notebooks are sometimes re-saved
"clean" after a run. I picked these three because they are the exact files the project's own
working notes cite as the source of the real checkpoints, and their checkpoints/result files
(`Stage 1/results(old)/plantseg_stage1/43seed_classifier_checkpoints/*.pt`, `result pkl/*.pkl`)
carry real, matching timestamps from the same training run. Several other near-duplicate
drafts exist in `Revision_1_Notebooks/` (e.g. `PlantSeg_Stage1_Classification_Benchmark.ipynb`,
`PlantSeg_S1_5_ConvNeXtV2Tiny*.ipynb`) — none of those have any executed cells either, and
they are not the ones the project notes point to, so they were left out of this folder.

## 02_Stage2_Segmentation — segmenter training (6 architectures, seed 42)
- `01_session1_trainers_1to3.ipynb` / `02_session2_trainers_4to6.ipynb` / `03_session3_aggregate.ipynb`
  ← `Stage 2/Notebooks/PlantSeg_S2_session{1,2,3}...ipynb`

Same caveat as Stage 1 — no embedded outputs in any copy, but these are the ones the project
notes cite and whose checkpoints (`Stage 2/checkpoints/*.pt`) match. `Revision_1_Notebooks/`
has several near-duplicates (plain, `_STAGE3` suffixed, a `PlantSeg_Stage2_Segmentation_
Benchmark.ipynb`) — all unexecuted, all left out.

## 03_Stage3_Severity_Validation
- `01_plantseg-stage-3-final-v2.ipynb` ← `Stage 3/plantseg-stage-3-final-v2.ipynb`

Three versions existed in `Stage 3/` (`plantseg-stage-3.ipynb`, `-final.ipynb`, `-final-v2.ipynb`),
all genuinely executed. `-final-v2` is the one actually used: it's the largest/most-recently-run,
and its save time comes after the `severity_vs_segmentation_quality.csv` it produced.

## 04_Stage4_XAI_Faithfulness
- `01_plantseg-stage-4-final-v2_main_run.ipynb` ← `stage 4/plantseg-stage-4-final-v2.ipynb` (main run, all faithfulness figures/CSVs)
- `02_fig21_23_regeneration_run.ipynb` ← `stage 4/plantseg-stage4-fig21-23-update.ipynb` (later rerun that regenerated just Figures 21 & 23 for the response letter)

`plantseg-stage-4-final.ipynb` (no `-v2`) is an older, fully-executed v1 superseded by `-v2` —
left out, since `-v2` is what the paper's numbers/figures actually come from.

## 05_Stage5_DecisionSupport_Demo
- `01_main_run_notebook32094e37db.ipynb` ← `stage 5/notebook32094e37db.ipynb` (the actual source of the deployed decision-support figure/batch validation)
- `02_fig24_regeneration_run.ipynb` ← `stage 5/stage5-fig24-ONLY.ipynb` (later rerun that regenerated just Figure 24 with the final colour-bar/legend annotations)

The root-level `PlantSeg_Stage5_Decision_Support_Demo.ipynb` and `stage 5/stage5-fig24-update.ipynb`
are earlier drafts/unexecuted or superseded — left out.

## 06_Stage6_Revision_Analyses_Round1
- `01_plantseg-stage-6-revision.ipynb` ← `Stage 6/plantseg-stage-6-revision.ipynb`

The `Revision_1_Notebooks/PlantSeg_Stage6_Revision_Analyses.ipynb` copy is an unexecuted
template of the same notebook — left out.

## 07_MultiSeed_Retraining — seeds 123 & 7, all 14 models
Segmenters (6 lanes, executed copies pulled from `Revision_1_Notebooks/Ran revision/`, which is
literally named for being the ones that were run):
- `01_segmenter_lane1_seed123.ipynb` ... `06_segmenter_lane6_seed7.ipynb`
  ← `multi-seed-retraining-lane-{1..6}.ipynb` (lane↔seed mapping: 1/3/5=seed123, 2/4/6=seed7)

Classifiers (2 lanes):
- `07_classifier_laneA_seed123.ipynb` ← `Revision_1_Notebooks/ClassifiersOnly/PlantSeg_ClassifiersOnly_LaneA_Seed123.ipynb`
- `08_classifier_laneB_seed7.ipynb` ← `Revision_1_Notebooks/ClassifiersOnly/PlantSeg_ClassifiersOnly_LaneB_Seed7.ipynb`

**Caveat:** the two classifier-lane notebooks are the only copies that exist locally (no
downloaded-with-outputs version was saved back from Kaggle) — their genuine execution is
evidenced by the real `results*.zip` files sitting alongside them in `classifier seed
123/`/`classifier seed 7/`, not by embedded cell outputs in the notebook itself.
`Revision_1_Notebooks/MultiSeed_Retraining/PlantSeg_MultiSeed_Lane*.ipynb` are my unexecuted
pre-fix/template copies — left out.

## 08_Round2_Kaggle_Diagnostics — Array round-2 review follow-up
- `01_NotebookA_single_seed_diagnostics.ipynb` ← `.../Round2_Kaggle_Notebooks/single-seed-diagnostics-seed-42.ipynb`
- `02_NotebookB_multi_seed_diagnostics.ipynb` ← `.../multi-seed-diagnostics-seeds-42-123-7.ipynb`
- `03_NotebookC_LibraryVersionRecovery_executed.ipynb` ← `.../Round2_Kaggle_Notebooks/Notebook C.ipynb`

The unexecuted generator templates (`PlantSeg_R2_NotebookA/B/C_*.ipynb`) were left out.

**Not included yet:** Notebook D (the corrected ViT/Swin seed-42 recovery, scoped rerun) —
it's still running on Kaggle as of this folder being built. Once you bring its results back,
say so and I'll add the executed copy here as `08_Round2_Kaggle_Diagnostics/04_NotebookD_...`.

---
*Generated automatically while organizing the project; originals are untouched in their source
folders — this is a copy-only reference set.*
