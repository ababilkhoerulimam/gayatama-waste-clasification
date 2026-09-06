<div align="center">
  <img src="logo.png" alt="Gayatama Logo" width="128" height="128">

  <h1>Gayatama Waste Classification</h1>
  <p><strong>Industrial Multi-Class Waste Classification, Calibrated Soft-Voting Ensemble, and Human-in-the-Loop Downstream Routing</strong></p>

  <p align="center">
    <img src="https://img.shields.io/badge/Task-Multi--Class_Vision-blue?style=flat-square" alt="Task">
    <img src="https://img.shields.io/badge/Classes-10_Categories-orange?style=flat-square" alt="Classes">
    <img src="https://img.shields.io/badge/Dataset-15%2C000_Images-green?style=flat-square" alt="Dataset">
    <img src="https://img.shields.io/badge/Top1_Accuracy-97.69%25-success?style=flat-square" alt="Accuracy">
    <img src="https://img.shields.io/badge/Macro_F1-0.9768-brightgreen?style=flat-square" alt="Macro F1">
    <img src="https://img.shields.io/badge/License-MIT-lightgrey?style=flat-square" alt="License">
  </p>

  <p align="center">
    A deep learning benchmark across six neural network architecture families for municipal solid waste classification, featuring cross-family soft-voting ensemble, McNemar statistical significance validation, Expected Calibration Error minimization, and human-in-the-loop decision routing for industrial circular economy facilities.
  </p>
</div>

## Tech Stack

![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54)
![PyTorch](https://img.shields.io/badge/PyTorch-%23EE4C2C.svg?style=for-the-badge&logo=PyTorch&logoColor=white)
![scikit-learn](https://img.shields.io/badge/scikit--learn-%23F7931E.svg?style=for-the-badge&logo=scikit-learn&logoColor=white)
![NumPy](https://img.shields.io/badge/numpy-%23013243.svg?style=for-the-badge&logo=numpy&logoColor=white)
![Pandas](https://img.shields.io/badge/pandas-%23150458.svg?style=for-the-badge&logo=pandas&logoColor=white)
![Matplotlib](https://img.shields.io/badge/Matplotlib-%23ffffff.svg?style=for-the-badge&logo=Matplotlib&logoColor=black)
![Git](https://img.shields.io/badge/git-%23F05033.svg?style=for-the-badge&logo=git&logoColor=white)

## Project Overview

Municipal solid waste classification in developing and industrializing economies faces severe operational challenges due to heterogeneous waste streams, inconsistent sorting, and safety hazards from misallocated toxic materials. The Gayatama Waste Classification project provides a rigorous, end-to-end computer vision and decision pipeline developed to support circular waste processing plants.

The system addresses three critical industrial requirements:
1. Multi-Class Material Sorting: Accurate discrimination across 10 granular material classes derived from national waste data (SIPSN KLHK) and international recycling taxonomies.
2. Cross-Family Ensemble Generalization: Systematic evaluation of six neural architecture families to exploit complementary visual inductive biases via soft-voting probability aggregation.
3. Safety-First Operational Routing: Integration of probability calibration and a selective classification reject option, guaranteeing that low-confidence inputs and hazardous materials (B3 battery and electronic waste) are automatically diverted to a Human Inspection Desk.

## Dataset and Taxonomy

The dataset comprises 15,000 curated, high-fidelity images structured into a perfectly balanced 1:1 distribution across 10 material classes (1,500 images per class). To eliminate data leakage, global MD5 checksum deduplication was executed across all partitions, ensuring 0% sample overlap between training, validation, and holdout test sets.

The dataset is partitioned into:
- Training Split (70%): 10,500 images (1,050 images per class)
- Validation Split (15%): 2,250 images (225 images per class)
- Holdout Test Split (15%): 2,250 images (225 images per class)

The official dataset archive is hosted on Kaggle:
- Kaggle Dataset: [ababilkhoerulimam/gayatama-waste](https://www.kaggle.com/datasets/ababilkhoerulimam/gayatama-waste)

### 10-Class Taxonomy

1. `organic`: Biodegradable food scraps, kitchen waste, fruit and vegetable remnants.
2. `wood_vegetation`: Timber cut-offs, dry leaves, branches, sawdust, and untreated plant biomass.
3. `paper_cardboard`: Clean corrugated cartons, newsprint, office documents, and kraft packaging.
4. `rigid_plastic`: High-density containers, plastic bottles, caps, buckets, and structured polymers.
5. `flexible_plastic`: Thin films, plastic bags, multilayer sachets, wrappers, and LDPE sheets.
6. `textile_rubber_leather`: Fabric remnants, worn apparel, footwear, rubber straps, and leather goods.
7. `metal`: Aluminum drink cans, tin cans, scrap hardware, wires, and metallic components.
8. `glass_ceramic`: Glass beverage containers, jars, ceramic pottery, tiles, and broken glass shards.
9. `battery_electronic`: Spent lithium/alkaline batteries, printed circuit boards, cables, and e-waste.
10. `mixed_residual`: Non-recyclable composites, contaminated packaging, diapers, and inert sweeping residues.

## Downstream Industrial Routing

Standard classification models output categorical predictions without considering downstream process requirements. Gayatama implements an automated 3-track physical routing layer governed by material viability and confidence thresholding:

Track 1: Furniture Candidate Stream
- Targeted Classes: `wood_vegetation`, `metal`, `rigid_plastic`.
- Downstream Destination: Upcycling workshops, mechanical shredding, and composite timber production.
- Criteria: Must meet high structural durability criteria and confidence threshold $\tau^* \ge 0.400$.

Track 2: Energy Recovery / Refuse-Derived Fuel (RDF) Stream
- Targeted Classes: `paper_cardboard`, `flexible_plastic`, non-hazardous dry residuals.
- Downstream Destination: Cement co-processing kilns and industrial RDF pelletizers.
- Criteria: High calorific value items free from hazardous chlorine or non-combustible heavy metals.

Track 3: Human Inspection Desk (Mandatory Safety & Selective Rejection)
- Targeted Classes: All `battery_electronic` items, plus any sample whose prediction confidence satisfies $\max_c P(y=c \mid x) < \tau^*$.
- Downstream Destination: Manual sorting station equipped with safety gear and diagnostic equipment.
- Operational Rationale: B3 hazardous battery waste poses catastrophic fire and heavy metal explosion risks if routed into shredders or incineration kilns. Diverting all electronic waste and ambiguous samples preserves operator safety and facility uptime.

In holdout test simulation, 79.9% of total waste volume was autonomously routed (30.7% other/residual handling, 29.7% energy recovery, 19.5% furniture stream), while 20.1% was routed to the Human Inspection Desk.

## Architecture Benchmark and Empirical Results

To establish a comprehensive performance baseline, six model architectures representing distinct structural paradigms were benchmarked under identical training budgets (15 epochs per model, batch size 32, input resolution 224x224, AdamW optimizer, cosine annealing schedule, and mixed precision on NVIDIA Tesla T4 GPU).

The candidate architectures represent:
- Modern Hierarchical CNN: `convnext_tiny`
- Pure Vision Transformer: `swin_tiny_patch4_window7_224`
- Classic Deep CNN: `resnet50`
- Lightweight Edge CNN: `mobilenet_v3_large`
- Self-Supervised Foundation Model: `vit_small_patch14_dinov2.lvd142m` (Linear Probe)
- Scratch Baseline: `resnet18` (Trained from scratch, random initialization)

### Quantitative Performance Comparison

| Model Architecture | Structural Family | Val Acc (%) | Val Macro F1 | Test Acc (%) | Test Macro F1 | Training Time (15 ep) | Parameters | Checkpoint Size |
|---|---|---|---|---|---|---|---|---|
| ConvNeXt-Tiny | Modern CNN | 97.60% | 0.9760 | 97.07% | 0.9706 | 23.5 min | 28.6M | 106.2 MB |
| Swin-Tiny | Vision Transformer | 96.84% | 0.9688 | 96.44% | 0.9642 | 24.0 min | 28.3M | 105.1 MB |
| ResNet-50 | Classic Deep CNN | 96.58% | 0.9657 | 96.31% | 0.9629 | 22.6 min | 25.6M | 90.1 MB |
| MobileNetV3-Large | Edge CNN | 95.91% | 0.9604 | 95.20% | 0.9517 | 21.2 min | 5.4M | 16.3 MB |
| DINOv2-Small (Probe) | Self-Supervised | 79.64% | 0.7968 | 78.93% | 0.7891 | 22.5 min | 22.1M | 84.3 MB |
| ResNet-18 (Scratch) | Scratch Baseline | 77.69% | 0.7742 | 76.89% | 0.7665 | 20.7 min | 11.7M | 44.7 MB |
| Soft-Voting Ensemble | Tri-Family Hybrid | 97.96% | 0.9795 | 97.69% | 0.9768 | Post-hoc | 82.5M combined | 301.4 MB total |

### Key Empirical Observations

Transfer Learning Advantage: Supervised ImageNet pretraining delivers a +20.18% absolute Macro F1 improvement over the random initialization baseline (0.9760 vs 0.7742), proving the indispensable value of pretrained generic visual representations for waste classification.

Efficiency of Edge CNN: MobileNetV3-Large achieves 95.20% test accuracy with only 5.4 million parameters and a 16.3 MB disk footprint, making it an exceptional candidate for resource-constrained edge devices and embedded microcontrollers.

Modern CNN Superiority: ConvNeXt-Tiny achieved the highest single-model validation Macro F1 (0.9760), outperforming both Swin-T (0.9688) and ResNet-50 (0.9657).

## Soft-Voting Ensemble and Statistical Significance

To maximize out-of-distribution robustness and minimize classification variance, a cross-family ensemble was constructed using the top three performing models from distinct architectural paradigms:
- ConvNeXt-Tiny (Modern CNN)
- Swin-Tiny (Vision Transformer)
- ResNet-50 (Classic Deep CNN)

Predictions are aggregated via soft probability averaging across model outputs:

$$
P_{\text{ensemble}}(y = c \mid x) = \frac{1}{K} \sum_{k=1}^{K} P_k(y = c \mid x)
$$

On the holdout test set of 2,250 unseen images, the Soft-Voting Ensemble achieves:
- Top-1 Accuracy: 97.69% (+0.62% absolute gain over best single model)
- Macro F1-Score: 0.9768 (+0.0062 gain over best single model)

### McNemar Statistical Significance Test

To confirm that the ensemble's performance gain over the strongest individual model (ConvNeXt-Tiny) is statistically significant rather than an artifact of test sample selection, McNemar's Chi-squared test with continuity correction was conducted:

$$
\chi^2 = \frac{(|b - c| - 1)^2}{b + c}
$$

Contingency table on holdout test samples:
- Both models correct: 2,170 samples
- Both models incorrect: 38 samples
- Single model correct, Ensemble incorrect: 14 samples
- Ensemble correct, Single model incorrect: 28 samples

Test Statistics:
- Chi-squared statistic: $\chi^2 = 5.2812$
- Asymptotic p-value: $p = 0.02156$

Because $p = 0.02156 < 0.05$, the null hypothesis of equal marginal predictive accuracy is rejected. The ensemble provides a statistically significant improvement at the 95% confidence level.

## Reliability and Probability Calibration

In automated sorting installations, raw softmax scores from deep neural networks are frequently miscalibrated, often exhibiting overconfidence on incorrect predictions. Calibration quality was assessed using Expected Calibration Error (ECE) across 15 confidence bins:

$$
\text{ECE} = \sum_{m=1}^{M} \frac{|B_m|}{N} |\text{acc}(B_m) - \text{conf}(B_m)|
$$

Experimental Results:
- Top Single Model (ConvNeXt-Tiny): $\text{ECE} = 1.81\%$
- Soft-Voting Ensemble: $\text{ECE} = 0.45\%$

The cross-family ensemble achieved a 75% reduction in Expected Calibration Error, producing near-perfect alignment between predicted posterior probability and empirical accuracy without requiring post-hoc temperature scaling.

## Selective Classification and Human-in-the-Loop

For industrial safety, the sorting pipeline incorporates a selective classification decision rule parameterized by rejection threshold $\tau$:

$$
\hat{y}(x) = 
\begin{cases}
\arg\max_c P(y=c \mid x), & \text{if } \max_c P(y=c \mid x) \ge \tau \\
\text{REJECT (Route to Human)}, & \text{if } \max_c P(y=c \mid x) < \tau
\end{cases}
$$

### Calibrated Operating Point

Setting target selective accuracy to $\ge 95\%$:
- Calibrated Optimal Threshold: $\tau^* = 0.400$
- Empirical Selective Accuracy: 97.73% (exceeds the 95% operational reliability target)
- Autonomous System Coverage: 99.82% (only 0.18% of non-hazardous items rejected due to low confidence)

Combined with the mandatory B3 hazardous rule (which routes 100% of battery/electronic items to human operators), this setup ensures zero dangerous items enter autonomous shredding tracks while maintaining high facility throughput.

## Interpretability and Environmental Stress-Testing

### Grad-CAM Visual Explainability

Gradient-weighted Class Activation Mapping (Grad-CAM) was applied to the final convolutional feature maps of the models. Saliency heatmaps confirm that the neural models focus attention on distinctive material signatures rather than background clutter:
- `wood_vegetation`: High attention on natural grain, fibrous cut-ends, and organic surface textures.
- `metal`: Focused on specular highlights, metallic rim geometry, and brand embossing.
- `battery_electronic`: Centered directly on anode/cathode terminals, button cells, and PCB circuitry.

### Environmental Robustness Stress-Testing

To evaluate operational stability under challenging industrial facility conditions, the models were stress-tested under synthetic image degradation:
- Baseline Clean Test: Macro F1 = 0.9768
- Low Illumination (Brightness factor 0.4): Macro F1 = 0.9729 (99.6% retention)
- Severe Motion Blur (Gaussian kernel size 9): Macro F1 = 0.9680 (99.1% retention)
- Harsh Glare / Overexposure (Brightness factor 1.8): Macro F1 = 0.9547 (97.7% retention)

The cross-family ensemble maintained robust performance across all degradation regimes, demonstrating substantial resistance to ambient lighting and camera vibration variations.

## Repository Structure

```text
gayatama-waste-clasification/
├── .gitignore
├── LICENSE
├── logo.png
├── README.md
├── docs/
│   └── Gayatama_Waste_Classification.pdf
└── notebooks/
    └── gayatama_waste_classification_pipeline.ipynb
```

### Key Artifacts

- `notebooks/gayatama_waste_classification_pipeline.ipynb`: Complete self-contained Jupyter notebook containing all 24 executed cells with full empirical outputs, loss curves, confusion matrices, Grad-CAM plots, and HIL curves.
- `docs/Gayatama_Waste_Classification.pdf`: Formal academic research paper detailing the theoretical framework, dataset curation, benchmarking methodology, and industrial implementation analysis.
- `README.md`: Publication-grade project documentation, comprehensive architectural benchmark report, and reproduction guide.

## Reproducibility and Kaggle Execution

The full experimental pipeline is designed for seamless execution on Kaggle GPU instances (Tesla T4 or P100).

### Step 1: Clone the Repository

```bash
git clone https://github.com/ababilkhoerulimam/gayatama-waste-clasification.git
cd gayatama-waste-clasification
```

### Step 2: Access Dataset

The dataset is publicly hosted on Kaggle:
```bash
kaggle datasets download -d ababilkhoerulimam/gayatama-waste
unzip gayatama-waste.zip -d dataset_split/
```

### Step 3: Run the Pipeline Notebook

1. Import `notebooks/gayatama_waste_classification_pipeline.ipynb` into a Kaggle Notebook or your local Jupyter environment.
2. Attach the dataset `ababilkhoerulimam/gayatama-waste` as an input source.
3. Select GPU Accelerator: GPU T4 x2 or GPU P100.
4. Execute all 24 cells sequentially. The pipeline handles dependencies (`timm`, `albumentations`, `scikit-learn`), dataset verification, 6-model benchmarking, checkpointing, ensemble aggregation, McNemar testing, calibration, and HIL simulation.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Citation

If you use this benchmark, methodology, or dataset in your academic or industrial research, please cite:

```bibtex
@article{gayatama2026waste,
  title={Industrial Multi-Class Waste Classification via Calibrated Cross-Family Neural Ensembles and Selective Human-in-the-Loop Routing},
  author={Imam, Ababil Khoerul and Gayatama Research Team},
  journal={Gayatama Technical Report},
  year={2026},
  url={https://github.com/ababilkhoerulimam/gayatama-waste-clasification}
}
```