---
# yaml-language-server: $schema=schemas/page.schema.json
Object type:
    - Page
Created by:
    - silver
Creation date: "2025-08-19T00:15:11Z"
id: bafyreiccf2hage4gijlzowh4i67ekc7ckxroebqq3vluw7o56qp6xrgcii
---
# Do-calculus   
## Definition   
- Axiomatic sys for replacing prob formulas containing the do-operator with ordinary conditional prob   
- 3 axiom schemas that provide graphical criteria for when certain subs may be made
   
- “What will happen to y if we intervene & set X to a certain value” => P(Y \| do(X=x))
   
- Unlike standard conditional prob P(Y \| X=x) the do-expression reflects a causal intervention not a passive obs
   
- Usually, we only have obs data → do-calculus derives causal effects from purely observational dist
   
- Perl’s do-calculus allows us to identify any causal quantity that is identifiable.
   
- P(Y \| do(T=t), X=x) where Y, T & X are arbitrary sets (don’t need to be scaler. X are covariates, T are treatments and Y are outcomes.
G\_X where bar is under X means all arrows pnting from X are removed. bar is over X means that all arrows pnting to X are removed   
   
## Prereq:   
- **ADMG:** Acyclic Directed Mixed Graph: rep causal rships → contains a directed edge for causal rships and bidirected edge for unobserved confounding (latent var)
   
- **Unobserved confounding:** confounding occurs when z vars share common cause, influencing both
   
    - If the common cause is unobs, then it’s unobs confounding = latent var
   
    - Given A&B&C: C → A & C → B & C isn’t measured => spurious association btw A & B even if A doesn’t cause B => A ←> B meaning that A doesn’t cause B V.V. but there’s a latent var affecting both.   
- **MC:** Markov Chain   
- **d-separation:** directional separation to determine if A is indep from B given C in ADMG. If all paths btw A & B are blocked by C then A is d-separated from B given C. A path btw A&B is blocked by C if:
   
    - Non-collider node: M
   
        - X → M → Y   
        - X ← M → Y
   
        - X ← M ← Y   
    - C = {M}   
    - Collider & neither are in C: X → M ← Y   
- **Example: **given I is Intelligence, E is Education, J is Job perf and H is Hired: I → J ← E and J → H
=> J is a collider if non cond on J or H then I&E are d-separated
=> if we know nothing about job perf or hiring, intelligence says nothing about education.   
    - If cond => if we know someone is a high perf then learning that they’re educated means intelligence or V.V.
=> Collider bias: explaining away effect
   
    - If we cond J then path is blocked => if we already know job perf, then intelligence doesn’t contribute   
- **d-separation vs do-operator: **d-separation is a graphical criterion for determining cond indep in obs dist. Do-operator is a math operator rep intervention (manipulation), tells what happens when we actively set a var to a specific value.   
- **Generalization of d-separation to interventional dist **→ how to determine cond indep in manipulating G (not obs G)
   
- **In obs G, X ⊥ Y \| Z means P(z\|y, z) = P(x\|z): **this is the std def of cond indep; knowing that Y doesn’t give additional info about X beyond Z.
   
- **Interventional d-separation **extends this to interventional dist
X ⊥ G\_T Y \| Z means that vars X & Y are d-separated by Z in manipulated graph => implies cond indep in the interventional dist P(x\|y,z, do(t) = P(x\|z do(t))   
   
## The Generalization Steps:   
- **Graph manipulation:** when perf do(T), we create G\_Tbar by removing all incoming edges to T (fixing T’s vals)
   
- **Apply d-separation in G\_T:** check if Y&Z are cond indep given T&W in G\_T using std d-separation rules
   
- **Interventional indep:** if d-separated, then P(y\|do(t), z, w) = P(y\|do(t), w)   
- Generalization is crucial bc: Y&Z might be dependent due to confounders in obs G, or possible broken paths after intervention making Y&Z indep in manipulated G   
   
## 3 Rules:   
Given G a ADMG on var set V & P satisfies (MC-d separations) 3 rules:   
1. **Insertion / Deletion of obs: **P(Y\|do(T), Z, W) = P(Y\|do(X), W) if Y & Z are d-separated by T U W in G; G is obtained by removing all arrows pnting into X. Denoted Y ⊥ G\_Tbar Z \| T,W : y is cond indep of Z given T and W in G => observing Z doesn’t give additional info about Y beyond what you already know from intervention do(t) and covariates W. Therefore, you can drop Z from the cond set
- Exp: studying the effect of drug (T) on recovery (Y) also obs mood (Z) and age (W) → obs mood is irrelevant for recovery given intervention & age
- Generalization of d-separation to interventional dist → how to determine cond indep in manipulating G (not obs G)   
2. **Action / obs exchange:** P(Y\|do(X), do(Z), W) = P(Y\|do(X), Z, W) if Y&Z are d-separated by X U W in G, which is obtained by removing all arrows pnting to X & all arrows pnting out of Z. Denoted Y ⊥ G\_{Tbar Zbar\_) Z \| T,W (G is doubly manipulated) => Y&Z are d-separated given T&W => replace an intervention with an obs Z. Range of I\_x = range of X + “off” val. When I\_x is off, the val of X is determined by its other parents in the causal model. When I\_x takes any other val, X takes the same val, regardless of the value of X’s other parents. If X is a set of vars, then I\_x will be the set of corresponding intervention vars.
- If after intervening on T and removing Z’s ability to causally effect other vars, Z has no remaining causal path to Y given T&W, then it doesn’t matter if we intervene on Z or obs Z
- Exp: after fixing education (T) and cutting income’s (Z) outgoing effects, income has no causal path to health (Y) given education (T) & age (W) => Forcing income vs obs it gives the same info about health given education intervention
- If x is a var in causal model, its corresponding intervention var I\_x is an exogenous var with one arrow pnting into X.   
3. **Insertion / deletion of an action: **P(Y\|do(X), do(Z), W) = P(Y\|do(X), W) if Y&I\_Z are d-separated by X U W in G, which is obtained by removing all arrows pnting into X => remove unnecessary interventions
Exp: given X is treatment, Z is intermediate biomaker affected by treatment, Y is recovery and W is baseline health (covariate): W → X → Z → Y & W → Y
- remove incoming edges to X → do(X): X is fixed by intervention   
   
       - remove outgoing edges from Z → do(Z): Z is set by intervention
       => Once treatment & acc for patient baseline, setting the biomaker 
       artificially doesn’t influence recovery.
        => Doctors don’t need to manipulate the biomaker (Z) if they’re already 
        setting the treatment (X) considering baseline health (W). The 
        biomaker’s val becomes irrelevant for pred recovery   
   
*Credits: [https://plato.stanford.edu/entries/causal-models/do-calculus.html](https://plato.stanford.edu/entries/causal-models/do-calculus.html) *   
