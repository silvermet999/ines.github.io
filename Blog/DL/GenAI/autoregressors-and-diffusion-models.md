---
# yaml-language-server: $schema=schemas/page.schema.json
Object type:
    - Page
Created by:
    - silver
Creation date: "2025-08-25T00:48:27Z"
id: bafyreidywo5fa7v72uppt34g7j3wibjdkkk27sbc3lolajtb3yg75sz4my
---
# Autoregressors and Diffusion Models   
## Autoregressors   
- Reconstruct pixels from img   
- Reconstruct those pixels   
- ChatGPT uses an autoregressor   
    - Transformer to process and encode context   
    - Autoregressor to gen text step by step   
   
=> Autoregressors are not used to gen imgs anymore: too long to run   
- If stat indep: taking pixels from diff parts of the img (spread out), then predict → regressively taking out more pisels over the next steps   
- To minimize computation time, we want to take as many pixels from diff areas as possible => To maximize avg spread   
- Adding noise to img instead of removing pixels   
- Where to start training:   
    - First scale down org img, then add noise   
    - If repeated many times, info from org img disappear   
    - The result is equiv to a pure random sample from the noise dist   
   
    **=> Denoising diffusion**   
- By adding noise to an autoregressor, we can spread out the removal of info all across the img, which makes the pred vals as indep if each other as possible => fewer net evals   
- **Causal archi:** train all these gen steps while eval only once (causal CNN, causal transformers), autoregressors gen always uses it (not the case for diffusion models)
   
   
## Diffusion   
- The prev described diffusion is pred the slightly less noisy img   
- It's better to pred org (clean) img → it's trained to gen imgs from noisy data   
- To avoid messy pred, we can train the model to pred the noise added to the img → plug it in:   

$$
\hat x = x_t - (1 - \alpha) (1-\bar \alpha _ t)^{-0.5}\epsilon
$$
   
where xhat is the pred clean img, x\_t is the input after noise at time t, epsilon is the pred noise, alpha is the amount of noise added and alphabar\_t is the cummulative amount of noise at step t   
=> The model pred noise from diff noise samples: avg noise is valid noise   
- Steps:   
    1. Start with a dist from some target dist: x\_0   
    2. Define a diffusion process that gradually add noise over t timesteps: x\_T   
    3. Reverse the process: **denoising**   
- The fwd process q takes the form of a Markov chain where the dist at a particular timestep only depends on the sample from the immediately prev step so we can write out the dist of corrupted samples conditioned on the initial data pnt x\_0 as the product of successive simple step conditionals:
   

$$
q(x_{1:T}|x_0) = \prod _{t=1}^T q(x_t|x_{t-1})
$$
- Continuous data:   

$$
q(x_t| x_{t-1}) = \mathcal N (x_t; \sqrt{1-\beta_1} x_{t-1}, \beta_t I)
$$
   
=> Each transition is param as a diagonal gaussian, where beta\_t is the variance at t timestep (hyperparam), follow a fixed schedj for particular training run). Beta increases with time and beta\_t ∈ (0,1): The coef will be non zero; mean of the gaussian getting close to 0   
- As x\_T approaches inf, q approaches a gaussian centered at zero with indentity cov, losing all info about the org sample
   
- Using a large albeit finite # steps allows to set the indiv variances beta\_t << 1 (very small) => undoing the steps of the fwd process sample dist at time t-1
   
- q resembling a mix of gaussian with z modes
   
- We observe x\_t & infer the posterior dist over x\_t-1: where did the chain likely come from in order to arrive at x\_t   
- If the noise step q(x\_t \| x\_t-1) is allowed to be large => uncertain about the location of x\_t-1   
- If noise step is smaller, we can justify in modeling the posterior of the fwd step q(x\_t-1\|x\_t) with a unimodal gaussian   
   
=> Limiting the step size of the true reverse process will have the fwd process   
- Diffusion param each learnt reverse step to be a unimodal diagonal gaussian:   

$$
P_\theta (x_{t-1}|x_t) := \mathcal N (x_{t-1}; \mu_\theta (x_t, t), \sum(x_t, t))

$$
- The model takes both x\_t and t as input in order to account for the fwd process variant schedj => diff timesteps are associated with diff noise levels and the model can learn to undo these individually   
- The reverse process is also set up as a Markov Chain:    

$$
P_\theta (x_{0:T}) := p(x_T) \prod^T p_\theta (x_{t-1}|x_t)
$$
   
is the joint prob of a seq of samples as a product of cond & marginal prob of x\_T, where 
   

$$
p(x_T) = \mathcal N (x_T; 0, 1)
$$
is pure noise dist -> in inference time, in order to gen a sample, we start from a gaussian and begin sampling from the learnt individual steps of the reverse process p(x\_t-1\|x\_t) until producing an x\_0
   
- Fwd process is designed to push a sample of the manifold into noise
   
- Reverse is trained to produce trajectory back   
- We have to marginalize over all possible trajectories, how we arrived at x\_0 starting from noise:   

$$
P_\theta (x_0) = \int P_\theta (x_0:T)dx_{1:T}
$$
   
=> Contractable (continuously shrunk into itself): we can max a lower bound view x\_1...x\_T as a latent vars and x\_0 as observed like in VAe
=> Fwd is the encoder, reverse is the decoder, except that the encoding is fixed: we focus on learning in the reverse   
- Derive variational evidence lower bound: log p\_theta (x) >= variational lower bound
   
- Likelihood term (recon) - KL divergence:   

$$
\log p_\theta (x) >= \mathbb E_{q(z|x)}[\log p_\theta (x|z)] - D_{KL} (q(z|x) -|| p_\theta (z))
$$
- Likelihood term encourages the model to max the expected density assigned to the data
   
- KL divergence encourages the approx posterior q(z\|x) to be similar to the prior on the latent var p(z)   
- Substituting x\_0 and x\_1:T :   

$$
\mathbb E_{q[x_{1:T}|x_0)} [\log p_\theta (x_0|x_{1:T})] - D_{KL} (q(x_{[1:T]} |x_0) || p_\theta(x_{1:T})) = \mathbb E_q [log \dfrac{P_\theta(x_{0:T})}{P_\theta(x_{1:T}|x_0)}
$$
- Refactor the chain prob into their indiv steps:   

$$
\mathbb E_q [\log p(x_T) + \sum_{t>=1} \log \dfrac {P_\theta(x_{t-1}|x_t)} {q(x_t|x_{t-1})}
$$
- Any arbitrary fwd process can be sampled directly in closed form:   

$$
q(x_t|x_0) = \mathcal N(x_t; \sqrt {\bar \alpha_t}x_0,(1-\alpha_t))
$$
   
where   

$$
\alpha_t :=1-\beta_t 

$$
and   

$$
\bar \alpha_t := \prod^t_{s=1} \alpha_s
$$
because the sum of indep gaussian steps is still a gaussian   
- The objective t>=1 can be obtained without having to simulate the entire chain -> we can optimize this obj by randomly sampling pairs of x\_t-1 and x\_t and max the cond density assigned by the reverse step to x\_t-1
   
- However, because diff trajectories may visit diff samples at time t-1 on the way to x\_t, the setup can have high variance limiting variance limiting training efficiency -> we can rearrange the obj as follows:
   

$$
\mathbb E_q[-D_{KL}(q(x_T|x_0)||q(x_T)) - \sum_{t-1}D_{KL}(q(x_{t-1}|x_t,x_0)||p_\theta(x_{t-1}|x_t)) + \log p_\theta(x_0|x_1)]
$$
   
where p(x\_T) is the start of the reverse process (fixed), same for q(x\_T\|x\_0); the fwd process
   
- The KL divergence btw a reverse step and a fwd posterior cond on x\_0
   
- If q(x\_t-1\| x\_t, x\_0) is known -> gaussian since the reverse is also a gaussian (p\_theta(x\_t-1\| x\_t))
=> It's comparing two gaussians and can be eval in close form: reduce variance instead of recon Monte Carlos samples, the targets for the reverse become the true posterior of the fwd process given x\_0   
- In denoising diffusion prob models (DDPM), authors select reverse process variances to time specific constance sum theta -> learning led to unstable training and lower quality samples   
   
=> Reverse is tasked with learning the means mu\_theta   
- Suggest reparam to pred the noise added rather than the gaussian mean
   
- First, rewrite sampling from an arbitrary fwd step by using an auxilary noise var epsilon:   

$$
x_t = \sqrt{\bar a_t}x_0 + \sqrt{1-\bar \alpha_t} \epsilon
$$
   
where epsilon ~ a normal dist → constant dist indep of fwd timestep T and the reverse can be designed to pred epsilon:   

$$
loss = \mathbb E_{x_0,\epsilon,t} [||\epsilon-\epsilon_\theta (x_t,t)||^2]
$$
where epsilon\_theta (x\_t,t) is the reverse step ntw, we delete w\_t; step specific weighting from VLB   
- Authors found that a simpler version of the version of the variational bound that discards the weights that appear in the org bound led to better sample quality
-> Compared to avg loss, their obj downweight steps that have very small noise at early timesteps of fwd: focus on more challenging steps   
- Conditional reverse step ntw: epsilon\_theta (x\_t, t, y)
   
- Guiding the model with a separate classifier can help: push the reverse diffusion in the direction of the gradient of the target label prob with respect to curr noise img
   
- **Classifier-free guidance:** 
   
    - Solution to the reliance on a classifier in **conditional diffusion**   
    - In noise pred, the model is run twice (with & without): the pred without - the pred with => conditional - conditional modes
   
    - The cond label y is set to a null label with some prob during training:
   
    
$$
\epsilon_\theta(x_t, t, y) = \epsilon_\theta(x_t, t, \empty) + s*\epsilon_\theta(x_t, t, y) - \epsilon_\theta(x_t, t, \empty)
$$
   
    where s is the guidance scale >=1   
    - At inference, the recon-ed samples are pushed further towards the y cond direction and away from the null label. Then, replace known regions of an img with a sample from the fwd process:
   
    
$$
\hat x_t \sim q(x_t|x_0) 
$$
   
    after each reverse step -> edge artifacts (no context) => fine tune model from this task: remove samples randomly from img & have the model fill them in, conditioned on the full context   
- Diffusion limitations: slow markov chain, the lower bound can be competitive on density estim benchmarks -> continuous time formulation of diffusion models can lead to prob flow ODE (approx log likelihood via num integration)
   
- Connection btw diffusion and score matching models:
   

$$
score := \triangledown_x \log p_{data}(x)
$$
   
Then a markov chain produce samples from learned dist guided by this gradient
=> Score is close to the noise up to a scaling factor
=> Denoising in diffusion is trying to follow the gradient of the data log density    
   
