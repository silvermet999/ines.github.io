---
# yaml-language-server: $schema=schemas/page.schema.json
Object type:
    - Page
Created by:
    - silver
Creation date: "2025-08-19T01:04:22Z"
id: bafyreiflso5by4f72g4tnyu3fsa5rqehnugsx6zb6beld4zahbhxx33x2u
---
# Loss Behavior   
## Sample Equations   
- 1D maniforld: s=L.D-1
   
- 2D manifold: s=L.D-1/2
   
- d-dim manifold: s=L.D-1/D   
   
## Theory   
- The error of testing pnt is bounded by a function of its distance to the nearest training pnt
   
- If model is powerful enough to perfectly fit the training data then the learned manifold will match the true data manifold exactly at training pnts
   
- A DNN using ReLU is able to linearly interpolate btw these training pnts to make pred. If we assume that the manifolds are smooth (Lipschitz continuity) then we can use a Taylor expansion to show that the error will scale as the distance btw the nearest training & testing pnts sqrt (the ntw fits the linear term of the expansion, first non-zero term is quadratic: theoretic models that predict scaling laws)
   
- We establish that the avg distance btw training pnts scale as the size of the ds; D-1 over the dim of the manifold so we can sqrt this term to get an estim for how the error scales with ds size and compute D-2/d
   
- Applying a similar Taylor expansion to CE function shows that the distance btw the pred & true val sqrt   
- Theoretically, CE is expected to scale as (D-2/d)² = D-4/d
→ Worst case error: upper bound; CE is expected to scale proportionally or better
=> Resolution Limited Scaling: more data is allowing the model to better resolve the manifold
   
- Considering the rship btw model size & loss, the theory pred the same fourth power rship. In this case, the additional model params are allowing the model to fit the manifold at higher resolution   
   
## Theory vs Experiment (by OpenAI)   
- Obs the CE scaling as (D/5.4 \* 1013)-0.095 where 0.0095 is α\_D.
If the theory is correct, then α\_D >≈ 4/d where d is the intrinsic dim of the data
   
- Estim d is tricky since it requires estim the dim of the manifold (intrinsic dim of language)
   
- Started with easy ds where intrinsic dim can be estim or known → theory holds: in case where synth training data of known intrinsic dim is created by a teacher model & learned by a student model
   
- Holds well with smaller scale img ds (MNIST)   
- In language: the dim is ≈ -4/d = -0.095 and d = 42.1
→ Result was tested by estim the intrinsic dim of the manifolds learned by a language model & found the intrinsic dim to be sig higher on the order of 100
   
- No unified theory yet   
   
   
*Credits: [https://youtu.be/5eqRuVp65eY?si=1WBpRwsHg2kIt26T](https://youtu.be/5eqRuVp65eY?si=1WBpRwsHg2kIt26T) *   
