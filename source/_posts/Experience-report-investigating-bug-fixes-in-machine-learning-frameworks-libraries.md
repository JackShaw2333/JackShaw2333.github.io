---
title: >-
  Experience report: investigating bug fixes in machine learning
  frameworks/libraries
mathjax: true
date: 2022-01-03 19:58:22
tags:
---

## 0. Infomation

https://doi.org/10.1007/s11704-020-9441-1 from Frontiers of Computer Science 21.

<!--more-->

## 1. Abstract

- 7 types of bugs;
- 12 fixing patterns;
- about 2/3 of patches are small-scale and can be done in a month;
- about 50% bug fixes are corrective activitiy
- questionnaire survey

## 2. Introduction

The following 4 types

- compatibility bugs
- variable bugs
- doc bugs
- design defect bugs

occur most.

## 3. Empirical study

### 3.0. RQs

- RQ1: the categories of bugs?
- RQ2: common fixing patterns?
- RQ3: fixing duration?
- RQ4: fixing scale?
- RQ5: common types of software maintenance activities?

### 3.1. Data collection

5 ML projects (Scikit-learn, Paddle, Caffe, SciPy, TensorFlow) from GitHub:

- Scikit-learn: 269
- Paddle: 131
- Caffe: 101
- SciPy: 179
- TensorFlow: 259

939 bugs in total.

### 3.2. Methodology

2 authors 1) categorized them and 2) checked each others' categories

## 4. Empirical results

### 4.0. RQ1: bug category

Based on occuring reasons.

- variable bug, 24.28%
  - variable transfer (VT), an error when data stream is passed
  - variable evaluatoin (VA)
  - variable constraint (VC), when constraints are ignored or are set incorrectly; leads to low performance learners; takes many efforts
  - data initialization (DI), variable error in value assignment
- design defect, 22.26%, caused by coding errors or boundary errors
  - algorithm design error (ADE), incorrect design of ML algorithm
  - coding error (CE), inaccurate understanding of ML algorithm
  - boundary error (BE), users do not consider special situation
- compatibility bug, 21.51%
  - conflicts with the OS (COS), 39.60%
  - conflicts with algorithm model version (CAV), 20.30%
  - conflicts with hardware (CH), 16.34%
  - conflicts with platform software (CPS), 40.91% in Caffe
- doc bug, 11.08%
  - no-timely-update bug (NTU), occurs when updates frequently
  - API caller error bug (ACE), users use wrong APIs due to wrong doc
- performance optimization bug (PO)
- memory overflow bug (MO)

### 4.1. RQ2: fixing patterns

12 patterns,

- add test module
- add if module
- modify variable

are 3 most common ones.

---

- for variable bugs
  - add if module
  - modify variable
- for design defects and compatibility bugs
  - add if module
  - modify variable
  - add test module

are most common ones.

---

*if fixing pattern* has 2 kinds:

- add if module (AIM)
- change if condition (CIC)

*try fixing pattern* useful in bug localization.

---

some fixing patterns related to functionality:

- modify assertion module (MAM)
- add loop module (ALM)
- add test module (ATM)

---

some other fixing patterns:

- keep consistency between GPU and CPU (KCGC)
- modify content of different versions (MCV)

### 4.2. RQ3: bug fixing time

- short-term: 1 day - 1 week, 40.58%
- common-term: 1 week - 1 month, 26.20%
- long-term: > 1 month, 33.23%

many bugs are fixed by 1 or 2 developers.

memory overflow and compatibility bugs need a longer time to fix

### 4.3. RQ4: bug fixing scale

- micro-scale: 0-50 lines code change and 1 changed file, 34.08%
- small-scale: 0-50 lines code change and >1 changed files, 34.72%
- medium-scale: 50-200 lines, 19.38%
- large-scale: >200 lines, 11.82%

most patches are small and within 1 method

### 4.4. RQ5: maintenance types of ML issues

- corrective, 45.90%
- adaptive, 20.77%
- perfective, 31.63%
- preventive, 1.70%

### 4.5. Questionnaire survey

48 valid responses out of 1000 emails

some inconsistencies with empirical results
