---
layout: default
title: AI & Shallow ML: Model Training
---
# Model Training   
## Scenarios   
- If training loss is increasing with high variance and the val loss is also increasing => decrease LR
   
- If training loss is with low variance and the val loss is increasing => increase LR
   
- If training and val losses are with low variance => decrease b1
If training loss is with high variance and val loss is increasing => increase b1
   
- If training loss is with low variance and val loss is decreasing => decrease b2
   
- If training and val losses are with high variance => increase b2   
   
## Regularization   
- Sched: stepLR, multistepLR, ReduceLROnPlateau
   
- Early stopping
   
- Noise: gaussian
   
- Denoising
   
- Gradient clipping
   
- L1 (ridge):
   
- L2 (lasso):
   
- ElasticNet
   
- Dropout
   
- Spectral Norm
   
- Gradient Penalty
   
- Bootstrapping: for overfitting
   
- Constructive learning: escape local minima
   
- Stockastic approxim / annealing
   
- Quick prop(Fahlman) / RPROP (Riedmillier)   
