# WebRTC 伴随手册

> 在做 **Rephone Security**（相机端 App + flutter-webrtc-server 信令服务）过程中，边做边写的 WebRTC 学习笔记与踩坑记录。
> 读者定位：**WebRTC 小白**，从零建立心智模型，并把每个知识点对齐到真实项目代码。

本仓库用 **MkDocs Material** 渲染成电子书（GitHub Pages 发布），Markdown 源码在 [`docs/`](docs/) 目录。

## 在线阅读 / 本地预览

- **在线阅读（GitHub Pages）**：<https://wanghongjie.github.io/webrtc-handbook/>（推送到 `main` 后由 `.github/workflows/pages.yml` 自动构建发布）
- **本地预览**：

  ```bash
  python3 -m venv .venv
  .venv/bin/pip install -r requirements.txt
  .venv/bin/mkdocs serve   # 打开 http://127.0.0.1:8000
  ```

## 章节总目录（正文在 docs/）

| 章节 | 说明 |
| --- | --- |
| [`docs/index.md`](docs/index.md) | 首页：总目录、使用说明、完善进度表 |
| [`docs/01-WebRTC-基础概念.md`](docs/01-WebRTC-基础概念.md) | 基础概念（已填充） |
| [`docs/02-WebRTC-术语速查表.md`](docs/02-WebRTC-术语速查表.md) | 术语速查（基础已填充） |
| [`docs/03-音视频与媒体基础.md`](docs/03-音视频与媒体基础.md) | 轨道/流/编解码/SDP/RTP（骨架） |
| [`docs/04-信令与连接建立.md`](docs/04-信令与连接建立.md) | offer/answer/ICE/信令协议（骨架） |
| [`docs/05-状态机与生命周期.md`](docs/05-状态机与生命周期.md) | 状态枚举/心跳/断线重连（骨架） |
| [`docs/06-项目代码地图.md`](docs/06-项目代码地图.md) | 两端文件索引/调用链（骨架+初步） |
| [`docs/07-问题与坑位记录.md`](docs/07-问题与坑位记录.md) | 踩坑仓库（骨架+示例） |
| [`docs/08-媒体流与渲染链路.md`](docs/08-媒体流与渲染链路.md) | 穿透 Native：stream 创建、textureId↔Surface、音视频如何被消费（已填充） |
| [`docs/09-采集端与数据格式约定.md`](docs/09-采集端与数据格式约定.md) | 采集→编码→RTP/SRTP、数据格式分层、采集端与渲染端的约定（已填充） |

> 说明：正文中提到的 `rephone-security` / `flutter-webrtc-server` 属私有项目代码，仅以路径提示形式出现，用于对齐你自己的项目；`04`/`06` 章提到的"监控端信令连通时序文档"亦为仓库外私有资料，不在本书中。
