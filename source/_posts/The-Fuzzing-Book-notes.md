---
title: The Fuzzing Book notes
date: 2021-07-15 22:58:23
tags: fuzzing
---

## II Lexical Fuzzing

<!--more-->

### Fuzzing: Breaking Things with Random Inputs

### [Code Coverage](https://www.fuzzingbook.org//html/Coverage.html)

#### [Black-Box Testing](https://www.fuzzingbook.org//html/Coverage.html#Black-Box-Testing)

derive tests from the specification

pros & cons

- Advantage: 独立于implementation，可在implementation前创建测试
- Disadvantage: implemented behavior相比specified behavior覆盖更广

#### [White-Box Testing](https://www.fuzzingbook.org//html/Coverage.html#White-Box-Testing)

Derive tests from the implementation (internal structure)

> Coverage criteria:
>
> - statement coverage: each statement must be executed by at least one input
> - branch coverage: each branch must be executed by at least one input

pros & cons

- Advantage: find errors in implemented behavior
- Disadvantage: may miss non-implemented behavior

#### [Tracing Executions](https://www.fuzzingbook.org//html/Coverage.html#Tracing-Executions)

`sys.settrace(f)`允许定义的`f()`函数在每行代码被执行时调用，`f()`函数有3个参数：

- `frame`, access to the current location and variables
  - `frame.f_code` is the currently executed code, `frame.f_code.co_name` is the function name
  - `frame.f_lineno` holds the current line number
  - `frame.f_locals` holds the current local variables and arguments
- `event`, a string, either "line" (a new line reached) or "call" (a func being called)
- `arg`, additional arg

#### [A Coverage Class](https://www.fuzzingbook.org//html/Coverage.html#A-Coverage-Class)

Python的`with`结构

```python
with OBJECT [as VARIABLE]:
    BODY
```

executes `BODY` with `OBJECT` being defined and stored in `VARIABLE`

在`BODY`的开始前和结束后，methods `OBJECT.__enter__()`和`OBJECT.__exit__()`会被自动调用，即使`BODY`出现异常。所以coverage的函数模板为：

```python
with Coverage() as cov:
    function_to_be_traced()
c = cov.coverage()
```

### [Mutation-Based Fuzzing](https://www.fuzzingbook.org//html/MutationFuzzer.html)


