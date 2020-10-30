---
layout: "post"
title: "Why is a target network required in DQN?"
date: "2020-10-30 15:54"
---

The following content is copied from the first reference link. It has a great explanation.

```
The difference between Q-learning and DQN is that you have replaced an exact value function with a function approximator. With Q-learning you are updating exactly one state/action value at each timestep, whereas with DQN you are updating many, which you understand. The problem this causes is that you can affect the action values for the very next state you will be in instead of guaranteeing them to be stable as they are in Q-learning.

This happens basically all the time with DQN when using a standard deep network (bunch of layers of the same size fully connected). The effect you typically see with this is referred to as "catastrophic forgetting" and it can be quite spectacular. If you are doing something like moon lander with this sort of network (the simple one, not the pixel one) and track the rolling average score over the last 100 games or so, you will likely see a nice curve up in score, then all of a sudden it completely craps out starts making awful decisions again even as your alpha gets small. This cycle will continue endlessly regardless of how long you let it run.

Using a stable target network as your error measure is one way of combating this effect. Conceptually it's like saying, "I have an idea of how to play this well, I'm going to try it out for a bit until I find something better" as opposed to saying "I'm going to retrain myself how to play this entire game after every move". By giving your network more time to consider many actions that have taken place recently instead of updating all the time, it hopefully finds a more robust model before you start using it to make actions.
```




### Reference
- [Why is a target network required?](https://stackoverflow.com/questions/54237327/why-is-a-target-network-required)
- [Deep RL Bootcamp 2017](https://www.youtube.com/playlist?list=PLAdk-EyP1ND8MqJEJnSvaoUShrAWYe51U)
