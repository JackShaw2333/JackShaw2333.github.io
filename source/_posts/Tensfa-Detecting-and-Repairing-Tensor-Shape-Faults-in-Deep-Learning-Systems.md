---
title: 'Tensfa: Detecting and Repairing Tensor Shape Faults in Deep Learning Systems'
mathjax: true
date: 2022-01-14 20:16:27
tags:
---

## 0. Intro

doi: 10.1109/ISSRE52982.2021.00014

<!--more-->

## 1. Abstract

- empirical study on **crashing tensor shape faults**
- propose and implement Tensfa to detect and repair crashing tensor shape faults
  - a ML method to learn and decision trees to detect
  - 1st automated solution to repairing
  - construct **SFData**, a set of 146 buggy programs
  - f1-score 95.88% in detection
  - 80/146 in repair

## 2. Introduction

computational graph:

- node - operation (OP)
- tensor - an edge of the graph

---

2 solutions:

- static shape fault detection
- dynamic shape fault detection

---

challenges:

- python's dynamic nature make static analysis hard
- many OPs are defined in frameworks/libraries, each may has trivial restrictions, which is hard to define detection rules
- a dataset for evaluation and repairing

## 3. An empirical study

### 3.0. Preliminaries

definitions:

- operation
- shape restriction
- tensor shape fault
- crashing tensor shape fault
- plausible/correct patch

### 3.1. Dataset

from stackoverflow

- extracting info from posts
- reusing code in github
- generating random test
- producing crash messages

### 3.2. Shape fault analysis

- construction
  - PRI, parameter restriction incompatible, OP parameter shapes and OP restriction are incompatible
  - ALI, adjacent layer incompatible, shapes of adjacent layers are incompatible
- execution, 65.82%
  - FII, feature input incompatible, feature shapes and model input shapes are incompatible
  - LOI, label output incompatible, label shapes and model output shapes are incompatible

The better the APIs are encapsulated, the less possible programs suffer from shape faults during construction.

### 3.3. Analysis of fault detection capability

- rule-based approaches suffers from generality problem
- crashing messages from shape faults are similar, making ML methods promising

## 4. Approach

