---
layout: post
title:  "水塘抽样"
date:   2016-10-20 17:30:00
categories: Algorithm
---

先看几个问题：

- 从一个很大的源代码中随机选取一行。
- 对一个长度未知的链表随机选取其中的k个节点。

**水塘抽样**([Reservoir sampling](https://en.wikipedia.org/wiki/Reservoir_sampling)) 是随机算法的一种。其目的在于从包含n个项目的集合S中选取k个样本，其中n为一很大或未知的数量，尤其适用于不能把所有n个项目都存放到主内存的情况。

考虑k=1的情况。算法如下：

- Keep the first item in memory.
- When the *i*-th item arrives (for *i*>1):with probability 1/*i*, keep the new item (discard the old one)with probability 1-1/*i*, keep the old item (ignore the new one)

So:

- when there is only one item, it is kept with probability 1;
- when there are 2 items, each of them is kept with probability 1/2;
- when there are 3 items, the third item is kept with probability 1/3, and each of the previous 2 items is also kept with probability (1/2)(1-1/3) = (1/2)(2/3) = 1/3;
- by induction, it is easy to prove that when there are *n* items, each item is kept with probability 1/*n*.



考虑一般情况，*Algorithm R* by [Jeffrey Vitter](https://en.wikipedia.org/wiki/Jeffrey_Vitter)：<!-- more -->

```cpp
/*
  S has items to sample, R will contain the result
 */
ReservoirSample(S[1..n], R[1..k])
  // fill the reservoir array
  for i = 1 to k
      R[i] := S[i]
 
  // replace elements with gradually decreasing probability
  for i = k+1 to n
    j := random(1, i)   // important: inclusive range
    if j <= k
        R[j] := S[i]
```

即以概率`k/i` 替换原水塘中的元素。