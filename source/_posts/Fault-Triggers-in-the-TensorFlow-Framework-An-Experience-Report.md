---
title: 'Fault Triggers in the TensorFlow Framework: An Experience Report'
mathjax: true
date: 2022-01-18 22:34:15
tags:
---

## 0. Intro

doi: 10.1109/ISSRE5003.2020.00010

<!--more-->

## 1. Abstract

- 2285 bug reports from TF's Github are collected
- A bug classification is performed based on fault triggering conditions, followed by the frequency distribution of different types of bugs and the evolution features of varying bug types over time
- Relationships between bug types and fixing time are investigated
- Root causes of Bohrbugs and Mandelbugs are studied
- 5 root causes are discovered
- Analysis of regression bug is conducted
- 8 implications

## 2. Introduction

Fault triggers not only including the timing of inputs and operations, but also involving the interactions with other systems.

[*Grottke* and *Trivedi*][GT] divided bugs into 2 categories:

- Bohrbug (BOH) is easy to be isolated and their manifestation is consistent under a well-defined set of conditions
- Mandelbug (MAN) is a special kind of faults, whose activation and/or error propagation is complex and difficult to be discovered by traditional techniques
  - non-aging related Mandelbug (NAM)
  - aging related Mandelbug (ARB) is a kind of bug that can lead to the software aging phenomenon
- Regression bug is a type of bug that causes a feature, which worked normally in previous versions bug stopped working after a certain code commit

---

RQs:

- what is the distribution of different bug types in TF
- how much time is spent on fixing different types of bugs
- what are the root causes of Bohrbugs and Mandelbugs

## 3. Study methodology

### 3.0. Research data

- report filtering
- retracting bug reports and related info

### 3.1. Bug classification based on fault triggering conditions

- BOH
- MAN
  - NAM
    - LAG, there exits a time lag between fault activation and the occurrence of its failure
    - ENV, interaction of the software with its system-internal env would affect the activation of the bug and/or error propagation
    - TIM, timing of inputs and operations has impacts on the fault activation and/or error propagation
    - SEQ, the sequence (i.e., the relative order) of inputs and operations would influence the fault activation and/or the propagation of error
  - ARB
    - MEM, caused by the accumulation of errors because of improper memory management
    - STO, caused by the accumulation of errors as a result of improper storage space management
    - LOG, caused by the leaks of other logical resources
    - NUM, caused by the accumulation of numerical errors
    - TOT, caused by the increase of the fault activation or error propagation rate with total system runtime, but it is not induced by the accumulation of internal error states

5 root causes:

- environment and configuration
- concurrency
- memory
- compatibility
- semantic

---

Bug report classification procedure

- actual bug filtering
- classification based on fault triggering conditions

## 4. Bug classification

- Finding 1. 2285 bugs, 41.17% reports contains an actual bug, 44.86% do not contain bugs, 13.44% are invalid bug reports
  - invalid reports and non-bugs account for a large proportion of all classified bug reports
- Finding 2. 953 bugs, BOH (78.17%), MAN (16.47%), UNK (5.35%)
  - testing of BOH should be the focus of TF testing
- Finding 3. major subtype of ARBs is MEM (84.09%)
  - pay attention to the release and consumption of resources
  - provide guidelines for users to configure the parameters and structure of their model to avoid insufficient memory problems
- Finding 4. major subtype of NAMs are TIM (46.38%) and ENV (31.88%)
  - testing NAMs should focus on TIM and ENV. For TIM, use pairwise testing to expose concurrency bugs; For ENV, focus on fault-tolerance techniques
- Finding 5. in the first 2 years, proportion of BOH decrease and increase in the following 2 years; MAN shows the opposite
- Finding 6. it takes more time to close an invalid bug report than close an actual bug
  - users should provide high-quality bug reports
- Finding 7. in TF, it tends to take more time to fix a Mandelbug than a Bohrbug
  - specific strategies should be developed to deal with Mandelbugs, mitigation approaches such as fault tolerance and software rejuvenation could be used

## 5. Root causes

- Finding 8. 98.93% of BOHs are caused by semantic bugs, 84.09% of ARBs are caused by memory bugs, and 39.13% of NAMs are caused by concurrency bugs
- Finding 9. A NAM is prone to be an environment/configuration bug or a concurrency bug; an ARB is more likely to be a memory bug; a BOH is more likely to be a compatibility bug or a semantic bug

## 6. Regression bugs in TF

- Finding 10. 86 out of 953 bugs are regression bugs. In all regression bugs, BOHs, MANs and UNKs account for 77.91%, 15.12%, and 6.98%
  - developers implement more regression testing before releasing a new TF version

[GT]: https://grottke.de/documents/AClassificationOfSWFaults.pdf "pdf"