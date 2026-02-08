华为 VLAN 与接口模式（Access/Trunk/Hybrid）详解
一、VLAN 基础概念
1. 什么是 VLAN？
VLAN（Virtual Local Area Network）：虚拟局域网，将一个物理局域网在逻辑上划分成多个独立的广播域。
核心作用：
隔离广播风暴，提升网络性能
实现业务隔离，增强网络安全
灵活组网，不受物理位置限制
2. VLAN 关键术语
VLAN ID：范围为 1–4094，其中 VLAN 1 是设备默认 VLAN，不可删除。
tagged / untagged：
tagged：数据帧携带 802.1Q 标签，用于在 Trunk 链路上区分不同 VLAN。
untagged：数据帧不携带 802.1Q 标签，通常用于终端设备接入。
PVID（Port Default VLAN ID）：接口的默认 VLAN ID，用于处理接收到的无标签帧，为其打上对应 VLAN 标签。
3. 华为 VLAN 配置命令
bash
运行
# 批量创建 VLAN
vlan batch 10 20 30

# 进入 VLAN 视图，配置 VLAN 名称
vlan 10
 name Office
vlan 20
 name Server
vlan 30
 name IOT
二、接口模式详解（Access / Trunk / Hybrid）
1. Access 模式（接终端）
特点：接口只属于一个 VLAN，收发数据帧均为 untagged 形式。
用途：用于连接 PC、服务器、摄像头等终端设备。
配置示例：
bash
运行
interface GigabitEthernet 0/0/1
 port link-type access
 port default vlan 10
理解：
接口收到无标签帧时，打上 PVID（即 VLAN 10）的标签。
发送 VLAN 10 的帧时，会剥离标签，以 untagged 形式发出。
2. Trunk 模式（交换机互联）
特点：允许多个 VLAN 通过，除了 Native VLAN（默认 VLAN 1）外，其余 VLAN 的帧均以 tagged 形式传输。
用途：用于交换机之间互联，传递多个 VLAN 的流量。
配置示例：
bash
运行
interface GigabitEthernet 0/0/2
 port link-type trunk
 port trunk allow-pass vlan 10 20 30
 port trunk pvid vlan 1  # 定义 Native VLAN，默认为 VLAN 1
理解：
Native VLAN（PVID 对应的 VLAN）的帧以 untagged 形式传输。
其他允许通过的 VLAN（10、20、30）的帧以 tagged 形式传输。
3. Hybrid 模式（华为独有，最灵活）
特点：可自由指定哪些 VLAN 以 tagged 或 untagged 方式通过，兼具 Access 和 Trunk 的特性。
用途：适用于复杂场景，如同一接口同时连接 PC（untagged）和 IP 电话（tagged）。
配置示例：
bash
运行
interface GigabitEthernet 0/0/3
 port link-type hybrid
 port hybrid untagged vlan 10  # PC 使用 untagged 方式
 port hybrid tagged vlan 20    # IP 电话使用 tagged 方式
 port hybrid pvid vlan 10      # 无标签帧归属于 VLAN 10
理解：
收到无标签帧时，打上 PVID（VLAN 10）的标签。
发送 VLAN 10 的帧时，剥离标签（untagged）。
发送 VLAN 20 的帧时，保留标签（tagged）。
三、拓扑与验证
拓扑图


验证命令
bash
运行
# 查看所有 VLAN 信息
display vlan

# 查看接口与 VLAN 的对应关系
display port vlan

# 查看指定接口的详细配置
display interface GigabitEthernet 0/0/1
display interface GigabitEthernet 0/0/2
display interface GigabitEthernet 0/0/3

# 测试连通性
ping 同 VLAN 内其他设备 IP
四、常见故障与排错
同 VLAN 终端无法互通
检查：接口是否加入正确的 VLAN；两端 Trunk 接口是否允许该 VLAN 通过；PVID 是否一致。
跨交换机 VLAN 不通
检查：Trunk 链路是否允许该 VLAN；两端 Trunk 接口的 PVID 是否一致；是否存在 ACL 或防火墙策略拦截。
Hybrid 模式下终端无法通信
检查：tagged/untagged 配置是否与终端类型匹配；PVID 是否正确；是否允许对应 VLAN 通过。
五、记忆口诀
Access：一个 VLAN，全 untagged，接终端。
Trunk：多 VLAN，除 Native 外全 tagged，交换机互联。
Hybrid：灵活控制，想让谁 tagged 谁就 tagged，想让谁 untagged 谁就 untagged。
PVID：无标签帧的 “归属地”，决定其进入哪个 VLAN。
Native VLAN：Trunk 链路上不打标签的那个 VLAN，两端必须一致。
