---
title: shell-string
date: 2023-10-02 13:38:15
tags: [shell]
categories: shell string
---

### 获取字符串长度
``` bash
${#string}
```

### 字符串拼接
``` bash
${str1}${str2}
"${str1} ${str2}"
```
### 字符串截取
``` bash
# 从string字符串的左边第start个字符开始，向右截取到最后，start从0开始
${string:start}
# 从string字符串的左边第start个字符开始，向右截取length个字符
${string:start:length}
# 从string字符串的右边第start个字符开始，向右截取到最后，start从1开始
${string:0-start}
# 从string字符串的右边第start个字符开始，向右截取length个字符
${string:0-start:length}
# 从string字符串左边第一次出现*chars的位置开始，截取*chars右边的所有字符
${string#*chars}
# 从string字符串左边最后一次出现*chars的位置开始，截取*chars右边的所有字符
${string##*chars}
# 从string字符串右边第一次出现chars*的位置开始，截取chars*左边的所有字符
${string%chars*}
# 从string字符串右边最后一次出现chars*的位置开始，截取chars*左边的所有字符
${string%%*chars*}
```