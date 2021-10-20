---
title: Static Analysis Lectures - Mayur Naik
date: 2020-11-28 16:38:49
tags:
---

## Intro

This is notes of Mayur Naik's youtube courses [`Software Analysis Lectures (University of Pennsylvania)`](https://www.youtube.com/playlist?list=PLF3-CvSRq2SYXEiS80KuZQ80q8K2aHLQX)

[Mayur Naik's youtube channel](https://www.youtube.com/channel/UCvwqRhlkE_Wm2FF9qzvHfJw)

<!--more-->

## Module 1: Introduction to Software Analysis

Parts of static analysis

- program representation
- abstract domain
- transfer functions
- fixed-point computation algorithm

### sound & complete

- sound: never accept bad program (may reject good program), false positive
- complete: never reject good program (may accept bad program), false negative

for trival analysis:

- reject all programs, it is sound
- accept all programs, it is complete

|               | dynamic                  | static                         |
|---------------|--------------------------|--------------------------------|
| cost          | proportional to run-time | proportional to program's size |
| effectiveness | unsound                  | incomplete                     |

### precision & recall

|      | Accept         | Reject         |
|------|----------------|----------------|
| Good | True Negative  | False Positive |
| Bad  | False Negative | True Positive  |

$$precision = \frac{\# True Positives}{\# Rejected}$$
$$recall = \frac{\# True Positives}{\# Bad}$$

precision衡量判断正确的比例；recall衡量正确找出所有positive的比例

$$F-measure = \frac{2}{\frac{1}{precision} + \frac{1}{recall}} \in [0, 1]$$

## Module 2: Software Specifications

### Key observations

- specifications must be explict
- independent development and testing
- resources are finite
- specifications evolve over time

### Landscape of testing approaches

### classification of specifications

- safety properties
- liveness properties

### pre- & post-conditions

- pre-conditions must be true before a statement or method
- post-conditions must be true after a statement or method is completed

pre- & post-conditions generalize the notion of assertion.

#### Invariants

- loop invariants
  - a property of a loop that hold before and after each iteration
- class invariants
  - a property that holds for all objects of a class
  - established upon the construction of the objects and constantly maintained between calls to public methods

specifications can be implemented as annotations in Java.

### Inferring specifications

编程人员往往不愿意在代码中编码和维护specifications，所以诞生了这样的想法：自动推断

#### Houdini algorithm

1个有名的guess & check的方法是`Houdini Algorithm`.

它可以自动infer pre-conditions, post-conditions, loop and class invariants。

`Houdini algorithm`的想法是generate many candidate invariants and employ ESC/Java to verify or refute each.

##### Workflow

[![workflow-of-houdini-algorithm](https://s3.ax1x.com/2020/11/29/DgnY26.png)](https://imgchr.com/i/DgnY26)

##### Pseudocode

$
\mathbf{Input}:\ An\ unannotated\ program\ \mathbf{P} \\
\mathbf{Output}:\ An\ annotated\ version\ of\ \mathbf{P} \\
\mathbf{Algorithm}: \\
Generate\ a\ set\ of\ canditate\ invariants\ and\ put\ it\ into\ \mathbf{P} \\
repeat \\
\qquad invoke\ the\ invariant\ checker\ to\ check\ \mathbf{P} \\
\qquad remove\ any\ refuted\ candidate\ invariants\ from\ \mathbf{P} \\
until\ no\ warnings\ remain
$

`Houdini algorithm`对每个`method`每轮只检查一次，与当前`method`不相关的`invariant`都暂时默认是正确的。

##### Pros & Cons

- pros
  - infer both loop invariants and method contracts
  - infer the strongest invariant in the candidate set
  - easy to implement
- cons
  - only infer conjunction invariants, for example, with candidates `a`, `b` and `c`: can infer `a && b && c`, but not `a || !b`
  - getting a high-quality invariant requires aggressively many invariants which makes refutation process more expensive
  - no guarantee inferred invariants useful for verifying desired property

### Measuring test suite quality

Coverage metrics & mutation analysis are 2 approaches to know how good a test suite is.

#### Coverage metrics

types of code coverage

- function coverage
- statement coverage
- branch coverage

#### Mutation analysis

founded on **competent programmer assumption**, which is

> The program is close to correct to begin with. It means that program faults are syntactically small and can be corrected with a few keystrokes.

Its key idea is **test variations (mutants) of the program**. 例如更改一些计算符号等类似的微小改动。

正常情况下，test suite对original code和mutant code的测试结果会不同，如果测试结果相同，一般说明test suite不完善，但也有可能是mutant和original等价，因此这个判别问题也需要一定方法进行解决。

然而这个问题不可判定，因此常常需要人工介入来判断。

## Module 3: Random Testing

random testing is also called `fuzzing`.

### What kinds of bugs can fuzzing find

- memory errors
- other undefined behaviors
  - integer overflow, divide-by-zero, null dereference
- assertion violations
- infinite loops
- concurrency bugs

### Random testing: Pros & Cons

- Pros
  - easy to implement
  - provably good coverage given enough tests
  - can work with programs in any format
  - appealing for finding security vulnerability
- Cons
  - inefficient test suite
  - might find bugs that are unimportant
  - poor code coverage

### AFL: American Fuzzy Lop

It is arguably the best-known coverage-guided fuzzing tool.

Its core ideas:

- genetic algorithm
- efficient source-code instrumentation
- effective heuristics for input mutation

```graph
+------+        +----------+        +--------+        +---------+
| LOAD | -----> | MINIMIZE | -----> | MUTATE | -----> | AUGMENT |
+------+        +----------+        +--------+        +---------+
    ^                                                      |
    |                                                      |
    +------------------------------------------------------+
```

Its 4 segments:

- LOAD
  - pick test cases from the mixture of seeds and feedbacks
- MINIMIZE
  - trim the test cases but don't alter the behavior of the test cases
- MUTATE
  - repeatedly mutate the minimized test cases using a variety of traditional heuristic based fuzzing strategies
- AUGMENT
  - add the feedbacks, which resulted in a new state transition, to the initial seeds
  - go to LOAD

其中，mutation heuristics are arguably the most important component of a successful fuzzer

Strategies used by AFL:

- flipping bits and bytes
- incrementing or decrementing constants
- replacing with potentially troublesome integers, usually from a pre-set set
- test case slicing

### LibFuzzer

Motivation: enable to fuzz libraries instead of the entire program.

### OSS-Fuzz

`OSS-Fuzz` is a continuous fuzzing infrastructure hosted on the Google Cloud Platform.

### ClusterFuzz

The backend of OSS-Fuzz.

### Grammar-based fuzzing

> Pumping lemma
>
> All sufficiently long words in a regular language may be pumped - that is, have a middle section of the word repeated an arbitrary number of times - to produce a new word that also lies within the same language.

如果一个语言`L`不满足`pumping lemma`，那么这个语言就不是正则语言。

Context-free Grammars provides a practical ground for use by grammar-based fuzzing.

使用上下文无关文法指定程序输入的好处有

- systematic and efficient test generation
- expressive enough to handle complex formats (e.g. XML or JSON)
- providee the basis for fuzzing a wide range of software components

### Concurrency testing in practice

使用sleep()随机delay来查看程序的行为。

#### Cuzz: fuzzing thread schedules

- `Cuzz` introduces `sleep()` calls
  - automatically
  - systematically before each statement (instead of those chosen by tester)
- gives worst-case probabilistic guarantee on finding bugs

##### Terms

- bug depth: the number of ordering constraints a schedule has to satisfy to find the bug (inter-process)
  - observation exploited by Cuzz: bug typically have small depth

### Summary

- random testing is effective for testing security, mobile apps and concurrency
- should complement, not replace systematic, formal testing
- must generate test inputs from a reasonable distribution in order to be effective
- may be less effective for systems with multiple layers (e.g. compiler)

## Module 4: Automated Test Generation

The key idea is to leverage the specifications to guide test generation:

- Types
- Invariants
- Pre- and Post-Conditions

### Feedback-directed random testing: Randoop

Input:

- classes under test
- time limit
- set of contracts

Output:

- contract-violating test cases

### Systematic testing: Korat

Its idea: leverage pre-conditions and post-conditions to generate tests automatically

> Small Test Case Hypothesis:
>
> If there is any test that causes the program to fail, there is a small such test

#### How to generate test inputs

- Use the types
- The class definition shows what values (or null) can fill each field
- Simply enumerate all possible shapes with a fixed set of nodes

#### Scheme for representing shapes

- order all possible values of each field
- order all fields into a vector
- each shape == vector of field values

#### A simple algorithm

- user selects some maximum input size `k`
- generate all possible inputs up to size `k`
- discard inputs where pre-condition is `false`
- run program on remaining inputs
- check results using post-condition

Korat avoid generating inputs that don't satisfy the invariant in the first place and leverage the invariant to guide the generation of tests.

The technique Korat uses is to instrument the invariant, which is to add code to record fields accessed by the invariant.

例如二叉树的构建，为了生成测试样例，树的形状根据它们相关的vector进行枚举

- initial candidate vector: all fields null
- next shape generated by:
  - expanding last field accessed in invariant
  - backtracking if all possibililties for a field are exhausted
- key idea: never expand fields not examined by invariant
- also, cleverly checks for and discards shapes isomorphic to previously generated shapes

例如，枚举含3个结点的二叉树


<table>
  <tr>
    <td></td>
    <td></td>
    <td colspan="2"><b>N0</b></td>
    <td colspan="2"><b>N1</b></td>
    <td colspan="2"><b>N2</b></td>
    <td></td>
  </tr>

  <tr>
    <td><b>id</b></td>
    <td><b>root</b></td>
    <td><b>left</b></td>
    <td><b>right</b></td>
    <td><b>left</b></td>
    <td><b>right</b></td>
    <td><b>left</b></td>
    <td><b>right</b></td>
    <td><b>valid?</b></td>
  </tr>

  <tr>
    <td>1</td>
    <td>null</td>
    <td>null</td>
    <td>null</td>
    <td>null</td>
    <td>null</td>
    <td>null</td>
    <td>null</td>
    <td>yes</td>
  </tr>

  <tr>
    <td>2</td>
    <td>N0</td>
    <td>null</td>
    <td>null</td>
    <td>null</td>
    <td>null</td>
    <td>null</td>
    <td>null</td>
    <td>yes</td>
  </tr>

  <tr>
    <td>3</td>
    <td>N0</td>
    <td>null</td>
    <td>N0</td>
    <td>null</td>
    <td>null</td>
    <td>null</td>
    <td>null</td>
    <td>no, containes a cycle</td>
  </tr>
</table>

第1次全`null`值符合invariant的要求，其中invariant检查的field是root，所以从root开始expand，也就是第2次将`root`值改为`N0`。第2次invariant检查到了`N0.left`和`N0.right`，`the last field accessed in invariant`是`N0.right`，所以下一次从`N0.right`开始扩展。

#### Strengths and weaknesses

- strong when we can enumerate all possibilities
- weak when enumeration is weak

only as good as the pre- and post-conditions

#### Summary

为什么automatic test generation在很多年前没有流行？

因为test generation严重依赖type information，而C和Lisp等语言没有提供必要的类型。而现代的语言如Java和UML提供了这样的类型信息。


