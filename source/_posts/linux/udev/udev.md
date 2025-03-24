---
title: udev
date: 2025年 3月24日 星期一 17时11分32秒 CST
tags: [udev]
categories: udev
---

## 使用 udevadm monitor 实时监控事件

```bash
udevadm monitor 可以实时监控 udev 事件。
监控所有事件：

sudo udevadm monitor

监控特定子系统（如 udc 或 platform）：

sudo udevadm monitor --subsystem-match=udc,platform


日志：
[来源][时间戳] 动作   设备路径 (子系统)
KERNEL[3477.342949] remove   /devices/platform/fec80000.i2c/i2c-6/6-0022/typec/port1/port1-partner (typec)
UDEV  [3477.343654] remove   /devices/platform/fec80000.i2c/i2c-6/6-0022/typec/port1/port1-partner (typec)
KERNEL[3477.347728] change   /devices/platform/fec80000.i2c/i2c-6/6-0022/typec/port1 (typec)
UDEV  [3477.347980] change   /devices/platform/fec80000.i2c/i2c-6/6-0022/typec/port1 (typec)
KERNEL[3482.738417] change   /devices/platform/fec80000.i2c/i2c-6/6-0022/typec/port1 (typec)
UDEV  [3482.741120] change   /devices/platform/fec80000.i2c/i2c-6/6-0022/typec/port1 (typec)
KERNEL[3483.054324] add      /devices/platform/fec80000.i2c/i2c-6/6-0022/typec/port1/port1-partner (typec)
UDEV  [3483.055604] add      /devices/platform/fec80000.i2c/i2c-6/6-0022/typec/port1/port1-partner (typec)

```
