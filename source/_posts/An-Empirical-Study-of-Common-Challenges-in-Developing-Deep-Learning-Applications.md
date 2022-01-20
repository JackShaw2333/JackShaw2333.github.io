---
title: >-
  An Empirical Study of Common Challenges in Developing Deep Learning
  Applications
mathjax: true
date: 2022-01-20 14:19:42
tags:
---

## 0. Intro

doi: 10.1109/ISSRE.2019.00020

A study on DL applications instead of frameworks/libraries.

<!--more-->

## 1. Abstract

A large-scale empirical study of DL questions in StackOverflow. 715 questions are inspected and 7 kinds of frequently asked questions are identified, among which program crashes, model migration, and implementation questions are the top 3.

5 root causes:

- API misuse
- incorrect hyperparameter selection
- GPU computation
- static graph construction
- limitted debugging and profiling support

Highlight the need for new techniques such as cross-framework differential testing.

## 2. Introduction

## 3. Study methodology

### 3.0. RQs

- what kinds of questions are frequently asked in DL
- which kinds of DL questions are hard to resolve
- what are the main root causes

### 3.1. Data collection

SO questions about 3 frameworks, TF, PyTorch, and Deeplearning4j.

### Manual inspection

### Automated classification

## Analysis of the results

### RQ1

- implementation
  - distributed training
- program crash
  - shape inconsistency
    - cannot be simply detected via static type checking, it requires customized dataflow analysis accounting for both layer connectivity and operations that transform tensor shapes
  - numerical errors
  - CPU/GPU incompatibility
- training anomaly
- model migration and deployment
- performance
- comprehension
- installation

### RQ2

### RQ3

## Research opportunities

implications:

- mining implicit API usage protocols and hyperparameter combinations is needed
- facilitating debugging and profiling is needed
- cross-framework and cross-platform differential testing is needed
