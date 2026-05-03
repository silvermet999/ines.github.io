---
# yaml-language-server: $schema=schemas/page.schema.json
Object type:
    - Page
Created by:
    - silver
Creation date: "2025-08-19T16:13:37Z"
id: bafyreifvicukwe6zps7yffpknujo4rgs6yru3utuymawx5i5fztoyf3qum
---
# Prioritized Experience Replay (PER)   
- Some exps may be more important than others but might occur less freq   
- Instead of sampling batches uniformally, PER use a criterion for sampling dist to define the priority of each tuple of exp where there’s a big diff btw pred & the TD (Temporal Diff) target since it means that we have a lot to learn about it   
- **DQN's Temporal Diff (TD) Target:** TD target = r + γ max\_a’ Q(s’, a’)   
- **PER’s TD: **Pi α \|δi\| + ε where P is priority of the ith exp   
- The abs val of the magnitude of the TD error: Pt = \|δi\| + ε where δi is the magnitude of TD error and ε is a constant to avoid 0 exp prob   
- Priority put in the exp of each replay buffer   
- No greedy prioritization (overfitting)   
   
=> Stockastic prioritization which gen the prob of being chosen for a replay   
- P(i) = Pi / {∑k Pkα} where Pi is priority val, α is a hyperperam to reintroduce randomness in the exp selection (if α=0 → uniform, if α=1 → highest priority exp) and ∑k Pkα is normalized by all priority val in the replay buffer   
- Stockastic update rule prob: exps must match the underlying dist   
- In normal exp, it’s normal: overfitting when updating ws => in priority, introd a bias twds high priority: update ws with a small portion of exp => **Importance Sampling (IS)**   
- **IS** = (1/N 1/P(i))b where N is the replay buffer size, P(i) is prob sampling and b control how much IS affect learning and annealed up to 1 over training dur bc ws are more important at the end when q begin to converge   
- Technically, can’t implement PER by sorting all the exp replay → not efficient due to O(nlogn) for inserting & O(n) for sampling   
- Inserting a new exp w priority into a sorted list take O(nlogn) time if maintaining order. Sampling exp according to priority takes O(n) time to scan through list & calc sum ws   
   
## **Data structure => SumTree**   
![1*Go9DNr7YY-wMGdIQ7HQduQ](files/1-go9dnr7yy-wmgdiq7hqduq.png)    
- A binary tree with max of 2 children for each node. Leaves contain priority vals + data arr pnting to leaves containing exp   
- Then create a mem obj that contain the SumTree + data   
- Sample minibatch of size k, range [0, total priority] will be divided into k ranges. A value is uniformaly sampled from each range   
- Exp of these sampled vals are retrieved from the SumTree   
   
   
*Credits: [https://www.freecodecamp.org/news/improvements-in-deep-q-learning-dueling-double-dqn-prioritized-experience-replay-and-fixed-58b130cc5682/](https://www.freecodecamp.org/news/improvements-in-deep-q-learning-dueling-double-dqn-prioritized-experience-replay-and-fixed-58b130cc5682/) *   
