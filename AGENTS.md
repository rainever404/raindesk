# RainDesk 代理指南（中文翻译）

## 项目身份

RainDesk 是基于上游项目 [`rustdesk/rustdesk`](https://github.com/rustdesk/rustdesk.git) 的二次开发分支。

本仓库是 RainDesk 的长期维护代码库。未来开发应将 `RainDesk` 作为产品名称使用，并尽量避免重新引入 RustDesk 品牌，除非出于上游归属、许可证声明或与上游代码注释兼容的需要。

保持与上游兼容性。在可能的情况下，将 RainDesk 的修改保持为小且易审查的补丁，以便未来从 RustDesk 上游进行 rebase 或 cherry-pick 时仍然可行。

## 项目布局

### 目录结构

* `src/` Rust 应用代码
* `src/server/` 音频 / 剪贴板 / 输入 / 视频 / 网络
* `src/platform/` 平台相关代码
* `src/ui/` 旧版 Sciter UI（已弃用）
* `flutter/` 当前 UI
* `libs/hbb_common/` 配置 / proto / 公共工具
* `libs/scrap/` 屏幕捕获
* `libs/enigo/` 输入控制
* `libs/clipboard/` 剪贴板
* `libs/hbb_common/src/config.rs` 所有选项

### 子模块维护

* `libs/hbb_common` 使用 RainDesk fork：`https://github.com/rainever404/hbb_common.git`
* RainDesk 对 `hbb_common` 的自定义修改先提交并推送到该 fork，再在主仓库提交子模块指针。
* 上游 `rustdesk/hbb_common` 仅作为同步来源，避免直接向上游推送 RainDesk 私有修改。

### 核心组件

* **远程桌面协议**：在 `src/rendezvous_mediator.rs` 中实现自定义协议，用于与 rustdesk-server 通信
* **屏幕捕获**：平台相关的屏幕捕获实现在 `libs/scrap/`
* **输入处理**：跨平台输入模拟在 `libs/enigo/`
* **音视频服务**：实时音视频流在 `src/server/`
* **文件传输**：安全文件传输实现于 `libs/hbb_common/`

### UI 架构

* **旧版 UI**：基于 Sciter（已弃用），文件在 `src/ui/`
* **现代 UI**：基于 Flutter，文件在 `flutter/`
  * 桌面端：`flutter/lib/desktop/`
  * 移动端：`flutter/lib/mobile/`
  * 公共部分：`flutter/lib/common/` 和 `flutter/lib/models/`

## RainDesk 开发规则

* 用户可见文本、构建元数据、包名、安装程序文字、桌面条目、图标和 Flutter UI 字符串尽量使用 RainDesk 名称。
* 保留上游 RustDesk 的许可证头和归属信息。
* 自定义 RainDesk 行为要易于识别和审查。
* 避免大规模重写，当小补丁即可解决时。
* 未明确需要时不要增加依赖。

## Rust 编码规则

* 避免在生产代码中使用 `unwrap()` / `expect()`
  * 例外：测试或锁获取失败仅表示锁被破坏，而不是正常流程。
* 其他情况优先使用 `Result` + `?` 或显式处理错误。
* 不要静默忽略错误。
* 避免不必要的 `.clone()`
* 能用借用时尽量使用借用。
* 未必要时不要增加依赖。
* 保持代码简单、符合惯用写法。

## Tokio 使用规则

* 假设已存在 Tokio runtime。
* 不要创建嵌套 runtime。
* async 代码中不要调用 `Runtime::block_on()`。
* 不要在 `.await` 期间持有锁。
* 优先使用 `.await`、`tokio::spawn`、channels。
* 阻塞工作使用 `spawn_blocking` 或专用线程。
* async 代码中不要使用 `std::thread::sleep()`。

## 编辑规范

* 仅修改必要内容。
* 优先最小有效 diff。
* 不要重构无关代码。
* 避免仅格式化修改。
* 命名/风格与周围代码保持一致。
* 编辑前阅读周围代码，特别是 `libs/hbb_common`、`src/server` 和 `flutter/lib`。
* 每完成关键步骤后尽量验证构建或运行针对性测试。
