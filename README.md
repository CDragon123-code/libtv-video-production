# OpenClaw LibTV Video Production

![LibTV Video Production](https://img.shields.io/badge/LibTV-Video%20Production-4f8af7?style=flat-square)
![OpenClaw](https://img.shields.io/badge/OpenClaw-Agent%20Script-3776AB?style=flat-square)
![Headless CLI](https://img.shields.io/badge/Headless-CLI-34c97c?style=flat-square)
![AI Video](https://img.shields.io/badge/AI-Video%20Workflow-ff6b35?style=flat-square)
![Character Anchor](https://img.shields.io/badge/Character-Anchor-9b59b6?style=flat-square)
![Image2Video](https://img.shields.io/badge/Mode-Image2Video-e74c3c?style=flat-square)
![Seedance 2.0 Mini](https://img.shields.io/badge/Model-Seedance%202.0%20Mini-2980b9?style=flat-square)
![Zero Dependency](https://img.shields.io/badge/Zero-Dependency-34c97c?style=flat-square)
![Cross Platform](https://img.shields.io/badge/Cross-Platform-f5a623?style=flat-square)

AI 短视频全流程自动化 —— AI Agent 操控 LibTV 画布，从脚本到视频一键生成。

一个 OpenClaw Skill，封装了使用 LibTV CLI 完成 AI 短视频创作的标准工作流。AI Agent 可以在 LibTV 画布上创建项目、搭建节点、生成角色锚点、配置视频参数并触发模型生成，最后下载合成。

- 标准化工作流 —— 7 步完成从素材到视频的全过程
- 角色统一性 —— 人物呈现板（四合一锚点），确保每个镜头角色一致
- 最佳模型选择 —— Z-image Turbo 锚点 + Seedance 2.0 Mini 视频
- 踩坑避雷 —— 8 条已验证的踩坑清单，减少摸索和积分浪费
- 积分可控 —— 提前预估消耗，理想情况单轮 ~1100 积分

## 安装

```bash
# 复制到 OpenClaw skills 目录
cp -r libtv-video-production ~/.openclaw/workspace/skills/

# 或 clone 到 skills 目录
cd ~/.openclaw/workspace/skills/
git clone https://github.com/CDragon123-code/libtv-video-production.git
```

## 自动触发

Skill 配置了以下关键词，对话中自动加载：

`libtv` `LibTV` `视频生成` `AI视频` `视频创作` `短视频脚本` `分镜脚本` `video production`

## 快速开始

安装后，直接用自然语言向 AI Agent 提出需求：

```
帮我用 LibTV 做一个 60 秒的短视频，两个角色在烧烤摊聊天的剧情。
```

Agent 会自动加载本 Skill，按标准 7 步流程执行：

1. 准备素材（场景库、脚本、角色设定）
2. 创建 LibTV workspace 和 project
3. 搭建四区画布（输入区、脚本区、分镜区、生产区）
4. 生成角色锚点（人物呈现板，Z-image Turbo）
5. 生成场景锚点
6. 创建视频节点并触发生成（image2video + Seedance 2.0 Mini）
7. 下载所有镜头到本地

## 前置依赖

- [LibTV CLI](https://www.liblib.tv/cli) v1.1.1+
- 已登录的 LibTV 账户（含积分）
- 可选：剪映/PR 用于后期合成和字幕

## 功能特性

| 功能 | 说明 |
|------|------|
| 工作流 | 7 步标准化流程，从素材到视频 |
| 画布结构 | 自动创建四区画布（输入/脚本/分镜/生产） |
| 锚点生成 | 人物呈现板四合一，保证角色一致性 |
| 模型选择 | Z-image Turbo 锚点 + Seedance 2.0 Mini 视频 |
| 视频配置 | image2video 模式，9:16 竖屏，720p，环境音 |
| prompt 模板 | 角色锚点、场景锚点、视频对白、字幕定格 |
| 积分预估 | 单轮 ~1100 积分，含重试 ~3100 |
| 踩坑清单 | 8 条已验证避坑指南（modeType/缓存/并发等） |
| CLI 速查 | 完整 LibTV CLI 命令参考 |
| 跨平台 | OpenClaw + LibTV CLI 跨平台可用 |

## 踩坑避雷

| 坑 | 错误做法 | 正确做法 |
|----|----------|----------|
| 文本节点写入 | `--content "..."` | `-u content='["..."]'` |
| 角色锚点风格 | Seedream 4.5 生成三视图，偏动画 | Z-image Turbo + photorealistic prompt |
| 锚点格式 | 正面/半侧面/侧面分 3 张图 | 一张四合一呈现板 |
| modeType | text2video 连接锚点图报错 | image2video 连接锚点图 |
| prompt 缩水 | image2video 只写动作描述 | 仍然写完整对白 + 角色描述 |
| 缓存 | 相同参数返回旧结果 | 微调 prompt 强制重新生成 |
| 空格模型名 | `-s model=Seedance 2.0 Mini` | `-s 'model=Seedance 2.0 Mini'` |
| 并发 | 同时触发过多报"稍候" | 串行触发或间隔 1-2 秒 |

## 积分预估

| 步骤 | 消耗 | 说明 |
|------|------|------|
| 角色锚点（2 张） | ~20 | Z-image Turbo |
| 场景锚点（1 张） | ~10 | Z-image Turbo |
| 视频生成（8 镜 66s） | ~1100 | Seedance 2.0 Mini, 720p |
| 含 3 轮重试 | ~3100 | 踩坑实际情况 |

## 目录结构

```
libtv-video-production/
├── SKILL.md              # 核心技能文档（完整工作流 + 避坑指南）
├── README.md             # 本文件
├── LICENSE               # MIT License
├── examples/
│   └── prompt-templates.md   # 锚点/视频 Prompt 模板
└── references/
    └── libtv-cli-cheatsheet.md # CLI 速查表
```

## 适用场景

- 真人剧情短视频（双人对白 / 多人场景）
- 产品推广短片（场景植入式自然推荐）
- 品牌故事短片
- 快手/抖音/小红书竖屏内容

## 技术栈

- Agent Runtime: OpenClaw Skill
- Video Platform: LibTV CLI v1.1.1+
- Image Model: Z-image Turbo（角色锚点）
- Video Model: Seedance 2.0 Mini（视频生成）
- Post-production: 剪映 / Premiere Pro（字幕 + 合成）

## 许可证

MIT License

---

**Made with OpenClaw + LibTV CLI**
