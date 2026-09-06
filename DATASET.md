# Gayatama Waste Classification Dataset

The official dataset for this benchmark is hosted on Kaggle:

- Web URL: https://www.kaggle.com/datasets/ababilkhoerulimam/gayatama-waste
- Kaggle CLI: `kaggle datasets download -d ababilkhoerulimam/gayatama-waste`

## Dataset Specifications

- Total Sample Count: 15,000 digital images
- Class Distribution: Perfectly balanced 1:1 distribution across 10 material classes (1,500 images per class)
- Partitioning Protocol:
  - Training Set (70%): 10,500 images (1,050 per class)
  - Validation Set (15%): 2,250 images (225 per class)
  - Holdout Test Set (15%): 2,250 images (225 per class)
- Deduplication & Leakage: 100% zero data leakage across splits audited via global MD5 hash verification

## 10 Material Classes

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

## Download and Extraction

To download and extract the dataset into the expected local structure:

```bash
kaggle datasets download -d ababilkhoerulimam/gayatama-waste
unzip gayatama-waste.zip -d dataset_split/
```
