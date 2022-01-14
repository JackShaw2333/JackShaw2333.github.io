---
title: On Equivalence Checking of Nondeterministic Finite Automata (archived)
date: 2021-11-01 11:18:18
tags:
mathjax: true
---

## 0. Brief Info

CS244计算理论的一份阅读材料，来自Lecture Notes in Computer Science (including subseries Lecture Notes in Artificial Intelligence and Lecture Notes in Bioinformatics)

[DOI: 10.1007/978-3-319-69483-2_13][doi]

[Slides made by me (Google Drive)][slides] have more details than this post.

<!--more-->

This paper provides a comparative study of some typical algorithms for language equivalence in NFA and combinations of optimization techniques.

Their practical efficiency relies on the **density** and **alphabet size** of the NFA.

Recommendation:

- HKC, if density is large and alphabet is small.
- Antichain algorithms, otherwise.
- Bisimulation equivalence & memoisation pay off.
- Symbolic algorithm if comparing highly structured automata over a large alphabet.

---

## 1. Brief version

### 1.0. Recap

Checking if 2 NFAs accept the check whether 2 NFAs accept the same language is decidable, using $E_{DFA}$ and symmetric difference. [Textbook p197]

### 1.1. PSPACE-complete

### 1.2. Bisimulation

- For any NFA, if a bisimulation between 2 states exists, they are language equivalent.
- If a simulation exists, like $x \lesssim y$, then $L(x) \subseteq L(y)$

### 1.3. HK and HKC algorithm

### 1.4. Antichain algorithm

### 1.5. Experiments

---

## 2. Detailed version

### 2.0. Introduction

Checking if 2 NFAs accept the same language is PSPACE-complete.

> [**PSPACE**][pspace] is the set of problems that can be solved by a Turing machine using a polynomial amount of space.
>
> A problem is [**PSPACE-complete**][pspace-complete] if 1) it is a PSPACE problem and 2) every other PSPACE problem can be transformed to it in polynomial time.

If restricted to DFAs, it can be done using either **minimisation** or **HK algorithm**.

> HK algorithm
> coinduction

Converting NFAs to DFAs is a straightforward idea, but there are exponential states in the powerset.

#### 2.0.0. Approach 1

If we just want to decide whether 2 specific sets of states are equivalent, we can construct the states on-the-fly and simultaneously build a bisimulation to relate these 2 sets to avoid the exponential size of states.

> A [**bisimulation**][bisimulation] is a binary relation between state transition systems, associating systems that behave in the same way in that one system simulates the other and vice versa.

As for implementation, a **naive HK algorithm** from NFA to DFA can be adapted: 

- The algo maintains 2 sets $todo$ and $R$.
  - $todo$ contains pairs of NFA states sets $(X, Y)$ to be checked.
  - $R$ contains the checked (equivalent) pairs
- It is a bisimulation relation when the algo terminates successfully.

This algo has several optimizations:

- Use HK or HKC algo to reduce $R$.
- Use memoisation techniques to avoid repetitions in $todo$.
- Use *binary decision diagram (BDD)* to symbolically represent the automata.
- Saturate the given automata with respect to bisimulation equivalence or simulation preorder.

#### 2.0.1. Approach 2

Use antichain algo.

The basic idea is to check language inclusion in both directions. It also exploits *coinduction*: In order to check if the language of an NFA states set $X$ is a subset of that of an similar set $Y$, it simultaneously tries to build a simulation relation, relating each state $x \in X$ (as a state in the NFA) to one in $Y$ (as a state in the DFA).

This algo can be optimized by:

- Use memoisation techniques to avoid repetitions in $todo$.
- Use antichain to reduce the list $R$.

The antichain algo can be enhanced by exploiting any preorder contained in language inclusion. The simulation preorder can be used for this purpose.

#### 2.0.2. Summary

Antichain is stable. HK and HKC vary a lot, depending on the size of alphabet and the density of transitions in the automata.

### 2.1. Preliminaries

#### 2.1.0. Simulation and Bisimulation

Let $R, R' \subseteq Q \times Q$ be 2 binary relations on states, we say that $R$ s-progresses to $R'$, denoted $R \rightarrowtail_s R'$, if $xRy$ implies:

- if $x$ is accepting, then $y$ is accepting
- $\forall a \in \Sigma$ and $x' \in \delta(x, a)$, $\exists y' \in \delta(y, a)$, $x'R'y'$

A simulation is a relation $R$ such that $R_1 \rightarrowtail_s R_2$. A bisimulation is a relation $R$ such that $R_1 \rightarrowtail_s R_2$ and $R_1^{-1} \rightarrowtail_s R_2^{-1}$, where $R^{-1}$ is the inverse relation of $R$.

Two states $s$ and $t$ are called **bisimilar** iff there is a bisimulation $R$ such that $sRt$. Two transition systems are **bisimilar** iff their initial states are bisimilar. The largest simulation and bisimulation are called **similarity** and **bisimilarity**, denoted by $\lesssim$ and $\sim$.

- For any NFA, if a **bisimulation** between 2 states can be found, they are **language equivalent**.
- For any NFA, if a **simulation** between 2 states can be found, e.g., $x \lesssim y$, then $L(x) \subseteq L(y)$.

For any NFA, if a bisimulation between 2 states can be found, they are language equivalent. If state $x \lesssim y$, then $L(x) \subseteq L(y)$. Computing similarity needs $O(|\delta| \cdot |Q|)$ time, while bisimilarity needs $O(|\delta| \cdot \log |Q|)$ time.

Bisimulation is a **sound** proof technique for checking language equivalence of NFA, and it's also **complete** for DFA. Simulation is a sound proof technique for checking language inclusion of NFA, and it's also complete for DFA.

#### 2.1.1. BDD

BDD is a standard technique to represent automata over a large alphabet. A BDD over a set of variables $X_n$ is a directed acyclic graph having leaf nodes and internal nodes. There is only one root node in a BDD. Each internal node is labelled with a variable and has 2 outgoing edges. Leaf nodes are labelled with 0 or 1. After fixing the order of variables, any BDD can be transformed into a reduced one with the fewest nodes.

### 2.2. Algorithms and Optimizations

#### 2.2.0. A Naive Algorithm for Language Equivalence checking

#### 2.2.1. Reduing $R$

[doi]: https://doi.org/10.1007/978-3-319-69483-2_13
[pspace]: https://en.wikipedia.org/wiki/PSPACE 'wikipedia'
[pspace-complete]: https://en.wikipedia.org/wiki/PSPACE-complete 'wikipedia'
[bisimulation]: https://en.wikipedia.org/wiki/Bisimulation 'wikipedia'
[slides]: https://drive.google.com/file/d/10DX1bm--yhuB1kTZseUyTdTcp641d7f0/view?usp=sharing 'google drive'
