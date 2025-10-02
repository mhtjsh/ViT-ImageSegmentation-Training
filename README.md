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


Q2. Text-Driven Image Segmentation with SAM 2
==============================================

Overview
--------

This repository contains the implementation for **Text-Driven Image Segmentation using SAM 2**, as required in Question 2.The goal is to segment an object in an image using a **text prompt**. The pipeline integrates **CLIPSeg** (for text-to-region seed generation) with **SAM 2** (for segmentation refinement).

The notebook (``q2.ipynb``) is designed to be runnable end-to-end on **Google Colab**.Note: The uploaded version has **all outputs cleared** to avoid errors and size issues; only the code and structure remain.

Pipeline
--------

The segmentation pipeline follows these steps:

1.  **Install dependencies**
    
    *   Required libraries for SAM 2, CLIPSeg, and utilities are installed in the first cells.
        
2.  **Load image dataset**
    
    *   The notebook randomly samples images from a dataset range.
        
    *   By default, it picks from **indices 1–10000**.
        
3.  **Accept text prompt**
    
    *   Example: "cat", "dog", "car".
        
    *   Text prompt is mapped to candidate regions via **CLIPSeg** (or similar models).
        
4.  **Generate seeds**
    
    *   CLIPSeg outputs region proposals based on the text query.
        
    *   These proposals are used as seeds for SAM 2.
        
5.  **Apply SAM 2**
    
    *   SAM 2 refines seeds into accurate segmentation masks.
        
    *   Final segmentation mask is displayed as an overlay on the image.
        

User Options
------------

*   **Image selection**
    
    *   By default, a random image is chosen from indices **1–10000**.
        
    *   You can change this range or directly specify an index.
        
*   **Class selection**
    
    *   The option text\_prompt (cat\_names) lets you specify the object to segment.
        
    *   Multiple class indices are supported:
        
        *   0, 1, 2, or 3 (depending on how many classes are present).
            
    *   For example:
        
        *   text\_prompt = cat\_names\[0\] → uses first class name
            
        *   text\_prompt = cat\_names\[2\] → uses third class name
            
    *   If an image contains **4 or more classes**, you can extend to index 3 or higher.
        

Running the Notebook
--------------------

1.  Open q2.ipynb in **Google Colab**.
    
2.  Run the install cells at the top in housekeeping tab.
    
3.  Let the notebook pick a random image or set an index manually.
    
4.  Modify ``text\_prompt`` to try different segmentation targets.
    
5.  View the final **mask overlay** in the output cell.
    

Limitations
-----------

*   Current implementation works only for **images**, not videos.
    
*   Propagation across video frames with SAM 2 is not implemented due to time constraints
    
*   Quality of segmentation depends on text–image alignment in CLIPSeg.
    
*   Random sampling may occasionally pick images with poor matches.

*   If multiple instances of the queried object exist, the model does not distinguish between them i.e. no instane disambiguation.
