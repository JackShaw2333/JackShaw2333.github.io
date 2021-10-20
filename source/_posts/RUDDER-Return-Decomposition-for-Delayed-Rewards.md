---
title: 'RUDDER: Return Decomposition for Delayed Rewards'
date: 2019-12-02 13:08:20
categories: [AI, RL]
tags:
- RL
- AI
- paper
---

This article consists of notes taken of this paper [`RUDDER: Return Decomposition for Delayed Rewards`](https://arxiv.org/abs/1806.07857), [comments on OpenReview](https://openreview.net/forum?id=ryeUtP5Il7)

# Abstract

`RUDDER(RetUrn Decomposition for DElayed Rewards)`是作者提出的全新RL方法，针对`delayed rewards in finite MDPs`，目标是使得未来奖励的期望为0，以此简化Q值的计算。

2个新概念：

1. `reward redistribution`，构造`return-equivalent MDPs`，返回能得到同样回报的与原MDP相同的最优决策但能使未来奖励和的期望为0
2. `return decomposition via contribution analysis`将RL问题转化为回归问题

# Introduction

TD learning中，对未来reward的计算存在bias，MC learning中则存在high variance。

本文目标是**构建一个MDP，其中expected future rewards为0**，这样Q值的计算简化为计算immediate rewards的期望

# Reward Redistribution and Novel Learning Algorithms

> A sequence-Markov decision process (SDP) 被定义为有Markov策略和Markov转移概率但reward不需要有Markov性
> 2 SDPS P and ~P are `return-equivalent` if
> 1. 仅在reward distribution不同
> 2. 对于每个策略，在t=0时都有相同expected return
> 如果对每个策略，每个episode都有相同expected return，则为`strictly return-equivalent`
> return-equivalent的SDP有同样的最优策略
