---
# yaml-language-server: $schema=schemas/page.schema.json
Object type:
    - Page
Created by:
    - silver
Creation date: "2025-08-19T15:55:43Z"
Emoji: ⚙️
id: bafyreigncxov6fn5zsuj6gjyge25toni7rpomljyix5lbvwfbu7qm4kqda
---
# Mechanistic Interpretability   
- Residual streams are matrices when going through each layer   
- Logit = final row of input \* embedded matrix, AF is softmax   
- Output vary regardless of pred: positive/negative, after instruction tuning: one unified pred   
- In which layer(s) the output is determined => **Mechanistic Interpretability**   
- Final row of residual stream → mapping of the last word provided   
- Uses softcap before softmax, softcap predicts variance of the word (diff capitalization, special char, diff languages)   
- In early layers: pred is the same as the last word provided (shown in weak models)   
- Layer clamped out (fixed within a range): higher pred prob to enforce positive pred   
- Reverse clamping: higher pred prob in skepticism   
- Phenomenon of simple neuron & LLMs corresponding to multiple seemingly unrelated concepts is **Polysemanticity**, known in LLMs more than vision models   
- Control pred with archi change: force the model to have fewer neurons fire for any given input => stop the model from spreading concepts across multiple neurons   
   
=> What combination of neurons map clearly to which concepts: **Sparse Ae**   
- Find some combination of the neurons’ outputs that clearly responds to exp of doubt. Multiply & sum each vector = strength of concepts   
- Superposition hypothesis: model rep more concepts than neutons → more w vectors → matrix   
   
=> The concept vector should have few neurons firing => sparcity   
Sparse Ae forces most values ≈ 0 & multiply by 2nd w matrix & recon the org input with the rest of vals   
If **superposition hypothesis** holds, then the recon is close to the org:   

$$
Loss = 
\sum_i \bigl(a_i - \hat{a}_i\bigr)^2

$$
- We don't know what those concepts with high pred are   
- **Neuronpedia**: see what exp max any feature in any sparse AE   
[Neuronpedia](https://www.neuronpedia.org/)    
- Incapability of disentangling cross-layer superposition: **sparse cross-coders** (under dev)   
   
   
*Credits: [https://youtu.be/UGO\_Ehywuxc?si=gJ-hoKtm99Wq8xtX](https://youtu.be/UGO_Ehywuxc?si=gJ-hoKtm99Wq8xtX) *   
   
- Logit lens: how transformers make decisions   
- Leverage linear properties to break down logit output → use prisms for residual streams, attention layers & MLP layers   
- Each prism in the transformer seq splits the logits from prev prism → see influence of final output   
- Decoder-only transformer: embed → . → { norm → attention } → . → { norm → MLP } → + → norm → unembed   
   
=> Treat non-linear activations as constants : calc the contribution of components using linear transformation   
### 1. Residual Stream Decomposition   
- Main ntw step   
- The output of a logit in a decoder-only transformer:    

$$
logit = W_{unembeded} * diag(W_{norm}) * \dfrac{W_{embeded}+\sum^N_{i=1} (a_i+m_i)}{S}
$$
   
   
where W\_unembed ∈ ℝ^v\*d is the unembedding matrix, W\_embed is the token embedding vector, a\_i is the attention, m\_i is the MLP outputs at the ith layer, S is the norm factor (root mean square of the denominator) & W\_norm is the scaling vector of the last norm layer   
-  If S is a constant, we break down logits into embed, attention & MLP layer: logit = P\*w\_embed + P\*a1 + P\*m1 + … + P\*a\_N + P\*m\_N   
   
where P is the projection matrix: P = W\_unembed * diag(W\_norm)* (S^-1 I), P\*a1 represent indiv contribution of the residual vector a1 to the final logit score & the cummulative sum is the model's logit score up to layer l.   

$$
P*W_{embed} + \sum^{l}_{i=1}P(a_i+m_i)
$$
### 2. Attention Decomposition:
   

$$
a_l = \sum^{n heads}_{i=1} H^i_l
$$
→ break down attention outputs into a sum over its attention heads, where H^i\_l is the ith attention head's output at layer l   
- The attention head's output is a weighted sum of the OV circuit output for each token in th seq   
- The attention output at layer l:   

$$
H^i_l = \sum_p \alpha_p OV^i_l * diag(w_{norm}) \dfrac{h^p_{l-1}}{s^p_{l-1}}
$$
   
where alpha\_p is the attention weight from the curr token to prev, OV^i\_l is the OV matrix for ith attention head at layer l, h^p\_l-1 is the hidden state of token p from prev layer l-1 & s^p\_l-1 is the norm factor   
- If alpha\_p and s^p\_l-1 are constants, attention output is a sum of linear projection of prev layer's hidden state h\_l-1   
- Contribution of token via attention head:   

$$
P^{a_i}_l = P(\alpha_p I) OV^i_l diag(W_{norm})((S^{p}_{l-1})^{-1} I)
$$
- We can decompose h^p\_l-1 into sum od token embedding and all prec layers residual output using residual stream decomposition   
- Transformer attention: input embedding → projected into queries, keys and values   

$$
Attn(Q,K,V) = softmax(\dfrac{Q_K}{\sqrt{d_k}})V
$$
- Output of the head is transformed with the output matrix (O)   
- Each head has W\_Q, W\_K, W\_V, W\_O   
- The OV circuit : explain how value vectors V get transformed via output matrix O: Output from head = Attn (Ws) (V) (W)   
- Wv maps token embedding into val spaces   
- W0 maps val based into residual stream => OV circuit = Wv \* Wo   
- Exp: head read embedding of "is" & write gram feature into residual through OV circuit    
- OV matrix: Wov = Wv \*Wo   
- Descrive direct linear map from token embedding to what gets written   
- Use fulness in inspecting what the head can copy   
- Exp: if head's OV matrix proj into nxt token prediction of punctuation then head do punctuation handling   
   
### 3. MLP Decomposition   
- MLP layers consist of 2 linear transformation ; W\_up and W\_down with non-linear function "g" applied btw   
- Output of MLP's l:   

$$
m_l = W_{down} g W_{up}diag(W_{norm})\dfrac{h_l}{s^a_l}
$$
   
where h^a\_l is the hidden state from prev attention layer   
- MLP output m\_l:   

$$
m_l = \sum^n_{i=1} diag(W^i_{down}) g^i diag(w^i_{up})diag(W_{norm})\dfrac{h^a_l}{s^a_l}
$$
   
where n is # neurons   
- Compute the contribution of the ith neuron in l to the logit output → treat g and s^a\_l as constants   
- Projection matrix:   

$$
P^{m_i}_l = P * diag(W^i_{down})(g^i I) diag(W^i_{up})diag(W_{norm})((s^a_l)^{-1} I)
$$
- Projection matrix also trace how residual vectors from earlier layers influence the final output logit through neuron via residual stream decomposition
   
- Diag(W\_norm): layer norm takes x ∈ ℝ^d and LN(n) = (normal dist) (W\_norm) (b\_norm)   
- Take diag bc each feature is indep (otherwise, mix dim)   
- W\_up choose what features to extract from residual stream (patterns)   
- Activate some of those features with g   
- W\_down map them back into the stream   
- g decide which is active   
- W\_down write them   
- It's the analog of QK/OV in attention decomposition
   
   
### Example:
   
- In contribution plot: large positive and negative vals in the first and last layer => key contributions
   
- In first layer: strong + contribution => embed , in the second layer: strong - contribution => attention putput
=> The model's embedding and unembedding vectors are the same => the input strongly pred itself as the output (nature of dot)

- Embeddings: token -> vector /text -> hidden state
- Unembedding: vector -> token prob / hidden state -> text
- 
- A direct result of using prisms is having a linear projection that maps val embedding vector to candidates unmebedded vectors
- The gap btw the current val & other vals is important
- Combined effect of neurons might pred the corr answer
-> Neurons' contributions to the target logits are linear projections onto diff target token unembedding vectors
=> The neuron activity patterns are likely encoded in the target token unembeddings
- The embeddings projected to 2D using PCA => 2D projection of digit unembedding vectors
=> Transformers encode templates for outputs in the unembedding space -> MLP selectively reads these templates based on W_down (linear projection) -> by triggering a specific combination of neurons (each representing a template), the ntw ensures the logits reach their max value for tokens with the highest prob
   
*credits: *   
[index.pdf](https://neuralblog.github.io/logit-prisms/index.pdf)    
