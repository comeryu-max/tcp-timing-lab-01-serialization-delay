[English](./README.md) | [简体中文](./README-zh-Hans.md)
🌌 TCP Timing Lab 01
Observing Transmission Delay (Serialization Delay)
观测传输时延（串行化时延）

The network transmits bits, not packets.
网络传输的是比特，而不是数据包。

From theory to wire — making L / R visible.
从理论走向链路 —— 让 L / R 可见

🧭 Overview｜概述

Transmission Delay (Serialization Delay) is the time required to place a full frame onto the wire.
传输时延（串行化时延） 是将一个完整帧推上链路所需的时间。

In Computer Networking: A Top-Down Approach:

在经典教材《Computer Networking: A Top-Down Approach》中：

L / R
L: packet length (bits)
R: link rate (bps)

💡

Everyone knows this formula.
Almost no one has ever seen it.

每个工程师都“知道”这个公式
但几乎没人真正“看见过它”

👉 This lab turns theory into observation
👉 本实验让理论变成“可观测现实”

🧠 Core Insight｜核心洞察
Transmission Delay = Serialization Delay = Δt

What you see in packet capture is not an approximation.
It is the wire.

你在抓包中看到的不是近似值
它就是链路本身

🎯 Objective｜实验目标
Make Transmission Delay observable
👉 让传输时延“可见”
Map L / R → Δt
👉 建立 L / R → Δt 映射
Validate theory with real packets
👉 用真实报文验证理论
🧪 Experiment Setup｜实验环境

✅ Measurement Validity｜测量有效性

This is not an approximation.
This is a direct physical observation.

这不是近似
这是对物理世界的直接观测

① No Intermediate Devices｜无中间设备
No switch / router
No queue / buffer / scheduling

👉 消除所有排队与处理时延

② Negligible Propagation Delay｜传播时延可忽略
Short cable
Nanosecond-level delay

👉 相比 ms 级串行化时延可忽略

③ Passive TAP Observation｜TAP无侵入观测
No packet modification
No timing distortion

👉 只复制信号，不改变时间结构

④ Hardware Timestamp Capture｜硬件时间戳
Microsecond precision
No packet loss

👉 Δt = 真实链路时间

⑤ Δt = Serialization Delay
Δt ≈ (Frame + Preamble + IFG) / Link Rate

This is not a model of the network
This is the network itself

🚂 Packet Train｜报文列车
Making Time Visible｜让时间结构显现
<pre> Frame1 Frame2 Frame3 Frame4 |------| |------| |------| |------| Δt Δt Δt </pre>

You are not seeing packets
You are seeing time

你看到的不是“包”
而是“时间结构”

📊 Key Observation｜关键观测
Link Speed	Δt
10 Mbps	≈ 1.23 ms
100 Mbps	≈ 0.123 ms
1 Gbps	≈ 0.012–0.013 ms
📈 Experimental Results｜实验结果
10 Mbps

Δt ≈ 1.23 ms
→ Direct physical manifestation of L / R

100 Mbps

Δt scales linearly with link rate
与 L / R 完全一致

1 Gbps

Limited by timestamp precision
受时间戳精度限制

📐 Theory Meets Reality｜理论与现实
Δt = 1538 × 8 / R

Example (10 Mbps):

≈ 1.23 ms

Theory = Measurement
理论 = 观测

🔧 Method｜方法论
Build ideal environment
👉 构建“纯净实验环境”
Use TAP
👉 无侵入观测
Use analyzer
👉 高精度采集
Measure Δt
👉 直接测时间
❗ Misconception｜常见误区

Transmission Delay is theoretical

❌ Wrong
✔ It is physical and deterministic

传输时延只是理论

❌ 错误
✔ 它是确定性的物理时间

🚀 Conclusion｜结论

L / R is not just a formula
It is a measurable physical reality

L / R 不是公式
它是可以被观测的物理现实

If you can measure Δt
you are not reasoning about the network
you are observing it

当你测量 Δt
你不是在“推理网络”
而是在“观测网络”

🧩 Why It Matters｜为什么重要

The network is a time-structured system
网络本质是一个时间结构系统

Implications：

Packet Train = physical pacing
ACK Clock = time-driven system
Throughput ≠ Bandwidth
🔁 Engineering Impact｜工程意义

This lab is the foundation of:

Packet Train
ACK Clock
Throughput anomalies
Congestion behavior

👉 本实验是以下一切的基础：

报文列车
ACK Clock
吞吐异常
拥塞行为
🔗 Next Lab
👉 Lab 02 — Serialization vs Throughput

When serialization delay is fixed
why does throughput fluctuate?

当串行化时延固定时
为什么吞吐会变化？

🌌 Final Statement｜最终表达

This is not a visualization
This is a measurement

这不是可视化
这是测量
