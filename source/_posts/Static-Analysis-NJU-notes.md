---
title: Static Analysis - NJU notes
date: 2020-10-19 14:28:45
tags: static analysis
---

## Sources

课程的资料来源如下。

[录播视频B站主页]

[课程主页]

[录播视频B站主页]: https://space.bilibili.com/2919428?spm_id_from=333.788.b_765f7570696e666f.1

[课程主页]: https://pascal-group.bitbucket.io/

<!--more-->

## Lec 1 Introduction

### PL and Static Analysis

Static Analysis属于Programming Language的一种application.

### Why we need Static Analysis

因为程序愈加复杂，人们更加关注以下内容：

- Program Reliability
- Program Security
- Compiler Optimization
- Program Understanding

### What is Static Analysis

Static Analysis试图在程序P运行之前分析它的行为以及它所满足的某些性质。

然而，根据`Rice's Theorem`，不存在一种方法能够判断（给出确切的回答）一个程序P是否满足一些`non-trivial`的性质。

> There is no such approach to determine whether P satisfies such non-trival properties, i.e., giving **exact answer**: Yes or No

#### Rice's Theorem

> Any non-trivial property of the behavior of programs in a r.e. language is undecideable.

其中，`r.e.`指`recursively enumerable`，即能被图灵机识别的语言。

那么，什么是`non-trivial properties`？

> A property is trivial if either it is not satisfied by any r.e. language, or if it is satisfied by all r.e. languages; otherwise it is non-trivial.

可以简单的理解为，`non-trivial`性质是一些有趣的性质，是于程序运行时行为有关的性质。

### Static Analysis features and examples

我们期望一个完美的Static Analysis能够做到如下2点：

- Sound
- Complete

然而`Rice's Theorem`告诉我们不存在这样完美的Static Analysis。

Sound，Complete和Truth的关系如下图所示。

```graph
+----------------------------+
| Sound                      |
|      +-------------------+ |
|      | Truth             | |
|      |      +----------+ | |
|      |      | Complete | | |
|      |      +----------+ | |
|      +-------------------+ |
+----------------------------+
```

- Truth代表Sound & Complete，是程序所有真正可能的行为
- Sound是一种overapproximation，存在`false positives`
- Complete是一种underapproximation，存在`false negatives`

因为`Rice's Theorem`指出的问题，所以我们的目的转而为实现**有用的**static analysis。

一般而言，我们选择牺牲completeness，实现sound and not fully-pricise static analysis。

#### Necessity of soundness

soundness对于诸如compiler optimization和program verification的应用非常关键。

到这里，可以给static analysis下一个定义：

> Static Analysis: ensure (or get close to) soundness, while making good trade-offs between analysis precision and analysis speed.

如果用2个词概括static analysis，就是`abstraction`和`overapproximation`。

## Lec 2 IR

### ASR vs IR

AST:

- high-level，接近语法结构
- language dependent
- 适合快速的type checking
- 缺乏control flow信息

IR:

- low-level，更接近机器码
- language independent
- compact and uniform
- 包含control flow信息
- basis for static analysis

### IR: three-address code (`3AC`)

At most one operator on the right side of an instruction.

Each `3AC` contains at most 3 address.

Address can be:

- Name
- Constant
- Temporary name

### `3AC` in real static analyzer: Soot

Soot's IR is `Jimple`: typed `3AC`

#### JVM中的4种方法调用

| 指令                   | 作用                                                            |
|------------------------|-----------------------------------------------------------------|
| `invokespecial`        | call constructor, call superclass methods, call private methods |
| `invokevirtual`        | instance methods call (virtual dispatch)                        |
| `invokeinterface`      | 调用interface的方法 不能做优化 检查是否实现interface方法        |
| `invokestatic`         | call static methods                                             |
| Java7: `invokedynamic` | 用于在JVM上运行非静态语言                                       |

#### method signature

`Jimple`中的`method signature`格式为`class-name: return-type method-name(param1-type, param2-type, ...)`

#### `clinit`方法

程序在用到某一个类时需要将该类加载到内存，加载后就会调用`clinit`方法，进行例如初始化静态变量等操作。

### Static single assignment (SSA) （Optional）

每一个变量都有一个单独的名字。

### Basic blocks（`BB`）

需要满足的性质：

- `BB`的入口只能是它的第一条指令
- `BB`的出口只能是它的最后一条指令

建立`BB`的算法：

- 确定`leader`
  - 程序的第1条`3AC`为`leader`
  - 任何跳转的target为`leader`
  - 任何跳转`3AC`的下一条`3AC`为`leader`
- 构建`BB`
  - 2条`leader`之间的`3AC`属于前一条`leader`的`BB`

### Control flow graphs (CFG)

CFG以`BB`为结点。

构建边算法：

- `BB` `A`和`BB` `B`之间有一条边当且仅当：
  - `A`末尾有一条任意形式的跳转指令到`B`开头
  - `A`与`B`相邻（`A`末尾是无条件跳转除外）

这样指令的跳转label可以替换为`BB`。

通常需要加入`entry`和`exit`结点。

## Lec 3 Data flow analysis 1

### Overview

- how data flows on CFG?
- how **application-specific (abstraction) data flows (safe-approximation)** through the **nodes (transfer funcs) and edges (control-flow handling) of** CFG?

- may analysis
  - outputs info that may be true
  - **over-approximation** is safe
- must analysis
  - outputs info that must be true
  - **under-approximation** is safe

因此，不同的DFA有不同的

- abstraction
- safe strategies
- transfer functions
- control-flow handling

### Preliminaries

#### Input and output states

- each IR execution将一个`input state`转化为一个`output state`
- 每一个state都与一条IR前后的`program point`相关联

汇聚形式的某个IR的$input\ state = output\ state1 \sqcap output\ state2$，其中$\sqcap$为`meet operator`，可以表示$\cup$或$\cap$等。

在任何一个DFA应用中，我们将每个`program point`都和一个data-flow value关联，这个value代表了在这个point能观察到的所有可能的state的抽象形式。

> 所有可能的data-flow value的集合是这次应用的`domain`。

#### Notations for transfer function's constraints

```graph
  |
  . <--- IN[s]
  |
  v
+---+
| s |
+---+
  |
  . <--- OUT[s]
  |
  v
```

每个语句s都是一个`transfer function`

- forward analysis
  - $OUT[s] = f_s(IN[s])$
- backward analysis
  - $IN[s] = f_s(OUT[s])$

#### Notations for control flow's constraints

```graph
   B
+-----+
| S1  |
| S2  |
| S3  |
| ... |
| Sn  |
+-----+
```

##### control flow within a `BB`

$IN[s_{i+1}]=OUT[s_i], \ for \ all \ i = 1, 2, ..., n-1$

##### control flow among `BB`s

$IN[B] = IN[s_1], OUT[B] = OUT[s_n]$

汇聚情况

###### forward

```graph
+----+  +----+   |
| P1 |  | P2 |   |
+----+  +----+   v
   \      /
    v    v
    +----+
    | B  |
    +----+
```

$IN[B] = \sqcap_{P \ a \  predecessor \ of \ B} OUT[P]$

$OUT[B] = f_B(IN[B]), f_B = f_{s_n} \circ ... \circ f_{s_1}$

###### backward

```graph
    +----+
    | B  |
    +----+
     /  \
    v    v
+----+  +----+   ^
| S1 |  | S2 |   |
+----+  +----+   |
```

$OUT[B]=\sqcap_{S \ a \ successor \ of \ B} IN[S]$

$IN[B] = f_B(OUT[B]),  f_B = f_{s_1} \circ ... \circ f_{s_n}$

### Reaching definitions analysis

#### Issues not covered

- method calls
- aliases

暂不引入上述2概念，否则过于复杂

---

“对变量v的定义”指的是对v赋值的语句。

什么是reaching definitions?

> A definition `d` at program point `p` reaches a point `q` if there is a path from `p` to `q` such that `d` is not killed along the path

因为考虑所有的path，所以是may analysis。

reaching definition可用于检查变量是否被初始化。

#### Understanding reaching definitions

```graph
B
+---------------+
| D: v = x op y |
+---------------+
```

这条语句“生成”了一个定义`D`并且“杀死”了程序中其他对变量`v`的定义。此时abstraction是definition，故集合内的元素为definition。

#### transfer function

$OUT[B] = gen_B \cup (IN[B]-kill_B)$

```graph
+----+  +----+   |
| P1 |  | P2 |   |
+----+  +----+   v
   \      /
    v    v
    +----+
    | B  |
    +----+
```

#### control flow

$IN[B] = \cup_{P \ a \  predecessor \ of \ B} OUT[P]$

#### Algorithm of reaching definitions analysis

暂略

## Lec 4 Data flow analysis 2

### Live variables analysis

> Live variables analysis tells if the value of variable `v` at program point `p` could be used along some path in CFG starting at `p`. If so, `v` is live at `p`; otherwise, `v` is dead at `p`

可用于寄存器优化。

```graph
      P
      +-----------+
      | v = 3     |
      +-----------+
            |
            v
      B
      +-----------+
      | ?         |
      +-----------+
      /           \
     v             v
S1            S2
+---------+   +---------+   ^
| ... = v |   | ... ... |   |
+---------+   +---------+   |
```

backward方式更适合live variables analysis。

此时abstraction为liva variables，故集合内的元素为variables。

LVA是may analysis。

$OUT[B] = \cup_{S\ a\ successor\ of\ B}IN[S]$

$IN[B] = use_B \cup (OUT[B] - def_B)$

#### Algorithm

暂略

### Available expressions analysis

> An expression `x op y` is available at program point `p` if
>
> - all paths from the `entry` to `p` must pass through the evalution of `x op y`
> - after the last evalution of `x op y`, there is no redefinition of `x` or `y`

```graph
       |   IN = {a + b}    |
       v                   |
+------------+             v
| a = x op y |
+------------+
       |   OUT = {x op y}
       v
```

forward analysis

$OUT[B] = gen_B \cup (IN[B] - kill_B)$

$IN[B] = \cap_{P\ a\ predecessor\ of\ B}OUT[P]$

#### Algorithm

### Summary

|                   | reaching definitions         | live variables               | available expressions        |
|-------------------|------------------------------|------------------------------|------------------------------|
| domain            | set of definitions           | set of variables             | set of expressions           |
| direction         | forwards                     | backwards                    | forwards                     |
| may / must        | may                          | may                          | must                         |
| boundary          | $OUT[entry] = \phi$          | $IN[exit] = \phi$            | $OUT[entry] = \phi$          |
| initialization    | $OUT[B] = \phi$              | $IN[B] = \phi$               | $OUT[B] = \cup$              |
| transder function | $OUT = gen \cup (IN - kill)$ | $IN = gen \cup (OUT - kill)$ | $OUT = gen \cup (IN - kill)$ |
| meet              | $\cup$                       | $\cup$                       | $\cap$                       |

## Lec 5 Data flow analysis - foundations 1

### Iterative algorithm, another view

假设一个`CFG`有k个结点，迭代算法在每轮迭代时更新每个结点的$OUT[n]$。

同时假设DFA中值的`domain`是$V$，我们可以定义一个`k-tuple`

$$(OUT[n_1], ..., OUT[n_k])$$

并且它是集合$(V_1 \times V2 \times ... \times V_k)$的元素，即$V^k$的元素。

那么每轮迭代可以看作一个映射$F: V^k \mapsto V^k$

### Partial order

偏序集的3个性质

- reflexivity
- antisymmetry
- transitivity

`poset`中2个元素之间不一定可以比较。

### Upper and lower bounds

#### `lub` (join) & `glb` (meet)

一些性质：

- 不是每个`poset`都有`lub`或`glb`
- 如果一个`poset`有`lub`或`glb`，它一定是唯一的

### Lattice, semilattice, complete and product lattice

如果一个`poset`中任意2个元素，它们的`lub`和`glb`都存在，则这个`poset`称为lattice

2种semilattice:

- 如果只存在`lub`，称为join lattice
- 如果只存在`glb`，称为meet lattice

一个`lattice`中任意子集都存在`lub`和`glb`，则这个`lattice`是`complete poset`。

对于每个`complete lattice` P

- P的`lub`$\top$称为`top`
- P的`glb`$\bot$称为`bottom`

同时，每个finite的lattice一定是`complete lattice`，反之不一定，例如“[0, 1]之间的实数上的小于等于关系”。

如果lattices$L_1, ... ,L_n$每个lattice都存在`lub`和`glb`，那么可以定义一个`product lattice`$L^n$，是所有lattice的笛卡尔积。

`product lattice`的一些性质：

- `product lattice`也是lattice
- 如果单个lattice is complete，那么`product lattice`也complete

### Data flow analysis framework via lattice

一个DFA framework $(D, L, F)$由以下构成：

- $D$: a direction of flow, forwards or backwards
- $L$: a lattice includeing a domain of $V$ and a meet or join operator
- $F$: a family transfer function from $V$ to $V$

这样，DFA可以看作是迭代地对lattice的值进行transfer func转换和meet/join操作的过程。

### Monotonicity and fixed point theorem （重要）

#### Monotonicity

> A function f: $L \mapsto L$ ($L$ is a lattice) is monotonic if $\forall x, y \in L$，
> $$x \leq y \implies f(x) \leq f(y)$$

#### Fix-point theorem

> 对于一个`complete lattice`，如果
>
> - $f: L \mapsto L$ is **monotonic** and
> - $L$ is **finite**
>
> 那么
>
> - 最小不动点可以通过$f(\bot), f(f(\bot)), ..., f^k(\bot)$迭代求出
> - 最大不动点可以通过$f(\top), f(f(\top)), ..., f^k(\top)$迭代求出

##### 存在性证明

##### 最小（大）性证明

## Lec 6 DFA - foundations 2

### Relate iterative algorithm to fixed point theorem

迭代算法中的函数$f$是由transfer function和meet/join operator组成的。为此我们需要证明

- 迭代算法中使用的complete lattice是finite的
- 函数$f$是单调的

当我们将迭代算法和不动点定理成功关联之后，我们可以证明迭代算法

- 一定能找到不动点
- 能找到最优不动点（“最优”含义后文定义）

目前还需解决的问题是“迭代算法何时能找到这个不动点？”，为此引入以下概念

> the height of a lattice `h` is the length of the **longest path** from $\top$ to $\bot$ in the lattice

不难得出，一个有`k`个node的CFG，每个结点的取值集合是lattice且`height`为`h`，所以最坏情况的迭代次数为`k * h`。

### May/must analysis, a lattice view

- may analysis中least fixed point最准
- must analysis中greatest fixed point最准

[![may-must-analysis](https://s3.ax1x.com/2020/11/20/DQQfVP.png)](https://imgchr.com/i/DQQfVP)

### MOP and distributivity

#### Meet-Over-All-Paths Solution (MOP)

$P = Entry \rightarrow S_1 \rightarrow ... \rightarrow S_n$

P的transfer function $F_P$是path上所有statement的transfer function的复合

$$MOP[S_i] = {\sqcup / \sqcap}_{a\ path\ P\ from\ Entry\ to\ S_i} F_P(OUT[Entry])$$

MOP计算每个path末尾的data-flow value并且使用join/meet得出它们的lub/glb。

MOP的一些问题：

- 有些path可能是unexecutable，所以会导致不精确
- unbounded and not enumerable，导致不可行

#### Our iterative algorithm vs MOP

我们的方法会在中间某个`BB`处进行meet/join操作，然后再进行transfer function操作，而MOP在最后才进行meet/join操作，抽象地表示即为

- $Ours = F(x \sqcup y)$
- $MOP = F(x) \sqcup F(y)$

因为

$$x \leq x \sqcup y$$
$$y \leq x \sqcup y$$

又因为$F$是单调的，所以

$$F(x) \leq F(x \sqcup y)$$
$$F(y) \leq F(x \sqcup y)$$

所以

$$F(x) \sqcup F(y) \leq F(x \sqcup y)$$

即$MOP \leq Ours$，若$F$满足分配律，那么$MOP = Ours$

注意，**bit-vector & gen/kill problems (set union/intersection for join/meet) are distributive**

### Constant propagation

### Worklist algorithm

## Lec 7 Interprocedural Analysis

### Motivation

### Call graph construction: CHA

> a call graph is a set of edges from call-sites to their target methods

以面向对象的典型语言Java为例。

|                  | static call    | special call                                                        | virtual call                   |
|------------------|----------------|---------------------------------------------------------------------|--------------------------------|
| instruction      | invokestatic   | invokespecial                                                       | invokeinterface, invokevirtual |
| receiver objects | $\times$       | $\surd$                                                             | $\surd$                        |
| target methods   | static methods | constructors, private instance methods, superclass instance methods | other instance methods         |
| #target methods  | 1              | 1                                                                   | $\geq 1$ (polymorphism)        |
| determinacy      | compile-time   | compile-time                                                        | run-time                       |

`virtual call`是构建OOPL call graph的关键。

#### Method dispatch of virtual calls

在运行时，virtual call基于以下内容解析

- type of the receiver object
- method signature at the call site

在本lecture中，method signature可以视作method的ID

- signature = <font color="red">class type</font> + <font color="blue">method name</font> + <font color="green">descriptor</font>
- <font color="green">descriptor = return type + parameter type</font>

```java
class C {
  T foo(P p, Q q, R r) {...}
}
```

<<font color="red">C</font>: <font color="green">T</font> <font color="blue">foo</font>(<font color="green">P, Q, R)</font>>


我们定义函数$Dispatch(c, m)$来模拟运行时method dispatch的过程。

$$
Dispatch(c, m) =
\begin{cases}
m', & if\ \textbf{c}\ contains\ non-abstract\ method\ \textbf{m'}\ that\ has\ the\ same\ name\ and\ descriptor\ as\ \textbf{m} \\
Dispatch(c', m) & otherwise, where\ \textbf{c'} is\ superclass\ of\ \textbf{c}
\end{cases}
$$

#### Call resolution of CHA (class hierarchy analysis)

CHA速度快但精度低，指针分析速度慢精度高

### Interprocedural control-flow graph

- CFG represents structure of an individual method
- ICFG represents structure of the entire program

> An ICFG consists of CFGs of the methods in the program, plus 2 kinds of additional edges:
>
> - Call edge: from call sites to the entry nodes of their callees
> - Reture edge: from return statements of the callees to the statements following their call sites (i.e. return sites)

``` java
void foo() {
  bar(); // call site
  int n = 3; // return site
}
```

所以

> ICFG = CFGs + call & return edges

call & return edge也需要transfer function

### Interprocedural data-flow analysis

对于每个call node, kill data-flow value for the LHS variable, its value will flow to return site along the return edges；否则会损失精度，如constant propagation中LHS原来的值和return得到的值若进行join操作会得到NAC

call site到return site的边称为`call-to-return edge`，它使得analysis能够在ICFG中propagate local data-flow，如果没有这条edge，local data-flow将经过其他method，这无疑是低效的。

## Lec 8 Pointer Analysis

### Motivation

比CHA精确。

### Intro

- compute which mem locations a pointer can point to
- regarded as a may-analysis
  - computes an over-approximation of the set of objects that a pointer can point to

```graph
         input                     output
program -------> pointer analysis --------> points-to relations
```

#### Pointer analysis vs alias analysis

是2个联系紧密但不同的概念

- pointer analysis: which objects a pointer can point to?
- alias analysis: can 2 pointers point to the same object?

### Key factors

- heap abstraction
- context sensitivity
- flow sensitivity
- analysis cope

#### Heap abstraction

在程序动态运行的过程中，heap object的数量可能是无限的。为了保证程序可终止，heap abstraction model动态地将无限的具体的object分配为有限的抽象object。

##### Allocation-site abstraction

用allocation site抽象具体的object，每个allocation site都有一个abstract object，因此abstract object的数量是有限的。

#### Context sensitivity

| content-sensitive                                         | context-insensitive                    |
|-----------------------------------------------------------|----------------------------------------|
| distinguish different calling contexts of a method        | merge all calling contexts of a method |
| analyze each method multiple times, once for each context | analyze each method once               |

#### Flow sensitivity

how to model control flow?

| flow-sensitive                                                 | flow-insensitive                                                                  |
|----------------------------------------------------------------|-----------------------------------------------------------------------------------|
| respect the execution order of the statements                  | ignore the control-flow order, treat the program as a set of unordered statements |
| maintain a map of points-to relations at each program location | maintain one map of points-to relations for the whole program                     |

#### Analysis scope

- whole-program
- demand-driven

### Pointers in Java

我们只关注影响pointer的statements。

- local variable
- static field
- instance field
- array element
  - usually ignore indexes, only modeled as a single field

### Pointer-affecting statements

本课程暂时忽略`static field`和`array element`。

- new
- assign
- store
- load
- call

## Pointer analysis foundations 1

### Domains and notations

| 类型                  | 表示                                  |
|-----------------------|---------------------------------------|
| Variables             | $x, y \in V$                          |
| Fields                | $f, g \in F$                          |
| Objects               | $o_i, o_j \in O$                      |
| Instance fields       | $o_i.f, o_j.f \in O \times F$         |
| Pointers              | $Pointer = V \cup (O \times F)$       |
| Pointers-to relations | $pt = Pointer \mapsto \mathcal{P}(O)$ |

- $\mathcal{P}(O)$ denotes the powerset of $O$
- $pt(p)$ denotes the points-to set of $p$

### Rules

| kind | statement | rule |
|-|-|-|
| new | `i: x = new T()` | $\overline{o_i \in pt(x)}$ |
| assign | `x = y` | $\frac{o_i \in pt(y)}{o_i \in pt(x)}$ |
| store | `x.f = y` | $\frac{o_i \in pt(x), o_j \in pt(y)}{o_j \in pt(o_i.f)}$ |
| load | `y = x.f` | $\frac{o_i \in pt(x), o_j \in pt(o_i.f)}{o_j \in pt(y)}$ |

其中，$\frac{premises}{conclusion}$
