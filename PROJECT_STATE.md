# Project State: Gayatama Waste Classification

**Schema version:** 1.1
**Last updated:** 2026-09-06
**Status:** Completed
**Current phase:** Delivery and Repository Reorganization Completed
**Project type:** PREDICTIVE
**Decision owner:** Project Research Team
**Primary notebook:** [notebooks/gayatama_waste_classification_pipeline.ipynb](notebooks/gayatama_waste_classification_pipeline.ipynb)
**Last completed cell:** Cell 24
**Data snapshot:** 2026-09-06 (15,000 unique images, 1:1 balanced, 100% zero leakage, MD5 audited, hosted on Kaggle: ababilkhoerulimam/gayatama-waste)
**Code version:** 9d16893

## Objective and Success Criteria

**Problem or decision:** Develop and evaluate a proof-of-concept PyTorch computer vision model to classify waste images into 10 material classes, provide initial handling recommendations (furniture candidate, energy recovery, or other handling/residual), and route uncertain or hazardous items through a human-in-the-loop decision layer.

**Unit of analysis:** One digital image representing a single dominant waste object.

**Outcome or target:** Discrete multi-class label across 10 classes (`organic`, `wood_vegetation`, `paper_cardboard`, `rigid_plastic`, `flexible_plastic`, `textile_rubber_leather`, `metal`, `glass_ceramic`, `battery_electronic`, `mixed_residual`).

**Primary success metric:** Macro F1-score >= 0.85 on test set; Selective Accuracy >= 0.95 on auto-accepted predictions under calibrated confidence threshold tau.

**Constraints:** GPU compute budget limited to cloud/Kaggle environments; initial benchmark restricted to 15 epochs per candidate architecture; classification limited to single dominant object per image; no chemical or sensor features available.

## Data Inventory

| Source | Metadata / Grain | Time Coverage | Sensitivity | Exploration Status |
|---|---|---|---|---|
| `dataset_split/train/` | 10,500 images, 10 balanced classes (1,050 / class) | Curated multi-source & pure scrape | PUBLIC | 100% UNIQUE, ZERO LEAKAGE |
| `dataset_split/val/` | 2,250 images, 10 balanced classes (225 / class) | Curated multi-source & pure scrape | PUBLIC | 100% UNIQUE, ZERO LEAKAGE |
| `dataset_split/test/` | 2,250 images, 10 balanced classes (225 / class) | Curated multi-source & pure scrape | PUBLIC | 100% UNIQUE, ZERO LEAKAGE |

## Progress

| Stage | Status | Evidence / Cell | Notes |
|---|---|---|---|
| Planning | COMPLETED | `project.md` | Core taxonomy, 3 routing tracks, and human-in-the-loop logic defined |
| Data quality and preparation | COMPLETED | E-001, E-002, E-006 | 15,000 verified images curated, 1:1 balanced, 100% zero leakage |
| EDA or statistical analysis | COMPLETED | E-007, E-008 | Resolution distribution, aspect ratios, and visual ground truth verified |
| Feature engineering | NOT APPLICABLE | `project.md` | Deep learning vision models use direct pixel input with standard transforms |
| Modeling and validation | COMPLETED | E-012, E-013, E-014, E-015, E-016 | 6 architectures benchmarked; Top 3 ensemble achieves 97.68% Test Macro F1 |
| Explanation and error analysis | COMPLETED | E-017, E-018, E-019, E-020, E-021 | McNemar p=0.0216, ECE 0.45%, Grad-CAM, Stress-test, and 3-track HIL routing validated |
| Methodology design | COMPLETED | `project.md` | 3-way cross-family soft-voting ensemble with selective classification |
| Delivery and export QA | COMPLETED | `README.md`, `docs/Gayatama_Waste_Classification.pdf`, `notebooks/` | Repository reorganized, clean English documentation and publication-grade README |
| Operational monitoring | NOT APPLICABLE | `project.md` | Proof-of-concept research scope; industrial deployment out of scope |

## Assumptions Register

| ID | Assumption | Category | Confidence | Impact if Wrong | Validation Plan | Status |
|---|---|---|---|---|---|---|
| A-001 | A single dominant object in the image determines the correct waste class | DATA | HIGH | HIGH | Audit confusion patterns on multi-object background images | OPEN |
| A-002 | Public benchmark images generalize sufficiently to Indonesian waste handling context | DATA | MEDIUM | HIGH | Qualitative error analysis on local test samples | OPEN |
| A-003 | Softmax probability ranking correlates with true predictive accuracy for threshold calibration | MODEL | HIGH | HIGH | Reliability diagram and selective accuracy curve on validation split | OPEN |
| A-004 | 15 epochs per candidate model is sufficient to rank architecture family performance | MODEL | HIGH | MEDIUM | Monitor validation loss plateau curves across 15 epochs | OPEN |

## Decisions Log

### D-001: 10-Class Taxonomy Adapted from SIPSN

- **Chosen:** 10 discrete material classes (`organic`, `wood_vegetation`, `paper_cardboard`, `rigid_plastic`, `flexible_plastic`, `textile_rubber_leather`, `metal`, `glass_ceramic`, `battery_electronic`, `mixed_residual`).
- **Alternatives considered:** Standard 6-class Garbage Classification taxonomy; fine-grained polymer-level taxonomy.
- **Why:** Aligns with Indonesian national waste composition data (SIPSN KLHK) while isolating hazardous battery/electronic waste and separating rigid vs flexible plastics for recycling compatibility.
- **Evidence:** `project.md` section 7.
- **Revisit when:** Downstream facility explicitly mandates sub-polymer separation (e.g. PET vs HDPE).

### D-002: Perfectly Balanced 1:1 Split (1,500 Images per Class)

- **Chosen:** Fixed cap of 1,500 images per class, split 70% train (1,050), 15% val (225), 15% test (225).
- **Alternatives considered:** Natural imbalanced distribution preserving raw scrape ratios.
- **Why:** Prevents majority class bias in transfer learning feature representations and ensures equal statistical support per class during validation.
- **Evidence:** E-001.
- **Revisit when:** Moving to operational deployment where real-world prior class probabilities must be modeled.

### D-003: Strict MD5 Deduplication Across Splits

- **Chosen:** Hash verification across all images in incoming extra folders against existing split partitions before assignment.
- **Alternatives considered:** Filename-based deduplication only.
- **Why:** Scraped files often have different filenames despite sharing identical image byte content; hash verification eliminates data leakage between train and test/val.
- **Evidence:** E-002.
- **Revisit when:** NOT APPLICABLE.

### D-004: Cross-Family Soft-Voting Ensemble

- **Chosen:** Evaluate 6 diverse architecture families and construct an ensemble from the 3 best-performing models from distinct families via probability averaging.
- **Alternatives considered:** Single best model; homogenous ensemble of identical architecture backbones.
- **Why:** Combining diverse structural inductive biases (CNN, ViT, Self-Supervised) produces superior predictive calibration and robustness for selective classification.
- **Evidence:** `project.md` section 11.2.
- **Revisit when:** Edge deployment latency constraints preclude running multi-model inference.

### D-005: DataLoader Configuration and Heavy Augmentation

- **Chosen:** 224x224 image input size, batch size 32, num_workers 2/4, Heavy Augmentation on training set (RandomResizedCrop scale 0.8-1.0, RandomHorizontalFlip, RandomRotation ±15°, ColorJitter b=0.2, c=0.2, s=0.2, h=0.05, ImageNet normalization).
- **Alternatives considered:** Batch size 64; 256x256 resolution; light standard augmentation without rotation.
- **Why:** 224x224 with batch size 32 ensures stable memory allocation on Kaggle Tesla T4 (14.5 GB) across all 6 architectures; Heavy augmentation provides rotational and illumination invariance needed for realistic waste photography.
- **Evidence:** User decision recorded in interactive session.
- **Revisit when:** Training throughput bottlenecks occur on larger ViT backbones.

### D-006: Manual Human Review for Web-Scraped Supplement Data

- **Chosen:** Store newly scraped images for `organic` and `battery_electronic` in `scraped_review/` for human manual noise filtering prior to dataset integration.
- **Alternatives considered:** Direct automatic ingestion into `dataset_split/`.
- **Why:** Web image scraping contains search noise (diagrams, text banners, unrelated electronics, composite items) that degrade label purity and model trustworthiness.
- **Evidence:** User explicit request during Interactive Navigator mode.
- **Revisit when:** Automated vision-language pre-filtering model (e.g. CLIP zero-shot confidence gate) is introduced.

### D-007: Pure ML and Benchmarking Pipeline Scope

- **Chosen:** Restrict notebook scope strictly to core Machine Learning, 6-family benchmarking, ensemble, statistical evaluation, Grad-CAM interpretability, calibration, and Human-in-the-Loop simulation (excluding Web UI / Gradio deployment layers).
- **Alternatives considered:** Adding Gradio/Streamlit UI cells into the experimental training notebook.
- **Why:** Maintains rigorous focus on empirical machine learning, reproducible benchmarks, and clean research code without UI overhead.
- **Evidence:** User explicit direction in Interactive Navigator mode.
- **Revisit when:** Moving to downstream operational demonstration phase.

### D-008: Architecture-Specific Hyperparameter Profiles

- **Chosen:** Employ tailored learning rates and weight decays per architecture family (e.g. 1e-3 for ResNet-50, 1e-4 for ConvNeXt/Swin with cosine schedule, and frozen backbone linear probing for DINOv2) to ensure fair convergence within the 15-epoch budget.
- **Alternatives considered:** Single uniform learning rate across all architectures.
- **Why:** Vision Transformers and modern ConvNets destabilize or under-converge under classic CNN learning rates; DINOv2 self-supervised features are best evaluated via linear probing / low-LR head tuning.
- **Evidence:** Reviewer methodology feedback.
- **Revisit when:** Full hyperparameter sweep (Optuna/Ray) is computationally feasible.

### D-009: Statistical Significance and Computational Cost Auditing

- **Chosen:** Integrate McNemar's Test for statistical significance between Top Single vs Ensemble and Scratch vs Transfer Learning, alongside a unified Computational Cost Table (Parameters, FLOPs, Latency ms/img, Checkpoint Size MB).
- **Alternatives considered:** Comparing raw percentage accuracy differences only.
- **Why:** Elevates empirical rigor for peer review, proving whether performance gains are statistically significant rather than test-split variance.
- **Evidence:** Reviewer methodology feedback.
- **Revisit when:** NOT APPLICABLE.

## Evidence Ledger

| ID | Claim or Result | Value | Evidence Source | Evaluation Context | Status |
|---|---|---|---|---|---|
| E-001 | Dataset split completed with 1:1 class balance | Exactly 1,500 images per class (1,050 train, 225 val, 225 test), total 15,000 images | `dataset_split/` directory inspection | DESCRIPTIVE | VALIDATED |
| E-002 | Deduplication prunes identical content between extra data and split | 27 mixed_residual and 12 wood_vegetation duplicates pruned | Python MD5 hash verification | DESCRIPTIVE | VALIDATED |
| E-003 | Kaggle GPU environment and dataset path verified | Tesla T4 (14.56 GB VRAM), 15,000 images across 3 splits confirmed active | Kaggle Cell 1 runtime output | DESCRIPTIVE | VALIDATED |
| E-004 | Cross-split duplicate audit flags 553 duplicate images across splits | 273 battery, 180 organic, 99 glass overlaps diagnosed to multi-source double import | Kaggle Cell 2 audit script | DESCRIPTIVE | VALIDATED |
| E-005 | Pure battery scrape audited with MD5 hash | 643 unique valid images, 0 corrupted, 0 internal duplicates, 0 overlap with source dataset | MD5 hash verification script | DESCRIPTIVE | VALIDATED |
| E-006 | Clean 15,000-image dataset split constructed with zero data leakage | Exactly 15,000 unique global MD5 hashes across 10 classes, 0 train-val-test overlap | Python full dataset MD5 audit | DESCRIPTIVE | VALIDATED |
| E-007 | Image resolution and aspect ratio profile audited across 1,000 samples | Median width=400, median height=384, aspect ratio median=1.00 (50.8% square, 35.5% landscape, 13.7% portrait) | Kaggle Cell 3 output | DESCRIPTIVE | VALIDATED |
| E-008 | Visual ground truth alignment verified across all 10 waste classes | 2x5 inspection grid confirms high visual fidelity and representative category objects | Kaggle Cell 4 plot inspection | DESCRIPTIVE | VALIDATED |
| E-009 | DataLoaders initialized under 224x224 and batch size 32 | 329 train, 71 val, 71 test batches per epoch verified | Kaggle Cell 5 output | DESCRIPTIVE | VALIDATED |
| E-010 | Batch tensor sanity check passed | Tensor [32, 3, 224, 224], zero NaNs/Infs, valid pixel stats, clean 10-class mapping verified | Kaggle Cell 6 output | DESCRIPTIVE | VALIDATED |
| E-011 | Metrics engine and CheckpointManager initialized | Standardized evaluate_model() and Macro-F1 checkpoint saver ready | Kaggle Cell 7 output | DESCRIPTIVE | VALIDATED |
| E-012 | Model factory supporting 6 architecture families verified | ResNet-50 test build confirmed with 23.53M params and tailored configs | Kaggle Cell 8 output | DESCRIPTIVE | VALIDATED |
| E-013 | Universal training engine with AMP, AdamW, and CosineAnnealingLR initialized | train_model() verified with per-epoch validation and Macro-F1 checkpointing | Kaggle Cell 9 output | DESCRIPTIVE | VALIDATED |
| E-014 | 6-Model Benchmarking executed over 15 epochs per architecture | ConvNeXt-Tiny ranks #1 (Val Macro F1 0.9760), Swin-T 0.9688, ResNet-50 0.9657, MobileNetV3 0.9604, DINOv2 0.7968, Scratch 0.7742 | Kaggle Cell 16 output & benchmark_6_architectures.csv | DESCRIPTIVE | VALIDATED |
| E-015 | Top 3 Cross-Family Ensemble constructed via Soft-Voting | convnext_tiny + swin_tiny + resnet50 achieves 97.96% Val Acc and 0.9795 Val Macro F1 | Kaggle Cell 17 & 18 outputs | CAUSAL | VALIDATED |
| E-016 | Holdout test set final evaluation on 2,250 unseen images | Soft-Voting Ensemble reaches 97.69% Test Acc and 0.9768 Macro F1 (+0.0062 gain vs 97.07% top single) | Kaggle Cell 19 output | CAUSAL | VALIDATED |
| E-017 | McNemar statistical significance test validates ensemble gain | Chi2 = 5.2812, p-value = 0.02156 < 0.05 (statistically significant) | Kaggle Cell 19 output | INFERENTIAL | VALIDATED |
| E-018 | Confidence calibration dramatically enhanced by ensemble | ECE drops from 1.81% (Single) to 0.45% (Ensemble), a 75% error reduction | Kaggle Cell 21 output & calibration_reliability_diagrams.png | INFERENTIAL | VALIDATED |
| E-019 | Environmental robustness stress-test under blur and lighting variations | Ensemble maintains superior F1 retention across Low Light (0.9729) and Harsh Glare (0.9547) | Kaggle Cell 22 output & robustness_stress_test.csv | DESCRIPTIVE | VALIDATED |
| E-020 | Human-in-the-Loop selective classification trade-off calibrated | Optimal tau* = 0.400 achieves 97.73% Selective Acc (exceeds >=95% goal) at 99.82% autonomous coverage | Kaggle Cell 23 output & hil_simulation_metrics.csv | CAUSAL | VALIDATED |
| E-021 | 3-Track downstream waste routing with mandatory safety rule | 79.9% Auto-Accepted (30.7% other, 29.7% energy, 19.5% furniture) and 20.1% routed to Human Inspection Desk | Kaggle Cell 24 output & downstream_routing_details.csv | CAUSAL | VALIDATED |
| E-022 | End-to-end notebook context and lineage documented | 24 code cells mapped and documented with cell lineage and contracts | docs/notebook_context.md via document-notebook | DESCRIPTIVE | VALIDATED |

## Methodology Design

**Method ID and version:** M-001 v0.1

**Maturity:** PROPOSAL

**Method spec:** `project.md` sections 8, 9, 11

**Case signature and baseline gap:** Waste image classification requires safety-critical routing where misclassifying hazardous waste (`battery_electronic`) or ambiguous composites (`mixed_residual`) into energy recovery or furniture causes severe equipment and safety hazards. Standard single-model uncalibrated predictions lack reliable rejection mechanisms.

**Exact next experiment:** Upload clean dataset to Kaggle or update remote storage, then proceed with exploratory data audit on image resolutions and training pipeline.

## Model Drivers and Error Analysis

Not applicable at current stage. Will be computed following model training and validation inference.

## Artifacts

| Artifact | Purpose | Validation Status |
|---|---|---|
| `notebooks/gayatama_waste_classification_pipeline.ipynb` | Executed end-to-end 24-cell Kaggle experiment notebook with academic English annotations and empirical plots | PASSED |
| `docs/Gayatama_Waste_Classification.pdf` | Official research paper and technical report | PASSED |
| `project.md` | Detailed architectural, methodology, and project roadmap specification | PASSED |
| `README.md` | Publication-grade English project documentation with empirical benchmark results | PASSED |
| `logo.png` | Project visual branding identity | PASSED |

## Operational Monitoring

**Status:** NOT APPLICABLE

**Owner and cadence:** NOT APPLICABLE

**Signals and intervention thresholds:** NOT APPLICABLE

**Fallback or rollback:** NOT APPLICABLE

## Limitations and Risks

- Visual classification cannot detect internal chemical properties (chlorine content, moisture level, calorific value).
- Public scraped images may exhibit background shortcuts or domain shift relative to actual sorting facilities.
- Single dominant object constraint ignores entangled or heavily cluttered conveyor belt waste streams.

## Open Questions

None. All modeling, validation, statistical tests, explainability, calibration, and documentation tasks have been completed.

## Exact Next Action

Repository reorganization and hygiene complete. Commit changes to Git and push to remote repository.
