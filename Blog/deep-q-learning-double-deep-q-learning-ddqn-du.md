---
# yaml-language-server: $schema=schemas/page.schema.json
Object type:
    - Page
Created by:
    - silver
Creation date: "2025-08-19T16:13:37Z"
id: bafyreia24yqspt3dfarvwctajlf5qtkghcf2r65g2qufjcywgs2hftic7q
---
# Deep Q-Learning + Double Deep Q-Learning (DDQN) + Duel Deep Q-Learning   
## Q Agent Functions   
1. Choose action (exploration vs exploitation)   
2. Learning from experience (updating Q-val)   
3. Manage exploration (reducing randomness)   
   
## Exploration vs Exploitation   
- **Exploration:** try new actions to learn about the env => epsilon   
- **Exploitation: **use curr knowledge to get the best rewards => 1-epsilon   
   
=> Start with high epsilon then reduce   
