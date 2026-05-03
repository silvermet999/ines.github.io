---
# yaml-language-server: $schema=schemas/page.schema.json
Object type:
    - Page
Created by:
    - silver
Creation date: "2025-08-19T16:13:37Z"
id: bafyreiexb5ifqx3ik7ogh7se6mq4r2kgaxyzefx7hhuwdq3gcyfoissvje
---
# Proximal Policy Approximation (PPO)   
- Sensitivity to hyperperam changes   
- Environment interaction learning   
- PPO is an **on-line policy**:   
    - Agent can pick actions   
    - Agent follow his policy   
   
(≠**Off-line policy**: exploration, replay buffer)   
- Doesn’t store experiences   
- Discounted rewards: immediate rewards pref over long-term   
- Value function: estimate of the sum discounted rs like in supervised learning, take state & estimate   
- Action: discounted r value function => how much better is the action taken vs what normally happens   
- Increase the likelihood of selecting a good action   
- Probab of action now than old version   
- If action is good → flatten at 1 + e to not overdo action updates (clip objective function)   
- If action is bad → decrease at 1-e to reduce probab of selecting action   
- Agent functions:   
    - Clipped action   
    - Update value function + policy output sharing loss function with value function   
    - Entropy in normal dist to encourage exploration   
   
   
*Credits: [https://youtu.be/5P7I-xPq8u8?si=lVTDs1MqYOnI\_jjr](https://youtu.be/5P7I-xPq8u8?si=lVTDs1MqYOnI_jjr) *   
