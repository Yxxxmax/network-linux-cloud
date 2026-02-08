# 华为 VLAN 与接口模式（Access/Trunk/Hybrid）详解

## 一、VLAN 基础概念

### 1. 什么是 VLAN？
- **VLAN（Virtual Local Area Network）**：虚拟局域网，将一个物理局域网在逻辑上划分成多个独立的广播域。
- 核心作用：
  - 隔离广播风暴，提升网络性能
  - 实现业务隔离，增强网络安全
  - 灵活组网，不受物理位置限制

### 2. VLAN 关键术语
- **VLAN ID**：范围为 1–4094，其中 VLAN 1 是设备默认 VLAN，不可删除。
- **tagged / untagged**：
  - **tagged**：数据帧携带 802.1Q 标签，用于在 Trunk 链路上区分不同 VLAN。
  - **untagged**：数据帧不携带 802.1Q 标签，通常用于终端设备接入。
- **PVID（Port Default VLAN ID）**：接口的默认 VLAN ID，用于处理接收到的无标签帧，为其打上对应 VLAN 标签。
