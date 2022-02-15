---
title: 'Free Lunch for Testing: Fuzzing Deep-Learning Libraries from Open Source'
mathjax: true
date: 2022-01-22 21:24:15
tags:
---

## 0. Intro

doi: 10.1145/3510003.3510041

arxiv: 2201.06589

<!--more-->

## 1. Approach

4 stages:

- code collection
- instrumentation
- mutation
- oracle

### 1.0. Code collection

3 sources:

- code snippets from library doc
- library developer tests
- DL models in the wild

### 1.1. Instrumentation

To collect dynamic info including type and value for each parameter.

- **customized type space**, by a customized type monitoring system `FuzzType`
- **API value space**, where each entry stands for an API invocation with its corresponding list of concrete arguments, which is later used in mutation phase as the starting point to generate more mutants
- **argument value space**, composed of different argument names and types with their values recorded when invoking different APIs

### 1.2. Mutation

- mutation rules
  - type mutation
    - tensor dim mutation
    - tensor dtype mutation
    - primitive mutation
    - tuple mutation
    - list mutation
  - value mutation
    - random value mutation
      - random tensor shape
      - random tensor value
      - random primitive
      - random tuple
      - random list
    - database value mutation
      - database tensor shape
      - database tensor value
      - database primitive
      - database tuple
      - database list
- algorithm
  - randomly select some arguments in the API to check and randomly use mutation rules, then run it

### 1.3. Test oracles

- **wrong-computation bugs**, differential testing
- **performance bugs**, metamorphic testing, i.e., the more precision info a tensor carries, the slower the execution will be

## 2. Experimental setup

- RQ1, how do the 3 different input sources perform?
- RQ2, how do different numbers of mutations perform?
- RQ3, how do different mutation strategies impact performance?
- RQ4, how does FreeFuzz compare with existing work?
- RQ5, can FreeFuzz detect real-world bugs?

### 2.0. Metrics

- **number of covered APIs**, test adequacy
- **size of value space**, analyze and compare input sources, show the scale of the traced data, not necessarily indicate fuzzing effectiveness
- **line coverage**, test adequacy

## 3. Result analysis

### 3.0. RQ1, input source study

- all tend to help
- settings covering more APIs tend to also achieve higher code coverage
- though each source exclusively covers different code portions, the majority of covered code tends to be shared by all 3 sources

### 3.1. RQ2, coverage trend analysis

- 600 mutations per API is cost-effective

### 3.2. RQ3, different mutation strategies

- type mutations can be more effective

### 3.3. RQ4, comparison with prior work

- without mutations, the input sources used by FreeFuzz can achieve higher API and code coverage than LEMON and CRADLE
- more coverage and efficient than LEMON and CRADLE

### 3.4. RQ5, bugs detected

- each mutation strategy can help detect certain bugs that other mutation strategies fail to detect

### 3.5. Threats to validity

- about **internal** validity, threats lie in the correctness of the implementation of FreeFuzz and the compared techniques
- about **external** validity, threats lie in the randomness of the benchmarks used
- about **construct** validity, threats lie in the metrics used

## 4. Discussion and future works

- **generalizability and specificity**
- **validity of test inputs**, even invalid inputs help detect various bugs
- **future work**
  - compared with model-level testing, API-level testing may miss bugs which can only be triggered by invoking a sequence of APIs. Future works can try to detect bugs which can be triggered by a sequence of APIs
  - fix flaky test problem
