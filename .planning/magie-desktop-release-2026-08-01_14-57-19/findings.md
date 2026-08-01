# 调研与结论：magie-desktop-release

- 任务 ID：`magie-desktop-release-2026-08-01_14-57-19`
- 创建时间：`2026-08-01_14-57-19`

## 需求事实

- 用户要求核实 `leaperone/magie`、配置 CI/CD 与 release actions，并发布 `desktop-v0.0.1`。
- 用户此前已要求把 macOS Dev App 及内嵌 Web branding 更名为 Magie，并用本地 Icon Composer 资产生成 M 图标。

## 真实调用链

- `origin` 指向已创建的 public `https://github.com/leaperone/magie.git`；默认分支 `main` 与本地 `origin/main` 均为 `0ad91b6e7`。
- 现有 `.github/workflows/release.yml` 是上游通用发布图，依赖 T3 Connect、npm、Vercel、GitHub App 和 Blacksmith runner，不适合直接在新仓库执行。
- `scripts/build-desktop-artifact.ts` 会从 `GITHUB_REPOSITORY`（或 `T3CODE_DESKTOP_UPDATE_REPOSITORY`）写入 electron-updater 的 GitHub provider；它会产出 macOS ZIP、`latest-mac.yml` 和 blockmap。
- `DesktopUpdates` 现已实现包装 App 的启动延迟、每 4 分钟轮询、按需下载和重启安装。

## 调研结论

- 使用 tag `desktop-vX.Y.Z` 可与历史的 `vX.Y.Z` 上游发布区隔；workflow 需将它解析为 Electron `X.Y.Z`。
- 原 release workflow 的 schedule 和 relay push deploy 不能保留，否则新仓库会因不存在的生产凭据/runner 而持续失败。
- 用户已确认首版不更新图标，继续使用仓库现有已跟踪 PNG/ICO 图标资产。
- `leaperone/magie` 已于本轮创建为 public repository；其 Actions 处于 enabled / allowed_actions=all。当前 token 无法列出或写入组织级 Actions secrets，因此未能验证或配置 Apple 签名凭据。
- 新 release workflow 仅由 `desktop-vX.Y.Z` tag 触发，使用标准 `ubuntu-24.04` 和 `macos-26` runners；与原来的 T3 Connect、npm、Vercel 和 Blacksmith 配置完全隔离。
- `icons:check` 依赖 Icon Composer 2+ 且不参与实际打包；首版使用现有图标时不应让该工具可用性阻塞 Release。
- 独立审查发现更新完成后的 Release 链接仍指向上游 `pingdotgg/t3code` 的 `vX.Y.Z`；共享 URL helper 已改为 `leaperone/magie` 的 `desktop-vX.Y.Z`，logic/toast 测试已覆盖。
- 首次远端 CI 的 Check job 全绿；Test job 暴露 `infra/relay/scripts/deploy.test.ts` 仍断言已移除的 relay release graph。该断言不再属于 Magie Desktop-only workflow，已删除。

## 技术决策

| 决策                      | 证据                                                                                            |
| ------------------------- | ----------------------------------------------------------------------------------------------- |
| 独立简化 release workflow | 现有自动更新资产和运行时可复用，但上游发布基础设施与 Magie 无关且缺少凭据。                     |
| 不宣称 signed auto-update | macOS 签名/公证秘密既未配置、也无法读取或创建；unsigned GitHub Release 不等同于可自动安装更新。 |
| 修正 Release notes URL    | Magie workflow 使用 `desktop-vX.Y.Z`；UI 必须打开同仓库同 Tag，不能沿用上游链接。               |

## 风险与边界

- `.github/workflows/release.yml`
- `.github/workflows/deploy-relay.yml`
- `scripts/build-desktop-artifact.ts`
- `apps/desktop/src/updates/DesktopUpdates.ts`
- `apps/web/src/components/desktopUpdate.logic.ts`
- `infra/relay/scripts/deploy.test.ts`
- `assets/README.md`
- `.github/workflows/{ci,release}.yml`

## 参考指针

- `.github/workflows/release.yml`
- `scripts/build-desktop-artifact.ts`
- `apps/desktop/src/updates/DesktopUpdates.ts`
- `docs/operations/release.md`
