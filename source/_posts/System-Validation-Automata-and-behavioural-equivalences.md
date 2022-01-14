---
title: 'System Validation: Automata and behavioural equivalences'
date: 2021-11-22 21:00:35
tags:
mathjax: true
---

## 0. Intro

Notes of the course [System Validation: Automata and behavioural equivalences][course-site] from Coursera.

<!--more-->

The notation used here follows that in Sipser's *Introduction to the Theory of Computation* instead of that in this course.

## 1. Behavioural Modelling

Basic knowledge about **finite automata**.

## 2. Basic Behavioural Equivalences

### 2.0. Concrete Behavioural Equivalences

#### 2.0.0. Strong Bisimulation

**Definition.** Let ($Q$, $\Sigma$, $\delta$, $q_0$, $F$) be a labelled transition system. We say that a relation $R \subseteq S \times S$ is a **bisimulation relation** iff $\forall s, t \in Q$, if $sRt$:

- if $\exists s' \in Q, \delta(s, a) = s' \implies \exists t' \in Q, \delta(t, a) = t' \land s'Rt'$
- if $\exists t' \in Q, \delta(t, a) = t' \implies \exists s' \in Q, \delta(s, a) = s' \land s'Rt'$
- $s \in F \iff t \in F$

Two states $s, t \in Q$ are called **bisimilar**, $s \leftrightarrow t$, iff there is a bisimulation relation $R$ such that $sRt$. Two transition systems are **bisimilar** iff their initial states are bisimilar.

**Theorem.** Every transition system has a **unique** minimal transition system that is bisimulation equivalent to it.

#### 2.0.1. Trace Equivalence

**Trace** is a sequence of actions that can be taken from the initial state, including empty action $\epsilon$, and *terminate* is an individual action.

**Definition.** Two transition systems are **trace equivalent** if they have the same set of traces.

Trace equivalence has some nasty properties:

- It does not preserve deadlocks.
- Calculating bisimulation on a finite transition system with $n$ states and $m$ transitions requires $O(m \log n)$ time, but trace equivalence is PSPACE complete.

Some facts:

- If 2 states are bisimilar, they are trace equivalent.
- For DFA, bisimulation and trace equivalence coincide.

### 2.1. The Internal Action

#### 2.1.0. $\tau$ action

**Definition.** The internal action or hidden action $\tau$ represents an action that can happen but is unobservable directly.

Some internal actions can be removed without altering the behavior of a system.

Hidden actions is an important tool to get insight in complex behavior.

#### 2.1.1. Branching Bisimulation

**Definition.** Let ($Q$, $\Sigma$, $\delta$, $q_0$, $F$) be a labelled transition system. We say that a relation $R \subseteq S \times S$ is a **branching bisimulation relation** iff it is symmetric and $\forall s, t \in Q$, if $sRt$:

- if $\exists s' \in Q, \delta(s, a) = s'$, then either 1) $a = \tau$ and $s'Rt$, or 2) $\exists t', t'', t \xrightarrow{\tau}^* t' \xrightarrow{a} t''$, $sRt'$ and $s'Rt''$.
- if $s \in F$, then $\exists t' \in F, t \xrightarrow{\tau}^* t'$ and $sRt'$.

Two states $s, t \in Q$ are called **branching bisimilar**, $s \leftrightarrow_b t$, iff there is a branching bisimulation relation $R$ such that $sRt$. Two transition systems are **branching bisimilar** iff their initial states are branching bisimilar.

#### 2.1.2. Rooted Branching Bisimulation

Branching bisimulation is not preserved when combining behaviors. We can define **rooted branching bisimulation** to solve this problem.

**Definition.** Let ($Q$, $\Sigma$, $\delta$, $q_0$, $F$) be a labelled transition system. We say that a relation $R \subseteq S \times S$ is a **rooted branching bisimulation relation** iff it is branching bisimulation relation and $\forall s, t \in Q$, if $sRt, \exists s' \in Q, \delta(s, a) = s' \implies \exists t' \in Q, \delta(t, a) = t' \land s' \leftrightarrow_b t'$

Two states $s, t \in Q$ are called **rooted branching bisimular**, $s \leftrightarrow_{rb} t$, iff there is a rooted branching bisimulation relation $R$ such that $sRt$. Two transition systems are **rooted branching bisimilar** iff their initial states are rooted branching bisimilar.

##### 2.1.2.0. Useful Facts

Strong bisimulation $\implies$ rooted branching bisimulation $\implies$ branching bisimulation

## 3. More Behavioural Equivalences

### 3.0. Various Other Behavioural Equivalences

#### 3.0.0. Divergence Preserving Branching Bisimulation

A state from which an infinite number of $\tau$s can be done is called **divergent**.

Obviously, branching bisimulation eliminates divergencies. All states on a $\tau$-loop are branching bisimilar.

To narrow bisimulation relation, we can define **divergence preserving branching bisimulation**, which requires same number of $\tau$ actions for states $s, t$, where $sRt$.

If divergent behaviour is important, divergence preserving branching bisimulation must be used.

#### 3.0.1. Weak Trace Equivalence

**Definition.** Two transition systems are **weak trace equivalent** iff they have the same set of traces where $\tau$s are omitted.

#### 3.0.2. Weak Bisimulation

**Definition.** Let ($Q$, $\Sigma$, $\delta$, $q_0$, $F$) be a labelled transition system. We say that a relation $R \subseteq S \times S$ is a **weak bisimulation relation** iff it is symmetric and $\forall s, t \in Q$, if $sRt$:

- if $\exists s' \in Q, \delta(s, a) = s'$, then either 1) $a = \tau$ and $s'Rt$, or 2) $\exists t_1, t_2, t_3, t \xrightarrow{\tau}^* t_1 \xrightarrow{a} t_2 \xrightarrow{\tau}^* t_3$, $s'Rt_3$.
- if $s \in F$, then $\exists t' \in F, t \xrightarrow{\tau}^* t'$ and $sRt'$.

For all practical purposes weak bisimulation and branching bisimulation are equivalent. Branching bisimulation is computationally more efficient than weak bisimulation.

#### 3.0.3. Language, Failure, and Completed Trace Equivalence

##### 3.0.3.0. Completed Trace Equivalence

**Definition.** A **completed trace** is a trace ending in a deadlock (where no valid action can be taken) or a terminating state.

Two transition systems who are completed trace equivalent can be not equivalent anymore if some operations are done to these 2 systems, e.g., blocking a certain action.

Thus, a new equivalence, **failures equivalence** is proposed.

##### 3.0.3.1. Failures Equivalences

**Definition.** A **refusal set** is a set of actions that cannot be done in a state.

**Definition.** A **failure pair** is a pair of a trace and a refusal set where the trace ends.

*To be continued.*

[course-site]: https://www.coursera.org/learn/automata-system-validation
