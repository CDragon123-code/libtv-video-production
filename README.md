# OpenClaw LibTV Video Production Agent Script Headless CLI Workflow Local First Privacy First

> LibTV Video Production | OpenClaw Agent Script | Headless CLI | AI Video Workflow | Character Anchor | Image2Video | Seedance 2.0 Mini | Zero Dependency | Cross Platform

**OpenClaw Skill - 基于 LibTV CLI 的 AI 视频创作全流程，含标准工作流、角色锚点方案和踩坑避雷指南。**

## 简介

这是一个 OpenClaw Skill，封装了使用 LibTV CLI 完成 AI 短视频创作的标准流程。从画布搭建、角色锚点生成、视频节点配置，到最终下载合成，含完整的踩坑避雷指南。

## 核心能力

- 标准 7 步工作流：素材准备 -> 项目创建 -> 画布搭建 -> 角色锚点 -> 场景锚点 -> 视频生成 -> 下载合成
- 角色统一性保证：通过人物呈现板（四合一锚点）确保每个镜头中角色外貌一致
- 最佳模型选择：Z-image Turbo 用于写真锚点，Seedance 2.0 Mini 用于视频生成
- 8 条避坑清单：覆盖 modeType、缓存、prompt、并发等常见问题
- 积分预估：提前计算消耗，减少重试浪费

## 安装

将 `SKILL.md` 复制到 OpenClaw 的 skills 目录：

```bash
cp SKILL.md ~/.openclaw/workspace/skills/libtv-video-production/
```

## 自动触发

Skill 配置了以下触发关键词，当对话中出现这些词时会自动加载：

`libtv` `LibTV` `视频生成` `AI视频` `视频创作` `短视频脚本` `分镜脚本` `video production`

## 前置依赖

- [LibTV CLI](https://www.liblib.tv/cli) v1.1.1+
- 已登录的 LibTV 账户（含积分）
- 可选：剪映/PR 用于后期合成和字幕

## 快速开始

安装后，直接用自然语言向 AI Agent 提出需求：

```
帮我用 LibTV 做一个 60 秒的短视频，两个角色在烧烤摊聊天的剧情。
```

Agent 会自动加载本 Skill，按标准流程执行。

## 目录结构

```
libtv-video-production/
├── SKILL.md           # 技能主文档（核心工作流+避坑指南）
├── README.md          # 本文件
├── LICENSE            # MIT 许可证
├── examples/          # 示例脚本和 prompt 模板
│   └── prompt-templates.md
└── references/        # 参考资料
    └── libtv-cli-cheatsheet.md
```

## 积分预估

| 步骤 | 消耗 |
|------|------|
| 角色锚点图（2张） | ~20 |
| 场景锚点图（1张） | ~10 |
| 视频生成（8镜x66秒） | ~1100 |
| **理想总计** | **~1130** |

## 贡献

欢迎提交 Issue 和 PR 改进此 Skill。

## 许可证

MIT License - 详见 [LICENSE](LICENSE)
