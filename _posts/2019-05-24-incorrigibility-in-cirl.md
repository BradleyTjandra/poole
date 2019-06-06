---
layout: post
title: Incorrigbility in CIRL
date: 2019-04-24 05:00:00 +1000
---

# Incorribility in CIRL

There are my notes from reading the paper *Incorrigibility in CIRL*, and from the subsequent discussion.

> [Link to paper](https://arxiv.org/pdf/1709.06275.pdf)

<!-- MarkdownTOC -->

- [Introduction](#introduction)
- [The paper](#the-paper)
    - [Utility Indifference](#utility-indifference)
- [Discussion](#discussion)
    - [Utility Indifference](#utility-indifference-1)
    - [Off-Switch as Information](#off-switch-as-information)

<!-- /MarkdownTOC -->

 
## Introduction

**CIRL** is a type of machine learning problem where

- The AI is trying to learn which actions will give it the highest reward (RL)
- The AI does not know what the reward function is and instead looks to the human's actions to imply what the latter deems valauble (IRL)
- The AI does not assume the human acts optimally; the AI can imply the human's reward function from other behaviours i.e. "active teaching, active learning and communicative actions" meant to best inform the AI what the human wants to achieve (CIRL)

## The paper

However, this requires the AI to have some probability distribution of the human's reward function. If this posterior is correct, then CIRL will lead to non-negative behaviour when the AI deviates from human-recommended actions. However, this is a big assumption! _Incorrigibility in CIRL_ demonstrates that if the AI has an incorrect posterior, it may lead to sub-optimal behaviour.

The paper advocates for a system which

1. Has a simple, easy-to-verify module which automatically shutsdown when issued a shutdown command
2. Has no incentive to remove, damage or ignore its shutdown module
3. Has a small incentive to keep its shutdown module around

Even under a "broad range" of incorrect reward functions, priors over the human reward parameters, which actions are available, "etc.".

To do this, Carey shows that:

- The **AI with an incorrect reward function posterior** achieves **sub-optimal results and ignore the human's suggested action**, because it thinks the latter is acting sub-optimally
- The same **AI with a shutdown button** may **ignore a human's instruction to shutdown** for the same reason
- The same **AI with a module which automatically shutsdown** the AI when issued the shutdown command is **optimal**
- But the same **AI with the ability to remove this shutdown module** will elect to do so, so long as the penalty is not too large
- An **AI who is incentivised too heavily to shutdown** will always do so, even if it's not optimal for the human
- An **AI with "utility indifference"** and a small penalty for removing its shutdown module will **act optimally**
- But an **AI with "utility indifference" who can gain a small reward for removing its shutdown module** will not. Carey points out this may more often be the case, where removing the shutdown module may allow for a performance boost because the physical or computational resources of the shutdown module could be used more effectively otherwise

### Utility Indifference

This is a suggestion from Stuart Armstrong that the reward from shutting down should be equal to the expected reward of not shutting down.

This makes the AI _indifferent_ to taking the shutdown command and not, and hence when the human suggests that it shutdown, it will be willing to.

There are two ways to determine this:

1. At the beginning of the episode, for every attainable, non-terminal state, calculate the expected reward of not shuttng down once you are at that state
2. Dynamically calculate the expected reward of not shutting down

The issue with the first is that is that the AI may still want to remove its shutdown module if it can gain a small performance boost as discussed in the previous section.

The second method means the AI will be indifferent between shutting down and removing its shutdown module. However, this continuous "re-balancing" of the shutdown button's reward makes the AI a kind of Buridan's ass, forever asking the human whether it should shutdown in order to resolve tie breaks.

## Discussion

At our AI Meetup, we discussed two points:

1. Using utility indifference to disincentivise removing the off-switch
2. Using the off-switch as information which will indicate to the AI that it should turn off

### Utility Indifference

In the first case, we proposed making the reward associated with removing the shutdown module equal to $-R -0.01$ where $R$ is the expected reward after the shutdown module is removed. In this case, the AI is disincentivised to remove its shutdown module, because any reward it could get is offset by the cost of removing the module.

Of course, it is not simply to know flag in advance all actions which would lead to the off-switch being removed. Obvious ones are things such as causing direct physical contact with the module.

### Off-Switch as Information

In the second case, we discussed whether an AI would always ignore a human's instruction to shutdown in the second scenario (see the above bullet points). We agreed that, if the robot has some type of unbound distribution on its estimate of the reward and believes that human is rational $1-\epsilon$ of the time (for $\epsilon \neq 1$), then with sufficient shutdown commands from the human, it would update on its reward function.

We showed this held in a simple case of assuming there was Gaussian uncertainty around the AI's reward estimate, even though the mean of the AI's estimates was always positive (that is, it expects not shutting off will yield positive value). That is, after $n$ shutdown commands, the expected value of not shutting down is estimated at:

$$
\mathbb E(R | o_{1:n} = a_{SD}) = \mu - (1 - \epsilon^n)\sigma \frac{\phi(-\mu/\sigma)}{\Phi(-\mu/\sigma)}
$$

We see the automatic shutdown condition as a special case where $\epsilon=0$, where the above formula doesn't hold.

We discussed further that the ideal case would be to paramterise the uncertainty around the reward and the prior on human irrationality $\epsilon$ so that the AI would avoid removing its ability to receive shutdown commands, as receiving these commands provides information about the reward. We did not get the opportunity to discuss this further.