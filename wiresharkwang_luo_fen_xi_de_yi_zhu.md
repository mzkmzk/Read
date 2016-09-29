# Wireshark网络分析的艺术

## 1. 读者问

### 来点有深度的

延迟确认发生太多会影响性能

可通过`tcp.analysis.ack_rtt > 0.2 and tcp.len ==0`来显示出有可能延迟确认带来的包

