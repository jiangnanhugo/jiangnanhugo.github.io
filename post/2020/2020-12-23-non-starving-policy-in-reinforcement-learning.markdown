---
layout: "post"
title: "non-starving policy in reinforcement learning"
date: "2020-12-23 21:33"
---
The  following content is copied from the reference:

```
A non-starving policy is a (behavior) policy that is theoretically guaranteed to visit each state and take all possible actions from each state an infinite number of times, so that to always update $$Q(s,a), \forall s,\forall a$$, an infinite number of times. In the context of off-policy prediction, this criterion implies that any trajectory will have no zero probability under a behavior policy. As a consequence, the experience from the behavior policy sufficiently covers the possibilities of any target policy.

An example of a non-starving policy is the $$\epsilon$$-greedy policy, which, with $$0<\epsilon\le 1$$ (which is usually a small number between 0 and 1) probability, takes a random action from a given state, and, with $$1-\epsilon$$ probability, takes the current best action, that is, the action with the highest value from a given state, according to the current value function.
```

### Reference
- [What is a non-starving policy in reinforcement learning?](https://ai.stackexchange.com/questions/15365/what-is-a-non-starving-policy-in-reinforcement-learning)
