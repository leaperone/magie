# 任务计划：magie-desktop-release

- 任务 ID：`magie-desktop-release-2026-08-01_14-57-19`
- 创建时间：`2026-08-01_14-57-19`

## 目标

在 `leaperone/magie` 建立 Magie 源码仓库与独立的 macOS Desktop 发布链路，发布首个 `desktop-v0.0.1` GitHub Release，并让已打包应用从该 Release 获取增量更新元数据。

## 范围

- 创建并推送公开的 `leaperone/magie` 仓库。
- 将用户已要求的 Magie Desktop/Web branding 纳入发布提交。
- 将上游 T3 Connect/npm/Vercel 发布配置收敛为不依赖这些服务的 Magie Desktop Release workflow。
- 发布两个 macOS 架构的安装与更新资产、合并 `latest-mac.yml`、保留 blockmap 差分下载数据。
- 保留运行时启动后 15 秒检查和每 4 分钟轮询的既有自动检查逻辑。

## 非目标

- 不配置或部署 T3 Connect、Vercel、npm CLI、移动端或 Windows/Linux 的发布。
- 不更改 `t3code://`、数据路径、Bundle ID 等本轮明确保留的兼容标识。
- 不更新或重新导出图标；首版使用仓库现有已跟踪图标资产。

## 关键约束

- 使用独立 worktree，保留 `main` 与现有 branding worktree 的状态。
- 发布 tag 格式为 `desktop-vX.Y.Z`；实际 Electron 版本为 `X.Y.Z`。
- macOS 可安装的自动更新需要有效 Apple 签名；无凭据时只能发布 unsigned 构建，不可称为端到端更新可用。

## 修改路径

- `.github/workflows/{ci,release,deploy-relay}.yml`
- `docs/operations/release.md`
- Desktop 构建/update 配置和对应定向测试（如证据显示需要）。
- 既有 branding worktree 的已验证用户可见文案与图标源/导出物。

## 验证方式

- 定向 workflow 静态检查与现有发布 smoke test。
- Desktop update、build-artifact、branding 定向测试和类型检查。
- GitHub Actions 完成的 tag workflow、Release 资产、`latest-mac.yml` 与 blockmap 的真实检查。
- 在已签名的打包 App 上进行一次更新检查；如缺签名凭据，明确记录为外部阻塞。

## 验收标准

- [x] Release candidate 包含 Magie branding、Desktop-only CI/CD 和发布文档。
- [x] `desktop-vX.Y.Z` workflow 可在无 Apple secrets 时构建 unsigned arm64/x64 macOS 制品。
- [x] workflow 收集 DMG、ZIP、blockmap 并合并 `latest-mac.yml`，且 smoke test 覆盖资产命名与 manifest。
- [x] 已打包 Magie 保留自动检查更新、差分下载元数据和手动安装路径。
- [x] 签名边界已写入 workflow 与发布文档，不把 unsigned 首版描述为端到端自动更新可用。

Tag workflow、GitHub Release 资产和线上状态在提交后执行并记录到最终交付报告，不由 planning 完成状态代替。

## 未确认事项

- Apple Developer 签名/公证凭据当前尚未证实；用户已确认先用现有条件推进，因此首版允许作为 unsigned Release 发布。

## 执行状态

- [x] 完成只读探索并确认真实调用链
- [x] 完成发布配置和品牌改动集成
- [x] 完成当前 release candidate 的定向测试、类型检查、格式与 release smoke
- [x] 完成交付前独立审查与 planning 收敛检查

## 决策

| 决策              | 理由                                                                                             |
| ----------------- | ------------------------------------------------------------------------------------------------ |
| 复用更新运行时    | `DesktopUpdates` 已有启动延迟、轮询、下载和安装状态机，发布只需正确生成 GitHub provider 元数据。 |
| Desktop-only 发布 | 当前用户场景是 macOS App；避免把不具备凭据的 relay/npm/Vercel 发布链路带入首版。                 |
| 首版沿用现有图标  | 用户明确要求不要更新图标；发布 workflow 不再把 Icon Composer 导出工具作为制品构建门槛。          |

## 错误与处理

| 错误                             | 尝试 | 处理结果                                         |
| -------------------------------- | ---: | ------------------------------------------------ |
| `leaperone/magie` GitHub API 404 |    1 | 创建 public repository 后已解决。                |
| Icon Composer GUI 导出           |    1 | 用户决定首版沿用现有图标，本任务不再依赖该工具。 |
