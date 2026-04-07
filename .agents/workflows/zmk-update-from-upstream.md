---
description: ZMK 结构与配置的无损合并流程 (Sync upstream ZMK structure without touching Keymap)
---

当你想将上游代码 (main分支) 的最新结构修复引入到 `recovery-code`（或你的定制分支），同时由于手动解决冲突太困难，你想采取直接用上游整体覆盖，再重新把你的 Layer 层按键配置替换进去的方案，请调用此工作流。

## 工作流规则说明

1. 了解上下文：我们有两条主线。`main` 分支是最新上游。`recovery-code` 则是你的定制分支。
2. 操作原理是：
   - 临时导出当前分支的 `config/eyelash_corne.keymap` 文件 和 `README.md` 到临时文件中。
   - 运行 `git checkout main -- .`，覆盖当前分支的所有内容为 `main`。
   - 解析导出的 `eyelash_corne.keymap` 中的 `keymap` 代码块（提取 `default_layer`, `lower_layer`, `raise_layer`, `layer_3` 等层的 `bindings`）。
   - 将工作区中已经被替换为 `main` 最新设定的 `.keymap` 文件里面的对应层 `bindings` 直接用上一步提取出的按键覆盖回来。对于 `sensor-bindings` 等非字母区排布则保持采用最新结构的策略。
   - 将导出的 `README.md` 直接覆盖回来。
   - 保存修改，并提醒用户手动提交 (git commit) 至远端以执行重构编译。

## 适用场景

`/slash-command zmk-update-from-upstream` 当上游（上游作者）又修复了一个构建编译失败的情况或新增了 ZMK 依赖时使用。
