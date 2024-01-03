---
title: PCIe
date: 2023年12月25日 11:01:14
tags: [PCIe]
categories: PCIe
---


### TLP
``` bash
TLP(Transaction Layer Packet): TLP包是由PCIe的Endpoint或者Root Complex发送的数据包。在PCIe体系中的事务层生成;TLP包由头（Hander）、数据（Data）、ECRC（校验）几个部分组成。TLP是用户程序和PCIe设备交互的唯一渠道.。
```

### DBI bus
``` bash
PCIe DBI（Device Bus Interface）总线是 PCIe 设备和主机之间进行数据交换和通信的总线。DBI 总线在 PCIe 基本配置空间之外，提供了一种直接的、低延迟的数据传输通道，用于设备和主机之间的寄存器读写操作。
DBI 总线是由 PCIe 设备上的端点功能提供的，主机通过 PCIe 配置事务（Configuration Transaction）将数据发送到设备的 DBI 总线上，并通过 DBI 总线接收来自设备的响应。
PCIe DBI 总线主要用于以下操作：

    寄存器读写：通过 DBI 总线，主机可以直接读写设备的特定寄存器，包括设备状态、配置寄存器和其他设备特定寄存器。
    中断：设备可以通过 DBI 总线向主机发送中断请求，以通知主机发生了特定事件，例如数据传输完成、错误发生等。
    错误报告：当设备发生错误时，可以使用 DBI 总线将错误信息传送给主机，以便主机能够适时进行错误处理和恢复。

总之，PCIe DBI 总线是 PCIe 设备和主机之间进行寄存器读写、中断和错误报告的通道。通过使用 DBI 总线，设备可以直接与主机进行通信，提供了高效、低延迟的数据传输和控制机制。
```

### BAR 
``` bash
在 PCIe（Peripheral Component Interconnect Express）中，BAR（Base Address Register）寄存器用于配置设备的基地址。每个 PCIe 设备都有一组 BAR 寄存器，用于指定设备对应的内存或 I/O 资源的起始地址。

BAR 寄存器是用来告诉主机系统设备需要的内存或 I/O 资源的大小和位置。主机系统在初始化阶段会读取设备的配置空间，并解析 BAR 寄存器的值来分配和映射资源。

以下是一些常见的 BAR 寄存器及其作用：

1. BAR0 - BAR5：
这些寄存器用于指定设备的基地址。每个 BAR 寄存器通常是 32 位或 64 位宽度，取决于设备的需求。BAR0 通常用于指定设备的主要内存或 I/O 资源。

2. BAR 控制位：
BAR 寄存器的最低位用于指定地址空间的类型，例如内存空间还是 I/O 空间。此外，还可以使用其他控制位来启用或禁用 BAR。

3. BAR 长度位：
BAR 寄存器的高位用于指定设备所需的地址空间大小。这些位用于告知主机系统分配多少地址空间给设备。

通过配置 BAR 寄存器，设备可以告知主机系统其对内存或 I/O 资源的需求，并与主机系统进行资源分配和映射。具体的 BAR 寄存器配置方式和含义可能因设备类型和规范而异，因此建议参考相关文档、规格书或硬件设计指南，以了解特定设备的 BAR 寄存器的使用方法和作用。
```

### inbound/outbound
```bash
在 PCIe（Peripheral Component Interconnect Express）中，配置 inbound 和 outbound 是指配置 PCIe 设备的传输方向和流量控制。

1. Inbound 配置：
Inbound 配置用于定义 PCIe 设备接收主机传输的数据流。当主机向设备发送请求时，设备需要配置 inbound 来接收请求并提供响应。这包括配置设备的接收队列、缓冲区和数据路径，以确保设备能够正确接收和处理主机的传输。

2. Outbound 配置：
Outbound 配置用于定义 PCIe 设备向主机传输的数据流。当设备需要向主机发送数据时，需要配置 outbound 来指定数据的传输路径和流量控制。这包括配置设备的发送队列、缓冲区和数据路径，以确保设备能够按需发送数据给主机。

通过配置 inbound 和 outbound，设备可以与主机进行双向数据传输，并确保数据的可靠性和效率。这些配置通常在 PCIe 设备的寄存器中进行，具体的配置方式和含义可能因设备类型、规范和应用而异。因此，建议参考相关文档、规格书或硬件设计指南，以了解特定设备的 inbound 和 outbound 配置的使用方法和作用。
```
### IATU
PCIe IATU（Address Translation Unit）寄存器用于配置 PCIe 设备的地址转换。IATU 是用于实现 PCIe 配置空间和内存空间之间的地址映射和转换的硬件单元。

以下是一些常见的 PCIe IATU 寄存器及其作用：
``` bash
1. ATU Viewport Register（ATUVR）：
用于选择当前配置的 IATU 视图，并控制对应的 IATU 寄存器的访问。

2. ATU Control Register（ATUCR）：
包含控制 IATU 行为的位字段，例如启用/禁用 IATU、选择地址映射模式等。

3. ATU Lower Base Address Register（ATULBAR）和 ATU Upper Base Address Register（ATUUBAR）：
用于指定 IATU 的基础地址，即起始地址。

4. ATU Lower Target Address Register（ATULTAR）和 ATU Upper Target Address Register（ATUUTAR）：
用于指定目标地址，即映射后的地址。

5. ATU Region Control 1 Register（ATURC1）和 ATU Region Control 2 Register（ATURC2）：
包含其他与地址转换相关的配置信息，如启用/禁用映射、选择地址映射大小等。

通过配置这些寄存器，可以实现 PCIe 设备的地址映射和转换，使得设备可以正确访问和使用系统内存或其他外设资源。
TLP中的地址哪里来？ATU转换过来的。ATU是一个地址转换单元，负责将一段存储器域的地址转换到PCIe总线域地址，除了地址转换外，还能提供访问类型等信息，这些信息都是ATU根据总线上的信号自己做的，数据都打包到TLP中，不用软件参与。

软件需要做的是配置ATU，所以如果ATU配置完成，并且能正常工作，那么CPU访问PCIe空间就和访问本地存储器空间方法是一样的，只要读写即可。
```

### APB
``` bash
PCIe (Peripheral Component Interconnect Express) 和 APB (Advanced Peripheral Bus) 是两种不同的总线协议，用于设备之间进行通信。
PCIe 是一种高速串行总线协议，用于连接计算机系统中的各种外设和扩展卡。它提供了高带宽和低延迟的数据传输，适用于高性能设备和系统。PCIe 总线在现代计算机系统中广泛应用，例如图形卡、网络适配器、存储控制器等设备都可以使用 PCIe 接口进行连接。
APB 是一种串行总线协议，通常用于片上系统 (SoC) 中的外设和芯片之间的通信。APB 总线是 ARM 公司提出的一种低功耗、低带宽的总线协议，主要用于连接低复杂度的外设，如 GPIO 控制器、串口控制器等。APB 总线相对于 PCIe 总线来说，传输速度较低，适合于对带宽和延迟要求不高的应用场景。
在某些情况下，PCIe 接口可以用于连接到 APB 总线的外设。这样做的目的是通过 PCIe 总线的高带宽和低延迟特性，提高对 APB 外设的访问性能。通过 PCIe-APB 桥接器，可以将 PCIe 总线上的数据传输转换为 APB 总线上的数据传输，以实现 PCIe 和 APB 之间的互连。
总而言之，PCIe 是一种高速、高性能的计算机总线协议，而 APB 是一种低功耗、低带宽的片上系统总线协议。PCIe 可以用于连接计算机系统中各种高性能外设，而 APB 则主要用于连接低复杂度的外设和芯片之间的通信。在特定情况下，可以使用 PCIe-APB 桥接器将 PCIe 总线连接到 APB 总线上的外设，以提高访问性能。
```

### ELBI
``` bash
外部本地总线接口。当期望控制器生成该寄存器的PCIe完成时，将控制器接收到的入站寄存器RD/WR发送到外部应用程序寄存器
RD /弯角。对于交换机应用程序:ELBI用于针对本地交换机应用程序寄存器的传入请求，而TRGT1用于通过交换机的tlp。控制器自动为路由到ELBI的请求生成完成,ELBI可以理解为通过PCIE访问一个CPU的内部空间寄存器。ELBI 主要是在EP端，使用RC没有该部分功能。
```

### PCIe IB (Inbound) 和 PCIe OB (Outbound)
``` bash
PCIe IB (Inbound) 和 PCIe OB (Outbound) 是PCIe总线中的两个概念，用于描述数据传输的方向。

1. PCIe IB (Inbound)：
   - PCIe IB 是指数据从外部设备（如扩展卡）进入计算机系统，通过PCIe总线传输到主机内存或其他目标。
   - 例如，当外部设备向主机发送数据时，数据的传输方向是PCIe IB。
   - 在Windows操作系统中，PCIe IB 数据的接收和处理通常由设备驱动程序负责。

2. PCIe OB (Outbound)：
   - PCIe OB 是指数据从计算机系统中的主机内存或其他源出发，通过PCIe总线传输到外部设备。
   - 例如，当主机向外部设备发送数据时，数据的传输方向是PCIe OB。
   - 在Windows操作系统中，PCIe OB 数据的发送通常由设备驱动程序负责。

对于PCIe设备和Windows操作系统，数据的传输方向（IB或OB）取决于具体的应用场景和设备驱动程序的实现。设备驱动程序负责管理和控制数据在PCIe总线上的传输，以确保可靠的数据交换。
```
