---
title: >-
  Silent Bugs in Deep Learning Frameworks: An Empirical study of Keras and
  TensorFlow
mathjax: true
date: 2022-01-15 14:27:28
tags:
---

## 0. Intro

https://arxiv.org/abs/2112.13314

<!--more-->

## 1. Abstract

> Silent bugs lead to wrong behavior but do not cause system crashes or hangs, nor show an error message.

However, little information can be obtained from the DL model’s internal mechanism and training/inference due to the “black-box” and stochastic nature of DL training/inference.

RQs

- impacts and consequences
- from which part of the framework's API
- to what extent are the considered silent bugs relevant for DL developers

---

- 77 bug reports from 1168 closed issues of Keras/TensorFlow
- 7 categories, 4 levels of bug impact
- 14 components are responsible
- an online survey with 103 DL developers
- suggest a set of guidelines
- make datasets used publicly available

## 2. Background

## 3. Silent bugs

### 3.0. Methodology

#### 3.0.0. Data collection

#### 3.0.1. Manual inspection

- 4 inclusion criteria
- 3 reviewers

#### 3.0.2. Labeling and classification

4 threat/impact levels:

- UI element
- model's info
- model's operation
- model's result

Labeling process is performed in 3 rounds.

### 3.1. Empirical results

#### 3.1.0. RQ1: Impact of bugs

- wrong shape
- wrong displayed message
- wrong save/load
- wrong parameter setting
- performance degradation
- wrong structure
- wrong calculation

#### 3.1.1. RQ2: Faulty components

All components:

- layer
- regularization
- callbacks
- optimizer
- activations
- metrics
- model
- estimator
- loss
- TF
- saving
- engine
- wrapper
- backend

The most impacted components are engine (19 samples), layer (14 samples) and model (13 samples)

## 4. Validation and relevance assessment

### 4.0. Methodology

### 4.1. Validation results

#### 4.1.0. Notion of silent bugs

- 56.3% are familiar with **silent bugs**
- 97.1% find **silent bug** meaningful

#### 4.1.1. Categorization of scenario

- wrong structure rareset, 28%
- performance degradation most, 55%

#### 4.1.2. Categorization of impact

- 75.8% agree that this scale is relevant

#### 4.1.3. Comparison with traditional bugs

- 72.8% find silent bugs more problematic

## 5. Potential triage of silent bugs

Some recommendations that can help in tracking and/or preventing silent bugs:

- reveal as much infomation as possible
  - use assertions or return more info, unit test and benchmark work badly
- do not blindly trust the framework
- following a proper development cycle
- redundancy is a must
  - regression testing: using multiple versions of the framework
  - N-versioning: running multiple semantically equivalent programs
