---
title: PCIe
date: 2023年12月25日 11:01:14
tags: [PCIe]
categories: PCIe
---

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
```