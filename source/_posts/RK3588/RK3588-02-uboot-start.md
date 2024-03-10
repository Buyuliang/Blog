---
title: uboot start
date: 2024年 03月 10日 星期日 23:57:11 CST
tags: [rk3588]
categories: rk3588
---

### SPL

``` bash
一般ROM Code是固化在芯片内部的，BL1是有厂商提供的bin文件，并不会开放源代码，所以从BL2开始。
从系统启动打印的信息可以找到BL2的入口如下，关键是_start函数.

Load uboot, ReadLba = 2000     //加载uboot （BL2）
Load OK, addr=0x200000, size=0x873d4  //uboot 地址和大小 mainload结束
...
INFO:    Entry point address = 0x200000  //UBoot 入口地址
 
//u-boot 符号表
4019: 0000000000200000     0 NOTYPE  GLOBAL DEFAULT    1 _start
```

``` bash
.globl  _start
 _start:
         nop
         b       reset     //jump to reset  ---->
```