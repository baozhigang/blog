---
title: PHP实现计数排序
date: 2020-03-27
categories:
- algorithm
tags:
- php
- algorithm
---

粘贴代码如下：

<!--more-->

``` bash
计数排序：
function countingSort($arr)
{
    $maxValue = max($arr);

    // 初始化数组
    for ($i=0; $i < $maxValue+1; $i++) {
        $bucket[$i] = 0;
    }

    // 写入临时数组
    for ($i=0; $i < count($arr); $i++) {
        $bucket[$arr[$i]]++;
    }

    // 覆盖原数组
    $index = 0;
    for ($i=0; $i < $maxValue+1; $i++) {
        while($bucket[$i] > 0) {
            $arr[$index++] = $i;
            $bucket[$i]--;
        }
    }

    return $arr;
}

```
