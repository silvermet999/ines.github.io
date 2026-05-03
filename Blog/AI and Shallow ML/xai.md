---
# yaml-language-server: $schema=schemas/page.schema.json
Object type:
    - Page
Created by:
    - silver
Creation date: "2025-08-17T12:27:18Z"
id: bafyreicm34wtctqzgftzs7kzdrtgsjcdumlqi5jzey72rerbtcv2ztczpy
---
# XAI   
## Definition:   
- Collection of procedures & techniques that enable ML algo to produce output & results that are understandable for human users   
- Key components for fairness, accountability & transparency (FAT)   
- Need arises from the fact that ML models are difficult to understand → black box
   
   
## Origin   
- Early example is [causality](causality.md) in ML by Judea Pearl    
- LIME (Local Interpretable Model-agnostic Explanation): uses local approx of the model to provide insights into factors that are most relevant & influential in the pred & has been used in domains   
   
→ explains a pred for a query pnt by finding important predictors & fitting a simple interpretable model
→ Model agnostic means treating model as a black box
→ The archi of XAI depends on specific approaches that are used tp provide transparency & interpretability   
## Key Components   
1. ML   
2. Explainable algo (provide insights about the factors most relevant. Can provide insights into the working of ML models)
   
3. Interface   
   
**XAI principles:** Transparency, Interpretability and Accountability
**XAI approaches:** Feature importance, attribution (each feature contribute to pred), viz.
**XAI techniques:**
1. Transparent: ML models
2. Post-HOC:
      2.1. Model Agnositic: LIME, SHAP (SHaply Additive exPlanation), ELI5 (Explain Like I’m 5)
      2.2 Model Specific: rule-based/local exp/ feature relevance/ saliency map
   
## Limitations   
Computational complexity, limited scope, lack of std-dization   
   
*Credits: [https://www.geeksforgeeks.org/artificial-intelligence/explainable-artificial-intelligencexai/](https://www.geeksforgeeks.org/artificial-intelligence/explainable-artificial-intelligencexai/) *   
   
