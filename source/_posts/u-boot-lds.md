---
title: linux-uboot
date: 2023-12-07 20:35:35
tags: [uboot]
categories: linux-uboot
---

这是一个 U-Boot bootloader 的链接脚本（Linker Script），用于指定 U-Boot 在内存中的布局和各个部分的放置规则。以下是对该脚本的简要解释：
```c

OUTPUT_FORMAT("elf32-littlearc", "elf32-littlearc", "elf32-littlearc")
OUTPUT_ARCH(arc)

    指定输出文件的格式为 ELF32-little-endian，输出的架构为 ARC。
```
入口点：

```c

ENTRY(_start)
```
    指定程序的入口点为 _start。

SECTIONS 部分：

```c

SECTIONS
{
   ...
}
```
    SECTIONS 部分定义了各个段的布局。

代码段（.text）：

```c

.text : {
   arch/arc/lib/start.o (.text*)
   *(.text*)
}
```
    将 arch/arc/lib/start.o 中的 .text* 段放入 .text 段中，然后将所有其它包含 .text* 的段也放入 .text 段中。

向前对齐到 1024 字节：

```c

. = ALIGN(1024);
```
    将地址指针向前对齐到 1024 字节。

中断向量表（.ivt）：

```c

__ivt_start = .;
.ivt :
{
   *(.ivt)
}
__ivt_end = .;
```
    定义了中断向量表段，并将其中的内容放入 .ivt 段。

只读数据段（.rodata）：

```c

.rodata : {
   *(SORT_BY_ALIGNMENT(SORT_BY_NAME(.rodata*)))
}
```
    将只读数据段中的内容放入 .rodata 段。

数据段（.data）：

```c

.data : {
   *(.data*)
}
```
    将数据段中的内容放入 .data 段。

U-Boot 列表段（.u_boot_list）：

```c

.u_boot_list : {
   KEEP(*(SORT(.u_boot_list*)));
}
```
    定义了 U-Boot 列表段，并保留其中的内容。

重定位段（.rela.dyn）：

```c

__rel_dyn_start = .;
.rela.dyn : {
   *(.rela.dyn)
}
__rel_dyn_end = .;
```
    将重定位段中的内容放入 .rela.dyn 段。

未初始化数据段（.bss）：

```c

.bss : {
   *(.bss*)
}
__bss_end = .;
```
    将未初始化数据段中的内容放入 .bss 段。

代码复制开始和结束标记：

```c

. = ALIGN(4);
__image_copy_start = .;
__image_copy_end = .;
```
    确保地址对齐到 4 字节，并定义了代码复制的开始和结束标记。

初始化结束标记：

```c

    . = ALIGN(4);
    __init_end = .;
```
        确保地址对齐到 4 字节，并定义了初始化结束标记。

这个链接脚本主要用于定义 U-Boot 在内存中的布局，以及各个段的放置规则。它决定了编译后的 U-Boot 在运行时的内存布局和组织结构。