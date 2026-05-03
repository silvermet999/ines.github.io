---
# yaml-language-server: $schema=schemas/page.schema.json
Object type:
    - Page
Created by:
    - silver
Creation date: "2025-08-17T12:40:27Z"
Emoji: "\U0001F333"
id: bafyreidt66hqndq376ln3duklytcy3tt4cln2urdascv2n3a57tev5446i
---
# Tree-based Algorithms   
## Random Forest   
- DT is sensitive to training data → high variance
   
- **Bagging:**
   
    - Create new data => **bootstrapping**
   
    - Multiple Trees = > **aggregation**
   
- Bootstrapping + feature selection = > random
   
- Log of total # features
   
   
## Gradient Boosting   
- **Ensemble:** combine outputs from individual trees
   
- **Boosting:** combine tree seq to connect the error of prev tree
Weak learners are DTs with one split => decision stump   
- Use gradient descent
   
- Compute residual → fit new tree to predict → update pred → weighted sum pred   
   
## XGB   
- **Distributed** GB
   
- L1 & L2 reg
   
- **Tree pruning** using depth-first approach ≠ stopping for tree splitting   
   
   
*Credits: [https://youtu.be/v6VJ2RO66Ag?si=BnDm5ySYXAQ\_SpE\_](https://youtu.be/v6VJ2RO66Ag?si=BnDm5ySYXAQ_SpE_) *   
*[https://youtu.be/yw-E\_\_nDkKU?si=BtkfLaK4ojXHztVT](https://youtu.be/yw-E__nDkKU?si=BtkfLaK4ojXHztVT) *   
