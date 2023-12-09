---
title: start.s
date: 2023年12月9日 16:51:35
tags: [uboot]
categories: uboot
---

```c
/*
 * (C) Copyright 2013
 * David Feng <fenghua@phytium.com.cn>
 *
 * SPDX-License-Identifier:	GPL-2.0+
 */

#include <asm-offsets.h>
#include <config.h>
#include <linux/linkage.h>
#include <asm/macro.h>
#include <asm/armv8/mmu.h>

/*************************************************************************
 *
 * Startup Code (reset vector)
 *
 *************************************************************************/

.globl	_start  //定义 _start 标签为全局可见，表示这是程序的入口点。
_start:
#ifdef CONFIG_ENABLE_ARM_SOC_BOOT0_HOOK
/*
 * Various SoCs need something special and SoC-specific up front in
 * order to boot, allow them to set that in their boot0.h file and then
 * use it here.
 */
#include <asm/arch/boot0.h>
#else
	b	reset   //跳转到 reset 标签处执行代码。
#endif

#if !CONFIG_IS_ENABLED(TINY_FRAMEWORK)
	.align 3    //对齐下一条指令到 8 字节边界。

.globl	_TEXT_BASE  //定义 _TEXT_BASE 标签为全局可见，表示代码段的起始地址。
_TEXT_BASE:
#if defined(CONFIG_SPL_BUILD)
	.quad   CONFIG_SPL_TEXT_BASE
#else
	.quad	CONFIG_SYS_TEXT_BASE
#endif

/*
 * These are defined in the linker script.
 */
.globl	_end_ofs    //定义 _end_ofs 标签为全局可见，表示代码段的结束地址与 _start 标签之间的偏移量。
_end_ofs:
	.quad	_end - _start   //使用 _end 减去 _start 的值，并将结果保存为一个 8 字节的值。

.globl	_bss_start_ofs  //定义 _bss_start_ofs 标签为全局可见，表示 BSS 段的起始地址与 _start 标签之间的偏移量。
_bss_start_ofs:
	.quad	__bss_start - _start    //标签处的代码：使用 __bss_start 减去 _start 的值，并将结果保存为一个 8 字节的值。

.globl	_bss_end_ofs    //标签处的代码：使用 __bss_start 减去 _start 的值，并将结果保存为一个 8 字节的值。
_bss_end_ofs:
	.quad	__bss_end - _start  //标签处的代码：使用 __bss_end 减去 _start 的值，并将结果保存为一个 8 字节的值。

reset:
	/* Allow the board to save important registers */
	b	save_boot_params
.globl	save_boot_params_ret    //这部分代码用于保存重要的寄存器状态，以备后续使用。
save_boot_params_ret:

//这部分代码用于在 U-Boot 被加载和执行的地址与链接地址不同时，修复 .rela.dyn 的重定位。它计算 _start 的运行时值和链接值之间的偏移量，并对 __rel_dyn_start 到 __rel_dyn_end 范围内的重定位项进行修复。
#if CONFIG_POSITION_INDEPENDENT
	/*
	 * Fix .rela.dyn relocations. This allows U-Boot to be loaded to and
	 * executed at a different address than it was linked at.
	 */
pie_fixup:
    //从 _start 符号获取运行时的值，并将其存储在寄存器 x0 中。
	adr	x0, _start		/* x0 <- Runtime value of _start */
    //从 _TEXT_BASE 符号获取链接时的值，并将其存储在寄存器 x1 中。
	ldr	x1, _TEXT_BASE		/* x1 <- Linked value of _start */
    //计算运行时与链接时的偏移量，并将其存储在寄存器 x9 中。
	sub	x9, x0, x1		/* x9 <- Run-vs-link offset */
    //从 __rel_dyn_start 和 __rel_dyn_end 符号获取运行时的地址范围，并将其存储在寄存器 x2 和 x3 中。
	adr	x2, __rel_dyn_start	/* x2 <- Runtime &__rel_dyn_start */
	adr	x3, __rel_dyn_end	/* x3 <- Runtime &__rel_dyn_end */
    //进入一个循环，从 x2 指向的位置加载链接位置和修复值到寄存器 x0 和 x1 中。
pie_fix_loop:
	ldp	x0, x1, [x2], #16	/* (x0, x1) <- (Link location, fixup) */
	ldr	x4, [x2], #8		/* x4 <- addend */
    //检查修复类型是否为相对修复（relative fixup）（通过比较 w1 和 1027）。
	cmp	w1, #1027		/* relative fixup? */
	bne	pie_skip_reloc
    //如果是相对修复，将链接位置和修复值分别加上运行时与链接时的偏移量，并将修正值加上运行时与链接时的偏移量。
	/* relative fix: store addend plus offset at dest location */
	add	x0, x0, x9
	add	x4, x4, x9
    //将修正值存储回目标位置。
	str	x4, [x0]
pie_skip_reloc:
	cmp	x2, x3
	b.lo	pie_fix_loop
pie_fixup_done:
#endif

//这部分代码用于配置系统的重置控制寄存器。它根据当前的 EL（执行级别）设置相应的寄存器，以确保系统在重置后处于正确的初始状态。
#ifdef CONFIG_SYS_RESET_SCTRL
	bl reset_sctrl
#endif
	/*
	 * Could be EL3/EL2/EL1, Initial State:
	 * Little Endian, MMU Disabled, i/dCache Disabled
	 */
//这部分代码用于根据当前的 EL 设置向量基址寄存器（VBAR）和系统控制寄存器（SCR）的值。根据不同的 EL，它设置相应的 EL 特定的寄存器值。
	adr	x0, vectors
	switch_el x1, 3f, 2f, 1f
3:	msr	vbar_el3, x0
	mrs	x0, scr_el3
	orr	x0, x0, #0xf			/* SCR_EL3.NS|IRQ|FIQ|EA */
	msr	scr_el3, x0
	msr	cptr_el3, xzr			/* Enable FP/SIMD */
#ifdef COUNTER_FREQUENCY
	ldr	x0, =COUNTER_FREQUENCY
	msr	cntfrq_el0, x0			/* Initialize CNTFRQ */
#endif
	b	0f
2:	msr	vbar_el2, x0
	mov	x0, #0x33ff
	msr	cptr_el2, x0			/* Enable FP/SIMD */
	b	0f
1:	msr	vbar_el1, x0
	mov	x0, #3 << 20
	msr	cpacr_el1, x0			/* Enable FP/SIMD */
0:

	/*
	 * Enable SMPEN bit for coherency.
	 * This register is not architectural but at the moment
	 * this bit should be set for A53/A57/A72.
	 */
//这部分代码用于启用 SMPEN 位以实现一致性。对于 A53/A57/A72 架构，将设置 cpuectlr_el1 寄存器中的 SMPEN 位。
#ifdef CONFIG_ARMV8_SET_SMPEN
	switch_el x1, 3f, 1f, 1f
3:
	mrs     x0, S3_1_c15_c2_1               /* cpuectlr_el1 */
	orr     x0, x0, #0x40
	msr     S3_1_c15_c2_1, x0
1:
#endif

	/* Apply ARM core specific erratas */
	bl	apply_core_errata   //这部分代码调用一个函数，用于应用特定于 ARM 核心的勘误。

	/*
	 * Cache/BPB/TLB Invalidate
	 * i-cache is invalidated before enabled in icache_enable()
	 * tlb is invalidated before mmu is enabled in dcache_enable()
	 * d-cache is invalidated before enabled in dcache_enable()
	 */

	/* Processor specific initialization */
	bl	lowlevel_init   //这部分代码调用一个函数，用于处理处理器特定的初始化操作

#if defined(CONFIG_ARMV8_SPIN_TABLE) && !defined(CONFIG_SPL_BUILD)
	branch_if_master x0, x1, master_cpu
	b	spin_table_secondary_jump
	/* never return */
#elif defined(CONFIG_ARMV8_MULTIENTRY)
	branch_if_master x0, x1, master_cpu

	/*
	 * Slave CPUs
	 */
slave_cpu:
	wfe
	ldr	x1, =CPU_RELEASE_ADDR
	ldr	x0, [x1]
	cbz	x0, slave_cpu
	br	x0			/* branch to the given address */
#endif /* CONFIG_ARMV8_MULTIENTRY */
master_cpu: //这部分代码用于处理 ARMv8 多核启动的情况。如果启动代码是在主 CPU 上运行，则跳转到 _main 函数。如果启动代码是在从 CPU 上运行，则进入 WFE（等待事件）状态，等待主 CPU 发送启动信号。
	bl	_main

#ifdef CONFIG_SYS_RESET_SCTRL   //这部分代码用于配置系统的重置控制寄存器。它根据当前的 EL 设置相应的寄存器，并调用 __asm_invalidate_tlb_all 函数来使 TLB（转换查询缓冲器）无效。
reset_sctrl:
	switch_el x1, 3f, 2f, 1f
3:
	mrs	x0, sctlr_el3
	b	0f
2:
	mrs	x0, sctlr_el2
	b	0f
1:
	mrs	x0, sctlr_el1

0:
	ldr	x1, =0xfdfffffa
	and	x0, x0, x1

	switch_el x1, 6f, 5f, 4f
6:
	msr	sctlr_el3, x0
	b	7f
5:
	msr	sctlr_el2, x0
	b	7f
4:
	msr	sctlr_el1, x0

7:
	dsb	sy
	isb
	b	__asm_invalidate_tlb_all
	ret
#endif

/*-----------------------------------------------------------------------*/

WEAK(apply_core_errata)

	mov	x29, lr			/* Save LR */
	/* For now, we support Cortex-A57 specific errata only */

	/* Check if we are running on a Cortex-A57 core */
	branch_if_a57_core x0, apply_a57_core_errata
0:
	mov	lr, x29			/* Restore LR */
	ret

apply_a57_core_errata:

#ifdef CONFIG_ARM_ERRATA_828024
	mrs	x0, S3_1_c15_c2_0	/* cpuactlr_el1 */
	/* Disable non-allocate hint of w-b-n-a memory type */
	orr	x0, x0, #1 << 49
	/* Disable write streaming no L1-allocate threshold */
	orr	x0, x0, #3 << 25
	/* Disable write streaming no-allocate threshold */
	orr	x0, x0, #3 << 27
	msr	S3_1_c15_c2_0, x0	/* cpuactlr_el1 */
#endif

#ifdef CONFIG_ARM_ERRATA_826974
	mrs	x0, S3_1_c15_c2_0	/* cpuactlr_el1 */
	/* Disable speculative load execution ahead of a DMB */
	orr	x0, x0, #1 << 59
	msr	S3_1_c15_c2_0, x0	/* cpuactlr_el1 */
#endif

#ifdef CONFIG_ARM_ERRATA_833471
	mrs	x0, S3_1_c15_c2_0	/* cpuactlr_el1 */
	/* FPSCR write flush.
	 * Note that in some cases where a flush is unnecessary this
	    could impact performance. */
	orr	x0, x0, #1 << 38
	msr	S3_1_c15_c2_0, x0	/* cpuactlr_el1 */
#endif

#ifdef CONFIG_ARM_ERRATA_829520
	mrs	x0, S3_1_c15_c2_0	/* cpuactlr_el1 */
	/* Disable Indirect Predictor bit will prevent this erratum
	    from occurring
	 * Note that in some cases where a flush is unnecessary this
	    could impact performance. */
	orr	x0, x0, #1 << 4
	msr	S3_1_c15_c2_0, x0	/* cpuactlr_el1 */
#endif

#ifdef CONFIG_ARM_ERRATA_833069
	mrs	x0, S3_1_c15_c2_0	/* cpuactlr_el1 */
	/* Disable Enable Invalidates of BTB bit */
	and	x0, x0, #0xE
	msr	S3_1_c15_c2_0, x0	/* cpuactlr_el1 */
#endif
	b 0b
ENDPROC(apply_core_errata)

/*-----------------------------------------------------------------------*/

WEAK(lowlevel_init)
	mov	x29, lr			/* Save LR */

#if CONFIG_IS_ENABLED(IRQ)
	branch_if_slave x0, 1f
	ldr	x0, =GICD_BASE
	bl	gic_init_secure
1:
#if defined(CONFIG_GICV3)
	ldr	x0, =GICR_BASE
	bl	gic_init_secure_percpu
#elif defined(CONFIG_GICV2)
	ldr	x0, =GICD_BASE
	ldr	x1, =GICC_BASE
	bl	gic_init_secure_percpu
#endif
#endif

#ifdef CONFIG_ARMV8_MULTIENTRY
	branch_if_master x0, x1, 2f

	/*
	 * Slave should wait for master clearing spin table.
	 * This sync prevent salves observing incorrect
	 * value of spin table and jumping to wrong place.
	 */
#if defined(CONFIG_GICV2) || defined(CONFIG_GICV3)
#ifdef CONFIG_GICV2
	ldr	x0, =GICC_BASE
#endif
	bl	gic_wait_for_interrupt
#endif

	/*
	 * All slaves will enter EL2 and optionally EL1.
	 */
	adr	x4, lowlevel_in_el2
	ldr	x5, =ES_TO_AARCH64
	bl	armv8_switch_to_el2

lowlevel_in_el2:
#ifdef CONFIG_ARMV8_SWITCH_TO_EL1
	adr	x4, lowlevel_in_el1
	ldr	x5, =ES_TO_AARCH64
	bl	armv8_switch_to_el1

lowlevel_in_el1:
#endif

#endif /* CONFIG_ARMV8_MULTIENTRY */

2:
	mov	lr, x29			/* Restore LR */
	ret
ENDPROC(lowlevel_init)

WEAK(smp_kick_all_cpus)
	/* Kick secondary cpus up by SGI 0 interrupt */
#if defined(CONFIG_GICV2) || defined(CONFIG_GICV3)
	ldr	x0, =GICD_BASE
	b	gic_kick_secondary_cpus
#endif
	ret
ENDPROC(smp_kick_all_cpus)

/*-----------------------------------------------------------------------*/

ENTRY(c_runtime_cpu_setup)
	/* Relocate vBAR */
	adr	x0, vectors
	switch_el x1, 3f, 2f, 1f
3:	msr	vbar_el3, x0
	b	0f
2:	msr	vbar_el2, x0
	b	0f
1:	msr	vbar_el1, x0
0:

	ret
ENDPROC(c_runtime_cpu_setup)

WEAK(save_boot_params)
	b	save_boot_params_ret	/* back to my caller */
ENDPROC(save_boot_params)
#endif

```
```c
这段代码是一个启动代码（reset vector），用于初始化系统并跳转到主程序。

    _start 是程序的入口点，它会根据配置决定是否跳转到 reset 或其他特定的启动代码。
    _TEXT_BASE 是链接地址中代码段的起始地址。
    _end_ofs 和 _bss_start_ofs 是代码段和 BSS 段的偏移地址。
    reset 是启动代码的标签，它会调用 save_boot_params 函数保存重要的寄存器，然后根据系统模式设置处理器状态。
    save_boot_params_ret 是 save_boot_params 函数的返回点。
    pie_fixup 是用于修复位置无关重定位（Position Independent Executable）的代码段的符号。
    reset_sctrl 是用于处理系统控制寄存器的代码段。
    apply_core_errata 是应用 ARM 核心特定勘误的函数。
    lowlevel_init 是系统的低级初始化函数，它会初始化中断控制器。
    smp_kick_all_cpus 是启动所有 CPU 的函数。
    c_runtime_cpu_setup 是用于重新定位 vBAR 寄存器的函数。
    save_boot_params 是保存启动参数的函数。
```