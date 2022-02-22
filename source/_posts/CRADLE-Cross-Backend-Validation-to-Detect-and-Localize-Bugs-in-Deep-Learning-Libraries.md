---
title: >-
  CRADLE: Cross-Backend Validation to Detect and Localize Bugs in Deep Learning
  Libraries
mathjax: true
date: 2022-02-19 21:02:08
tags:
---

## 0. Intro

https://doi.org/10.1109/ICSE.2019.00107

<!-- more -->

## 1. Approach

### 1.0. Overview

![overview](https://s4.ax1x.com/2022/02/19/HqM65d.png)

- detection phase uses pre-trained models and validation sets as input
- focus only on the inference stage because of the non-deterministic nature of DL training

### 1.1. Challenges

- How to determine if a model’s outputs with two backends are inconsistent?
  - always slightly different results
  - impossible to have a single threshold
  - **2 distance metrics**
- How to precisely localize the source of an inconsistency?
  - **a novel localization and visualization method by analyzing internal input and output and localizing the error spikes through the execution path**

### 1.2. Detection phase

- output extractor
  - input: pre-trained models and their validation sets
  - produces output using all 3 backends for each input
  - detects crashes
- output comparator
  - load stored outputs
  - do pair-wise comparison
  - group inconsistencies into unique ones
  - 2 metrics to compare a pair of backends
    - class-based distance, for classification
    - mean absolute deviation-based distance, for regression
  - identifying unique inconsistencies
    - same inconsistencies may exist in different Keras versions
    - group inconsistencies based on *inconsistencies pattern* to avoid duplicates

### 1.3. Localization phase

Given each unique inconsistency, *hidden states extractor* and *inconsistency localizer* produce a *localization map* (an execution deviation graph of 2 backends, highlighting inconsistent executions of a function and pointing to potential faulty functions in one of the backends)

- hidden states extractor
  - similar to output extractor
  - also retrieve the intermediate output (hidden states, presented as vectors of floating point numbers)
- inconsistency localizer
  - produce a localization map
    - first extract the execution deviation graphs
    - calculate the rate of change in deviation between consecutive function executions to localize the source of inconsistency
    - highlighting functions having inconsistent executions

---

(to be continued)

## 2. Datasets and experimental settings

## 3. Results

- RQ1, can CRADLE detect bugs and inconsistencies in DL backends?
- RQ2, can CRADLE localize the source of inconsistencies?
- RQ3, what is CRADLE's detection and localization time?

## 4. Limitations and threats to validity
