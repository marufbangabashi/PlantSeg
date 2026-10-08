# PlantSeg — Genuinely-Run Notebooks



## 01_Stage1_Classification — classifier training (8 backbones, seed 42)
- `01_Session1_Train_Models1to4.ipynb`  
- `02_Session2_Train_Models5to8.ipynb`  
- `03_Session3_Aggregator.ipynb` 



## 02_Stage2_Segmentation — segmenter training (6 architectures, seed 42)
- `01_session1_trainers_1to3.ipynb` / `02_session2_trainers_4to6.ipynb` / `03_session3_aggregate.ipynb`
 



## 03_Stage3_Severity_Validation
- `01_plantseg-stage-3-final-v2.ipynb` 



## 04_Stage4_XAI_Faithfulness
- `01_plantseg-stage-4-final-v2_main_run.ipynb`  (main run, all faithfulness figures/CSVs)
- `02_fig21_23_regeneration_run.ipynb` (later rerun that regenerated just Figures 21 & 23 for the response letter)



## 05_Stage5_DecisionSupport_Demo
- `01_main_run_notebook32094e37db.ipynb`  (the actual source of the deployed decision-support figure/batch validation)
- `02_fig24_regeneration_run.ipynb`  (later rerun that regenerated just Figure 24 with the final colour-bar/legend annotations)



## 06_Stage6_Revision_Analyses_Round1
- `01_plantseg-stage-6-revision.ipynb` 


## 07_MultiSeed_Retraining — seeds 123 & 7, all 14 models

- `01_segmenter_lane1_seed123.ipynb` 
  ← `multi-seed-retraining-lane-{1..6}.ipynb` (lane↔seed mapping: 1/3/5=seed123, 2/4/6=seed7)

Classifiers (2 lanes):
- `07_classifier_laneA_seed123.ipynb` 
- `08_classifier_laneB_seed7.ipynb` 

**Caveat:** the two classifier-lane notebooks are the only copies that exist locally (no
downloaded-with-outputs version was saved back from Kaggle) — their genuine execution is
evidenced by the real `results*.zip` files sitting alongside them in `classifier seed
123/`/`classifier seed 7/`, not by embedded cell outputs in the notebook itself.
`Revision_1_Notebooks/MultiSeed_Retraining/PlantSeg_MultiSeed_Lane*.ipynb` are my unexecuted
pre-fix/template copies — left out.

## 08_Round2_Kaggle_Diagnostics — Array round-2 review follow-up
- `01_NotebookA_single_seed_diagnostics.ipynb` 
- `02_NotebookB_multi_seed_diagnostics.ipynb` 
- `03_NotebookC_LibraryVersionRecovery_executed.ipynb` 


