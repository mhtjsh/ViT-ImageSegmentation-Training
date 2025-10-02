Q1. Vision Transformer Experiments on CIFAR-10
==========================================

### Setup

*   Dataset: CIFAR-10 (45k train / 5k val / 10k test)
    
*   Model: Vision Transformer (ViT) with patch size = 4, d\_model = 128, default depth = 6.
    
*   Optimizer: AdamW (lr=3e-4, wd=0.05), scheduler: CosineAnnealingLR.
    
*   Evaluation metric: Top-1 validation accuracy.
    

1\. Baseline ViT (no augmentation)
----------------------------------

*   **Final Accuracy**: ~74.7% (Val) after 20 epochs.
    
*   Training curves showed stable improvement, plateauing ~75%.
    
*   Observation: Without augmentation, model underfits complex classes, especially visually similar ones (cat/dog, airplane/ship).
    

2\. With Data Augmentation
--------------------------

*   Techniques: RandomCrop (32, padding=4), RandomHorizontalFlip.
    
*   **Final Accuracy**: ~81% (Val), ~82% (Train).
    
*   **Improvement**: +6–7% vs baseline.
    
*   Observation: Augmentation clearly improves generalization and reduces overfitting. Gains were consistent across epochs.
    

3\. Depth/Width Trade-offs
--------------------------

Tested multiple configs (depth = number of encoder blocks, width = embedding dim):

| Config (Depth, Width) | Best Val Acc   |
|-----------------------|----------------|
| (4, 128)              | ~70.8%         |
| (6, 128) baseline     | ~74.7%         |
| (8, 128)              | ~76–77%        |
| (6, 192)              | ~78–79%        |
| (6, 256)              | ~80%           |
| (8, 192)              | ~81%           |


**Insights**:

*   Increasing depth beyond 6 improves accuracy but with diminishing returns.
    
*   Width (embedding dim) is more effective than depth in this regime: going from 128 → 192 → 256 yields bigger gains than adding layers.
    
*   Best config tested: (8,192), ~81% Val Acc — comparable to augmentation benefits.
    

Summary 
-----------------

1.  **_Baseline ViT_** underperforms (~75%) without augmentation.
    
2.  **_Data augmentation_** alone boosts performance to ~81%, showing it’s critical for small datasets like CIFAR-10.
    
3.  **_Scaling depth/width_** improves accuracy, but width scaling is more impactful than depth scaling for this dataset size and compute budget.
    
4.  _For CIFAR-10: **augmentation + moderate width scaling** (~192–256) gives the best trade-off._
