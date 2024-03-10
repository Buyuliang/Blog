---
title: rk3588 bootrom
date: 2024年 03月 09日 星期六 23:49:10 CST
tags: [rk3588]
categories: rk3588
---

### bootRomTable

``` bash
RK3588从内部BootRom启动，通过软件编程支持重新映射功能。
Remap由 PMU_SGRF_SOC_CON2[1:0] 控制。
当remap设置为2'b01时，BootRom不可访问，PMU_SRAM被映射到地址OxFFFF0000。
当remap设置为2'b10时，BootRom是不可访问的，SYSTEM_SRAM被映射到地址OxFFFF0000。
板子一上电，PC就指向0xFFFF0000地址，第一条代码可以在BootRom执行，也可以在PMU_SRAM执行，也可以在SYSTEM_SRAM执行
```
![bootRomTable](/images/RK3588-01-start/bootRomTable.png)

``` bash
RK3588提供从片外设备(如SDMMC卡、eMMC存储器、串行Nand或Nor闪存)启动系统。当这些设备没有准备好启动代码时，也可以通过USB OTG接口提供系统代码下载到这些设备中。所有的引导代码都将存储在内部BootRom中。
下面是整个引导代码的引导过程，引导代码会提前保存在BootRom中。支持以下特性。
- 支持系统从以下设备启动:
    - Serial Nor Flash, 1位或4位数据宽度(FSPI IO中的设备布局)
    - Serial Nand Flash, 1位数据宽度(FSPI IO中的设备布局)
    - eMMC接口，8位数据宽度
    - sdmmc卡，4位数据宽度

    RK3588有两个引导:
    - 非安全世界中的Normal Bootrom，其中CPU_S和CPU_NS都可以访问安全世界中的
    - secure Bootrom，只有CPU_S可以访问

- 支持系统代码通过USB OTG下载
```

### 引导流程

![引导流程](/images/RK3588-01-start/bootstrap-process.png)

### 启动流程

![启动流程](/images/RK3588-01-start/Start-up-process.png)

``` bash
1、上电后， A53核心从0xffff0000这个地址读取第一条指令，这个内部BootROM在芯片出货的时候已经由原厂烧写；

2、然后依次从Nor Flash、Nand Flash、eMMC、SD/MMC获取ID BLOCK，ID BLOCK正确则启动，都不正确则从USB端口下载；

    - 如果eMMC启动，则先读取SDRAM（DDR）初始化代码到内部SRAM，由于SRAM只有192KB，因此最多只能读取那么多，然后初始化DDR，再将eMMC上的代码（剩下的用户代码）复制到DDR运行；
    - 如果从USB下载，则先获取DDR初始化代码，下载到内部SRAM中，然后运行代码初始化DDR，再获取loader代码（用户代码），加载到DDR中并运行；
```
