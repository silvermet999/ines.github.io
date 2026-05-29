---
# yaml-language-server: $schema=schemas/page.schema.json
Object type:
    - Page
Created by:
    - silver
Creation date: "2025-08-17T12:52:30Z"
id: bafyreibsoyzudkezhe7djhsvyz3g56vfxblnu6exxgaukez5gvd53ycpiq
---
# Causality    
## Definition   
Claim that ML is a corr pattern recog system → works well on data with same distribution, vulnerable to distribution changes
=> Causal ML: still at infancy → developed for low dim & simple structured data   
## Pillars of curr causal learning   
1. Causal discovery: focused on identifying causal relationships   
2. Causal effect estimation: assuming in causal models & quantifying effects   
   
Causal ML is a discipline to formalize the pursuit of identifying, modelling & quantifying causal relationships
**Distinguish btw cause & effect:** figuring out the true dir of influence btw vars, not just identifying they’re related but identifying who is the driver & who is the responder   
**Association:** relationship btw X & y not necessarly mathematical (broader term) → +/-, Linear/non-linear, cat/num
**Corr:** Quantitative measure of linear relationship   
## Causal ML value prop:   
- With tools & assumptions of causal discovery, you can learn causal graphs that represent causal relationship of the underlying system that gen data
   
- With tools of causal effect estim, you can quantify & estimate causal effects based on quantitative knowledge of the causal graph using causal inference (framework: [do-calculus](do-calculus.md))
   
- Causal ML brings them together with DNN to provide models fit for the complexities of a causal world   
   
   
*Credits: [https://medium.com/causality-in-data-science/what-is-causal-machine-learning-ceb480fd2902](https://medium.com/causality-in-data-science/what-is-causal-machine-learning-ceb480fd2902) *   
