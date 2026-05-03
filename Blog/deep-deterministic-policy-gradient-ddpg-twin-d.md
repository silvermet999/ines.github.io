---
# yaml-language-server: $schema=schemas/page.schema.json
Object type:
    - Page
Created by:
    - silver
Creation date: "2025-08-19T16:13:37Z"
id: bafyreif6egv7yh5547zixfdvasw4nkh26n4i4q5unqopk6pox3myfdsv6e
---
# Deep Deterministic Policy Gradient (DDPG) + Twin-delayed DDPG (TD3)   
Implemented TD3 in my paper: https://www.scitepress.org/Papers/2025/135677/135677.pdf    
## DDPG   
- Actor: input state dim, output action dim   
- Critic: input state dim, output 1 value in reward range   
- Select action: input state, output actor   
- Training:   
    - Define iter   
    - Sample replay buffer: s, a, s’, r, d   
    - Target Q: r \* d \* discount \* critic target (s, actor\_target(s))   
    - Current Q estim: critic(a, s’)   
    - Critic loss: current Q - Target Q   
    - Actor loss: critic(a, actor(a))   
    - Update target   
   
   
