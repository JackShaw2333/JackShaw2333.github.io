---
title: An Empirical Study on Bugs inside TensorFlow
mathjax: true
date: 2022-01-18 10:49:41
tags:
---
## 0. Intro

doi: 10.1007/978-3-030-59410-7_40

<!--more-->

## 1. Abstract

The 1st empirical study to analyze the bugs inside a typical deep learning library, i.e., TensorFlow.

The symptoms and root causes of are more likely ordinary projects.

TensorFlow bugs resides in

- interfaces, 26.24%
- learning algorithm, 11.79%
- how to compile, 8.02%
- deploy, 7.55%
- install, 4.72%

## 2. Introduction

RQs

- what are the symptoms and causes of bugs in TensorFlow
- where are the bugs inside TensorFlow

## 3. Methodology

### 3.0. Dataset

Analyzed 202 bugs from Dec 2017 to Mar 2019, 84 of them have bug reports.

Steps to extract bugs:

- filtering pull requests by labels
- searching pull requests by keywords
- extract bug reports and code changes

### 3.1. Manual analysis

#### 3.1.0. Protocol of RQ1

A *lift* function to measure the correlations between symptoms and root causes

$$lift(A, b) = \frac{P(A \cap B)}{P(A)P(B)}$$

If a *lift* value is greater than 1, A and B are correlated.

#### 3.1.1. Protocol of RQ2

Identified TF components:

- kernel
- computation graph
- API
- runtime
- framework
- tool
- platform
- contribution
- library
- doc

## 4. Empirical results

### 4.0. RQ1 symptoms and root causes

Symptoms

- functional error, 35.64%
- crash, 26.73%
- hang, 1.49%
- performance degradation, 1.49%
- build failure, 23.76%
- warning-style error, 10.89%

Root causes

- dimension mismatch, 3.96%
- type confusion, 12.38%
- processing, 22.28%
- inconsistency, 16.83%
- algorithm, 2.97%
- corner case, 15.35%
- logic error, 9.90%
- configuration error, 7.43%
- referenced types error, 4.95%
- memory, 2.97%
- concurrency, 0.99%

Distribution

- Finding 1. compared to symptoms, root causes are more determinative, since several root causes have dominated symptoms
- Finding 2. the symptoms and causes of TF are more like an ordinary software system than a maching learning system
- Finding 3. for symptom of TF bugs, build failures have correlation with inconsistencies, configurations and referenced type errors, and warning-style bugs have correlation with inconsistencies, processing, and type confusions. For the root causes of TF bugs, dimension mismatches lead to functional errors, and type confusions have correlation with functional errors, crashes, and warning-style errors

### 4.1. RQ2 bug locations

Distribution

- contribution, 34.91%
- kernel, 11.79%
- API, 26.42%
- library, 8.02%
- platform, 7.55%
- tool, 4.72%
- computation graph, 0.94%
- Finding 4. In TF, the major reported bugs are in deep learning algorithms and their interfaces, and the bugs in compiling, deploying, and installing TF on different platforms occupy a smaller proporation
