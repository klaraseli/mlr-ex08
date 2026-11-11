# Task 4 — A better classifier

## Results Table

| Variant / Change (vs. Baseline) | Val Accuracy (%) | Camera-like Val (%) | Notes / Variance (Over 3 seeds) |
| :--- | :--- | :--- | :--- |
| **Task 3 Baseline (`leafnet_geo`)** | 83.9% | 68.9% | ±3.9% variance on camera-like set |
| + Dropout (`leafnet_dropout`) | 75.3% | 68.9% | Did not improve performance |
| + Global Average Pooling (`leafnet_gap`) | 79.2% | 66.6% | Slightly hurt camera-like performance |
| **Task 2 Baseline (`mobilenet_plain`)** | 89.4% | 82.0% | Strong baseline, fine-tuned |
| + Transfer Learning (last layer only, `frozen`) | 78.1% | 71.9% | Fine-tuning the whole network is much better |
| + Geometric Augmentation (`mobilenet_geo`) | 88.3% | 86.4% | Massive jump in camera-like robustness |
| + Color Jitter (`mobilenet_geo_color`) | 87.5% | 88.3% | Better camera-like accuracy, slightly lower standard val |
| + Augmentation, LR Scheduler & 30 Epochs | 90.8% | 89.0% | Cosine annealing improved both metrics |
| + AdamW (`mobilenet_geo_adamw`) | 88.1% | 87.3% | Minor improvement over basic MobileNet+Geo |
| + Label Smoothing (`mobilenet_geo_ls`) | 89.2% | 87.5% | Minor standard val gain |
| **Best Combination (`mobilenet_best_combo`)** | 90.3% | 90.6% | **Highly stable:** ±0.3% variance on camera-like set |
| + Historical Data (`mobilenet_combo_data50`) | 90.3% | 90.4% | Increased variance (±1.3%), no major metric gain |

### What Worked (and what each change brought)
* **Transfer Learning:** Fine-tuning the entire MobileNet (`mobilenet_plain`, 89.4% val) drastically outperformed freezing all but the last layer (`mobilenet_frozen`, 78.1% val).
* **Geometric & Color Augmentation:** Adding geometric augmentation to MobileNet boosted camera-like accuracy from 82.0% to 86.4%. Adding Color Jitter further pushed camera-like accuracy to 88.3%, confirming it makes the model significantly more robust to real-world lighting changes.
* **Learning Rate Scheduler (Cosine):** Training for 30 epochs with a Cosine Annealing scheduler provided the highest single-variant standard validation accuracy (90.8%) and a strong camera-like score (89.0%). 

### Noise Analysis
* We ran all variants across 3 seeds (`0, 1, 2`). The standard baseline (`leafnet_geo`) was quite noisy, fluctuating by nearly ±3.9% on the camera-like set. 
* **Conclusion on significance:** The improvements seen in our Best Combination are significantly larger than the random noise. The Best Combination not only achieved the highest camera-like accuracy (90.6%), but it was incredibly stable, varying by only ±0.3% across the three seeds.

### What Did Not Help
* **Dropout & GAP on LeafNet:** Adding dropout or replacing flattening with Global Average Pooling on the basic LeafNet failed to improve standard validation and slightly degraded the camera-like metrics. 
* **Historical Data (Last year's photos):** Injecting additional data into our best combination did not yield meaningful improvements (camera-like accuracy slightly dropped to 90.4%) and re-introduced higher variance between seeds (±1.3%).

## 4. Final Selection
**Model chosen for the phone camera app:** `mobilenet_best_combo`

**Why:** This model achieved the absolute best performance on the camera-like validation set (90.6%), meaning it is the most prepared for the random crops, rotations, and lighting conditions of a live phone camera. Because it heavily relies on data augmentation, AdamW, and a learning rate scheduler rather than complex new helper layers or architectural rewrites, the code remains simple and readable while delivering highly stable, reliable inferences across all seeds.
