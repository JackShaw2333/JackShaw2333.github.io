---
title: CSAPP-datalab
date: 2020-09-01 14:19:00
tags:
---

# Introduction

我对[datalab]中题目的解答

题目总要求（日后补充）

[datalab]: http://csapp.cs.cmu.edu/3e/datalab-handout.tar

<!--more-->

# 题目

## 1 bitXor

```c
/*
* bitXor - x^y using only ~ and &
*   Example: bitXor(4, 5) = 1
*   Legal ops: ~ &
*   Max ops: 14
*   Rating: 1
*/
int bitXor(int x, int y) {
    return ~((~(~x & y)) & (~(x & ~y)));
}
```

## 2 tmin

```c
/*
* tmin - return minimum two's complement integer
*   Legal ops: ! ~ & ^ | + << >>
*   Max ops: 4
*   Rating: 1
*/
int tmin(void) {
    return 1 << 31;
}
```

## 3 isTmax

```c
/*
* isTmax - returns 1 if x is the maximum, two's complement number,
*     and 0 otherwise
*   Legal ops: ! ~ & ^ | +
*   Max ops: 10
*   Rating: 1
*/
int isTmax(int x) {
    return !((x + 1) ^ (~x)) & !!(~x);
}
```

## 4 allOddBits

```c
 /*
* allOddBits - return 1 if all odd-numbered bits in word set to 1
*   where bits are numbered from 0 (least significant) to 31 (most significant)
*   Examples allOddBits(0xFFFFFFFD) = 0, allOddBits(0xAAAAAAAA) = 1
*   Legal ops: ! ~ & ^ | + << >>
*   Max ops: 12
*   Rating: 2
*/
/*
// 1st attempt
int allOddBits(int x) {
    int filter = 0xAA;
    int res0 = x & filter ^ filter;
    int res1 = x >> 8 & filter ^ filter;
    int res2 = x >> 16 & filter ^ filter;
    int res3 = x >> 24 & filter ^ filter;
    return !(res0 | res1 | res2 | res3);
}
*/
int allOddBits(int x) {
    x = (x >> 16) & x;
    x = (x >> 8) & x;
    x = (x >> 4) & x;
    x = (x >> 2) & x;
    return (x >> 1) & 1;  // &1 is to eliminate the consequences caused by arithmetic right shift 
}
```

## 5 negate

```c
/*
* negate - return -x
*   Example: negate(1) = -1.
*   Legal ops: ! ~ & ^ | + << >>
*   Max ops: 5
*   Rating: 2
*/
int negate(int x) {
    return ~x + 1;
}
```

## 6 isAsciiDigit

```c
/*
* isAsciiDigit - return 1 if 0x30 <= x <= 0x39 (ASCII codes for characters '0' to '9')
*   Example: isAsciiDigit(0x35) = 1.
*            isAsciiDigit(0x3a) = 0.
*            isAsciiDigit(0x05) = 0.
*   Legal ops: ! ~ & ^ | + << >>
*   Max ops: 15
*   Rating: 3
*/
int isAsciiDigit(int x) {
    int bit45 = (x >> 4) ^ 0x3;   // 0 if legal
    int bit3 = (x >> 3) & 0x1;    // 0 then legal, otherwise judge more
    int bit2 = (x >> 2) & 0x1;    
    int bit1 = (x >> 1) & 0x1;
    return !bit45 & (!bit3 | (!bit2 & !bit1));
}
```

## 7 conditional 

```c
/*
* conditional - same as x ? y : z
*   Example: conditional(2,4,5) = 4
*   Legal ops: ! ~ & ^ | + << >>
*   Max ops: 16
*   Rating: 3
*/
int conditional(int x, int y, int z) {
    int mask = ~(!x) + 1; // if x==0, mask=0x0; else mask=0xff
    return (~mask & y) + (mask & z);
}
```
