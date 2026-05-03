---
# yaml-language-server: $schema=schemas/page.schema.json
Object type:
    - Page
Created by:
    - silver
Creation date: "2025-08-19T01:15:06Z"
id: bafyreidw6ijr4o4bc2sf4ordbdw5rvkfub3yucj6cq2nczthgnhtgmqn5i
---
# Adversarial Autoencoder (AAE)   
Implemented conditional AAE with attention-based discriminator in my paper: https://www.scitepress.org/Papers/2025/135677/135677.pdf    
## Low dim/ Latent var:     

$$
z \in \mathbb{R}^p

$$
where p  << d.
Enc & dec conditional dist p(z\|x) and p(x\|z) respectively.
Prior dist p(x) on the latent var → p-dim follow normal dist N(0,1)
G(x) = z ~ q(z)
D activation function is sigmoid   

$$
D(z) := 
\begin{cases}
1 & \text{if $z$ is real, } \; z \sim p(z), \\
0 & \text{if $z$ is generated, } \; z \sim q(z).
\end{cases}

$$
Recon: MSE is min btw x and xhat
Reg with GAN’s loss   
## 
Optim back propag:   

$$
\beta_1 , \beta_2 = \arg\min_{\beta_1, \beta_2} \; \lVert \hat{x} - x \rVert_2^2

$$
## Recon:   

$$

\beta_3(k+1) := \beta_3(k) - \eta \, \frac{\partial}{\partial \beta_3} V\!\bigl(\beta_3, \beta'_1\bigr)

$$

$$
\beta_1(k+1) := \beta'_1 - \eta(k) \, \frac{\partial}{\partial \beta_1} V\!\bigl(\beta_3(k+1), \beta_1\bigr)
$$
β1 = G & β3 = D
Sampling approaches of z from of z from coding layer of AE with posterior q(z)
Deterministic z is the output of enc directly: z=βi(xi)
The stockasticity in q(z) is in the dist of the ds p\_data (x)
**Gaussian posterior:** like VAe the enc outputs μ
z is sampled from gaussian z~N(μ(xi))
The stockasticity in q(z) is in p\_data(x) and the gaussian as output of the enc
**Universal approx posterior: **data pnt x + some noise η with fixed dist as input to the enc (Gaussian where ηi ~ N(0, I))
The stockasticity in q(z) is in p\_data(x) and η   
## Supervised AAE:   
**Version 1:** 1HE y ∈ ℝe to D; β3 by concat with z. D learns y of pnt x + discrimination of real & gen zs => G gen z corresponding to y.
**Version 2:** 1HE y is fed to the dec; β3 by concat with z. Dec learns to recon the data pnts by using the label of pnts. G gen z based on y of pnt.
**Version 3:** version 1 + version 2   
## Semi-supervised AAE:   
AE(β1 & β3)
Adv learning for z (β1 & β3)
Adv learning for class (β1 & β3)
G gen both y ∈ ℝ^c & latent var z ∈ ℝ^p
G AF softmax to output (for y) : c-dim vector, behaves as prob
Least AF is linear (z)   
**3 phases: recon, reg, classification**
Gen 1HE y for x; if x has y, use its y for training β1 & β3, if not, sample y ∈ ℝ^c from cat dist (y ~ cat(y))
Cat(y) gives 1HE vector where prior dist of every class is estimated by the proportion of class to the total labels   
   
