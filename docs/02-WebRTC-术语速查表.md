# 02 WebRTC 术语速查表

> 状态：🟡 基础已填充（持续追加） ｜ 读者：WebRTC 小白
> 用法：学习中遇到不认识的词 → 在本表搜索。表中【本项目】列指到具体代码位置，帮助把概念落到实处。
> 维护：新术语按字母顺序插入对应分区，并在 README 进度表登记。

---

## A. 媒体与采集

| 术语 | 一句话解释 | 补充说明 | 本项目位置 |
| --- | --- | --- | --- |
| **Track（轨道）** | 一路独立的音/视频信号（一路摄像头画面 / 一路麦克风） | 有 kind：`audio` / `video`；有 enabled（开关静音就是置 false） | `muteMic()/setMicEnabled()` |
| **Stream（流）** | 若干 Track 的容器，一个 Stream 可含多路 audio/video | 远端来的流通过 `pc.onTrack` 的 `event.streams[0]` 拿到 | `_localStream`、`_remoteStreams` |
| **getUserMedia** | 浏览器/App 请求摄像头麦克风权限并开始采集 | 需要 HTTPS 或 localhost；App 端就是系统权限 | `createStream()` 第 607 行 |
| **facingMode** | 指定前摄(`user`)还是后摄(`environment`) | 本项目切换摄像头时翻转该值并重启采集 | `switchCamera()` |
| **编解码器(Codec)** | 把原始画面/声音压缩成比特流（编码）和解开（解码）的算法 | 视频常见 H.264/VP8/VP9/AV1；音频常见 Opus/G.711 | 由 SDP 协商自动选择，代码基本不感知 |
| **分辨率/帧率/码率** | 画面大小 / 每秒帧数 / 每秒比特量 | 码率=分辨率×帧率×压缩率，是"画质-带宽"权衡 | 约束写在 `createStream()` mandatory 中 |

## B. 传输与连接

| 术语 | 一句话解释 | 补充说明 | 本项目位置 |
| --- | --- | --- | --- |
| **RTCPeerConnection (PC)** | WebRTC 最核心对象：管发送、接收、加密、状态 | 新建=建一次 P2P 连接，用完要 close+dispose | `_createSession()` 第 641 行 |
| **SDP（Session Description Protocol）** | 一段描述"媒体能力/方向/地址"的文本协议 | 不是传内容的协议，而是"自我介绍书" | `description:{sdp,type}` 在 offer/answer 里互传 |
| **Offer / Answer** | 建连协商的两次握手（能力清单与回应） | offer 由主叫发，answer 由被叫回 | `_createOffer()` / `_createAnswer()` |
| **renegotiation（重协商）** | 连接建立后再改媒体（如新增麦克风轨）而重新 offer/answer | 同一 Session 复用 PC，不新建 | `setMonitorTalkbackEnabled()` |
| **ICE** | 找通路的"决策算法"：收集候选→两两连通性测试→选最优 | RFC 8445 | `pc.onIceCandidate` |
| **ICE Candidate** | 一条可能通路的地址（host/srflx/relay 三种） | 通过信令互发 | `_send('candidate', ...)` |
| **STUN** | 查自己公网映射地址的服务器（不转发媒体） | 解决"我在 NAT 后面"问题的一半 | `stun:stun.l.google.com` |
| **TURN** | 打洞失败时转发媒体的中继服务器 | 需要用户凭据；媒体会过它，带宽成本高；详见 `04` 章 4.0.3 | `/api/turn` 动态签发 |
| **trickle ICE** | 候选边发现边发，不用等收集完 | 显著加快建连 | onIceCandidate 里逐条发送 |
| **DTLS** | 数据报传输层安全：PC 上做密钥协商/加密握手 | DataChannel 也走它加密 | PC 内部自动 |
| **SRTP** | 安全实时传输协议：媒体流的加密传输层 | DTLS 握手后派生密钥给它 | PC 内部自动 |
| **RTP / RTCP** | 实际传输媒体包的协议 / 反馈统计与控制的兄弟协议 | 媒体=一堆 RTP 包；RTCP 带丢包率等 | 不可见（native 层） |

## C. 信令与会话

| 术语 | 一句话解释 | 补充说明 | 本项目位置 |
| --- | --- | --- | --- |
| **信令(Signaling)** | WebRTC 之外双方"认识彼此/交换 SDP 与候选"的机制 | WebRTC 规范故意不管它，可自选（WS/HTTP/...） | `signaling.dart` |
| **SignalingServer** | 转发信令小纸条的中间人 | 本项目 = flutter-webrtc-server `/ws` | `signaler.go` |
| **Peer（对端）** | 信令层面的一个在线节点，用 ID 标识 | 服务端 peers 表 = 在线名册 | `peers map[string]Peer` |
| **Session（会话）** | 一对 peer 之间的一次呼叫关系，客户端自管 | 服务端不登记会话，只认 `session_id` 转发 | 客户端 `_sessions` |
| **Offerer / Answerer** | 主叫（发 offer）/ 被叫（回 answer） | 与本项目"谁出画面"无关 | 监控端主叫 |
| **Unified Plan** | SDP 规范：一条 m-line 一个 transceiver（现代标准） | 旧 plan-b 已废弃，本项目用 unified-plan | `sdpSemantics` |

## D. 状态与生命周期

| 术语 | 一句话解释 | 补充说明 | 本项目位置 |
| --- | --- | --- | --- |
| **SignalingState** | 信令连接状态（Open/Closed/Error） | 客户端自定义枚举 | `signaling.dart` 顶部 |
| **CallState** | 呼叫状态机（New/Ringing/Invite/Connected/Bye） | 客户端自定义枚举 | `signaling.dart` 顶部 |
| **ICE connection state** | P2P 通路状态（new/checking/connected/completed/...） | 排查黑屏关键状态 | `pc.onIceConnectionState` |
| **Keepalive（心跳）** | 周期性发的小消息保持连接/探活 | 本项目 15s 一次，发送失败触发重连 | `_startKeepalive()` |
| **心跳回显** | 服务器收到 keepalive 原样返回 | 客户端只打日志不回校验 | `signaler.go` `case Keepalive` |

## E. 网络/部署常识

| 术语 | 一句话解释 | 补充说明 | 本项目位置 |
| --- | --- | --- | --- |
| **NAT** | 路由器把内网 IP 映射到公网的行为 | 是 P2P 的头号障碍 | — |
| **WebSocket** | 全双工长连接，用于本项目信令传输 | 与 WebRTC 是两回事，别混 | `websocket.dart` |
| **HTTPS/TLS** | 加密的 HTTP / 通用加密传输层 | 本项目采集和 WS 均需 TLS（含自签放行） | `badCertificateCallback=true` |
| **HTTPS vs WSS** | 都跑在 TLS 上；HTTPS=HTTP over TLS（请求-响应），WSS=WebSocket over TLS（全双工长连接） | `s` 才是 TLS，前面的 `http`/`ws` 是说话方式；详见 `04` 章 4.0 | `/api/turn` 用 HTTPS、`/ws` 用 WSS |
| **SFU / MCU** | 多方场景的媒体服务器（选择性转发/混合） | 本项目 1v1 P2P 用不到 | — |
| **Mesh（全网状）** | N 方两两互联的拓扑，N 小可用 | 本项目 1v1 即最简单 Mesh | — |

## F. 本项目自定义词

| 词 | 含义 | 出现位置 |
| --- | --- | --- |
| `device_id` | 设备在信令里的身份，格式 `{8位hash}_{camera\|monitor}`，持久化、不含 `-` | `device_info.dart` |
| `session_id` | `主叫ID-被叫ID`，服务端用 `-` 拆分决定给谁发 bye | `signaling.dart invite()` |
| `media: 'video' / 'data'` | 会话类型：带音视频 / 纯数据通道 | `invite(peerId, media, ...)` |
| `fileTransfer` | 本项目 DataChannel 的 label，承载控制 JSON 与录像二进制 | `_createDataChannel()` |

---

### 仍待补充的术语（TODO）
- [ ] AVPF / NACK / FEC（抗丢包机制）
- [ ] SIMULCAST（多分辨率同时上行）
- [ ] mDNS / ICE 候选的隐私化
- [ ] Opus 的 DTX / 舒适噪声
- [ ] TURN REST API（draft-uberti-behave-turn-rest）
