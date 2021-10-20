---
title: 'SAVIOR: Towards Bug-Driven Hybrid Testing'
date: 2021-10-18 19:01:54
tags:
---

From 2020 S&P.

<!--more-->

## 1. Abstract

Despite of recent significant advancement, hybrid testing's code coverage-centric design is inefficient in vulnerability detection in that it a) blindly selects seeds, and b) quickly moves on after reaching a chunk without examining hidden defects.

SAVIOR, a bug-driven framework is proposed, prioritizing promising seeds and verifying all vulnerable spots along the path. By modeling faulty situations in SMT constraints, SAVIOR reasons the feasibility and generates test case as proofs.

SAVIOR outperforms mainstream techniques by about 40% faster in vulnerability detection and finds about 80 unique bugs.

## 2. Introduction

Fuzzing hardly dive into regions with complex conditions. Concolic execution frequently directs to branchs with many exec paths.

Typically, a hybrid approach runs fuzzer most until it make no progress. Then concolic execution re-run the seeds from fuzzing. Generally, it use concolic to guide fuzzer.

2 novel techniques in SAVIOR:

- bug-driven prioritization: before testing, SAVIOR analyzes the source code and statically labels the potential vulnerable spots. During dynamic testing, it prioritizes seeds reaching more labels. This technique make SAVIOR faster by about 40%.
- bug-guided verification: verifies the labeled vulnerability along the path traversed by the concolic executor.

## 3. Design

### 3.1. Core techniques

#### 3.1.1. Bug-driven prioritization

Need to predict the amount of vulnerabilities a seed can expose. Thus, 2 prerequisites are needed:

- R1: method to assess the reachable code region after a seed is run on. During compilation, the set of reachable regions from each branch is computed. At runtime, unexplored branches of a seed's execution path are identified, which are deemed as explorable regions.
- R2: metric to quantify the amount of vulnerabilities of a code chunk. UBSan is utilized to annotate potential bugs, and UBSan labels filtered statically after in each region is summed as R2.

#### 3.1.2. Bug-guided verification

### 3.2. System design

#### 3.2.1. Compilation tool-chain

##### 3.2.1.1. Sound vul labeling

SAVIOR can use other sanitizers as long as they satify the following 2 properties:

- able to annotate the vulnerabilities
- able to synthesize the triggering condition, which must have data dependency on input, of each labeled vul

The static filter in R2 trims a label meeting 3 requirements. As a result, on average about 5% labels are removed.

##### 3.2.1.2. Reachability analysis

Counts # of vul labels that can be forwardly reached by each BB in CFG. 2 phases:

- construct an inter-procedure CFG
- count UBSan labels reachable from each BB

##### 3.2.1.3. Target building

3 binaries are compiled:

- fuzzer
- coordinator
- concolic executor

#### 3.2.2. Coordinator

##### 3.2.2.1. Bug-driven prioritization

In a polling round, coordinator feeds each new seed from last round to SAVIOR-binary and updates 2 info:

- global coverage info
- the seq of BBs visited by each seed

With these info, coordinator assign scores for seeds. All seeds will be rescored except those already tested by the concolic executor.

Finally, top-ranked seeds are selected.

##### 3.2.2.2. Post-processing of concolic execution

coordinator triage new seeds by concolic executor for the fuzzer and:

1. rerun new seeds and retain those with new coverage or can reach uncovered labels. Transfer them to fuzzer
2. update # of attempts on uncovered branches.

#### 3.2.3. Concolic executor

replay seeds and chooses to solve branches based on coverage info. Also, it performs bug-guided verification.

##### 3.2.3.1. Independent coverage scheme

Use KLEE's internal coverage info.

##### 3.2.3.2. Fork Server mode

Because KLEE's initialization takes too long, thus an optim is introduced named *fork server*.

##### 3.2.3.3. Bug-guided verification

If the triggering constraint of a non-covered label is solved, KLEE generates a seed as the proof of the vul.

If path constraints conflict with vul triggering conditions, QSYM's approach is adopted. But relaxed-constraints may produce FP, in which situation a vul labeled is not deemed as covered.

##### 3.2.3.4. Timeout on concolic execution

Timeout threshold of a seed is set to be proportional to the # of uncovered branches it can reach.
