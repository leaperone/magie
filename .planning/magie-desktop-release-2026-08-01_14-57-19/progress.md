# 执行进度：magie-desktop-release

- 任务 ID：`magie-desktop-release-2026-08-01_14-57-19`
- 创建时间：`2026-08-01_14-57-19`
- 当前状态：`ready_to_release`

## 已完成

- 确认 `leaperone` 组织存在、当前账号拥有管理员成员资格；目标仓库尚不存在。
- 确认现有源码有成熟的 GitHub Release 更新资产生成和客户端自动检查状态机。
- 确认上游 release/relay workflow 不可原样用于 Magie。
- 创建公开的 `https://github.com/leaperone/magie`；Actions 已启用。
- 创建发布专用 worktree 并迁入计划，集成既有 Magie branding 改动。
- 用标准 GitHub runner 的 CI 和 `desktop-vX.Y.Z` macOS 双架构 release workflow 替换上游依赖服务的配置，并移除自动 relay deploy。
- 将稳定发行包名改为 `Magie-*`，保持 updater manifest、blockmap 和 GitHub provider 机制。
- 将更新完成后的 Release notes 链接改为 `leaperone/magie/releases/tag/desktop-vX.Y.Z`。
- 在最新 `origin/main` 基线上通过 113 项 Desktop/Web/scripts 定向测试和三包 typecheck；release smoke 在 rebase 前通过，发布相关文件在 rebase 中无冲突。
- 独立审查复核 URL 修复后无剩余代码级 Critical/High；unsigned 首版作为用户已接受的发布边界保留。

## 下一步

- 提交并推送 `main`，等待 CI 后创建并监督 `desktop-v0.0.1`。

## 修改文件

- `.github/workflows/ci.yml`、`.github/workflows/release.yml`、`.github/workflows/deploy-relay.yml`、`docs/operations/release.md`、Desktop artifact naming 及已验证 branding 改动。

## 验证结果

| 检查             | 结果                                                            | 状态 |
| ---------------- | --------------------------------------------------------------- | ---- |
| GitHub 仓库查询  | public `leaperone/magie`，默认分支 `main`                       | 通过 |
| 现有更新链路     | GitHub provider、manifest、blockmap、轮询和 UI 下载/安装均存在  | 通过 |
| GitHub 仓库创建  | public `leaperone/magie`，Actions enabled / allowed_actions=all | 通过 |
| 定向代码验证     | 10 files / 113 tests；Desktop/Web/scripts typecheck；diff check | 通过 |
| Release URL 修复 | 2 files / 34 tests；Web typecheck；format check                 | 通过 |
| 现有图标边界     | 用户确认首版不更新图标；Release 使用已跟踪图标资产              | 通过 |

## 错误与恢复

| 错误                                | 尝试 | 解决方式                                                                                  |
| ----------------------------------- | ---: | ----------------------------------------------------------------------------------------- |
| `orca computer` runtime unavailable |    1 | 已按 skill 启动 Orca，但 runtime metadata 仍不存在；停止重试，不以非原生图替代。          |
| GitHub Actions secrets API 权限不足 |    1 | token 不能列出组织 secrets；未创建或伪造签名材料，保留为用户需配置的外部凭据。            |
| LEAPERone 图像接口生成              |    2 | 已生成一张本地概念图；用户决定首版不更新图标，`output/` 不纳入发布提交。                  |
| rebase 后依赖补齐                   |    2 | npm 网络下载可选平台包超时；改用已安装的直接测试/typecheck 二进制完成当前 HEAD 定向验证。 |
