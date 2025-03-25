---
title: shell-calculator
date: 2025年 3月25日 星期二 14时23分47秒 CST
tags: [shell]
categories: shell
---

```sh
# 使用 `bc` 计算器（支持浮点数）
echo "3.14 * 2" | bc
echo "scale=4; 10 / 3" | bc  # 设置浮点精度

# 使用 `expr` 计算（仅支持整数）
expr 5 + 3

# 使用 `$(( ))` 进行整数运算
echo $((3 + 5))
echo $((10 / 3))  # 整数除法
echo $((2 ** 3))  # 计算 2^3

# 使用 `python` 计算
python3 -c "print(3.14 * 2)"

# 使用 `awk` 计算
awk 'BEGIN {print 10 / 3}'

# 计算 `sin()`、`cos()`、`sqrt()`
echo "s(1)" | bc -l   # sin(1)
echo "c(1)" | bc -l   # cos(1)
echo "sqrt(16)" | bc -l  # 平方根
```