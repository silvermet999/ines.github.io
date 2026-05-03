---
# yaml-language-server: $schema=schemas/page.schema.json
Object type:
    - Page
Created by:
    - silver
Creation date: "2025-08-19T01:15:06Z"
id: bafyreigqpjoempdztwyxjdm6qwq3tludpon3xginccjdgf2a6mi5ad5fuu
---
# Generative Adversarial Network (GAN)   
## GANs   
   
## Cycle GAN (Cycle-consistant GAN):   
- Img to img translation without the need to pair training imgs   
- Let X & Y be two domains of img translation: we have two gen G: X→Y and F: Y→X   
- Two discriminator:    
    1. D\_X for judging X and F(Y)   
    2. D\_Y for judging Y and G(X)   
- Two losses:   
    
$$
V(D_Y, G, X, Y) := \mathbb{E}_{y \sim P_{data}(y)}[log(D_Y(y))] + \mathbb{E}_{x \sim P_{data}(yx)}[log(D_Y(G(x)))]

$$
    
$$
V(D_X, F, X, Y) := \mathbb{E}_{x \sim P_{data}(x)}[log(D_X(x))] + \mathbb{E}_{y \sim P_{data}(y)}[log(1-D_X(F(y)))]


$$
- Cycle consistency loss have F(G(x)) ≈ x and G(F(y)) ≈ y:   

$$
V_{cyc} (G, F) := \mathbb{E}_{x \sim P_{data}(x)}[\lVert F(G(x) - x \rVert_1] + \mathbb{E}_{y \sim P_{data}(y)}[\lVert G(F(y))-y \rVert_1]
$$
- The overall loss function is:   

$$
min_{G,F} max_{D_X, D_Y} V(D_Y, G, X, Y) + V(D_X, F, X, Y) + \Lambda V_{cyc}(G,F)
$$
   
where lambda > 0 is the reg param   
   
*Credits: Ghojogh et al. Generative Adversarial Networks and Adversarial Autoencoders: Tutorial and Survey*   
   
