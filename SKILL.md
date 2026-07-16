---
name: "libtv-video-production"
description: "LibTV CLI AI视频全流程创作：项目搭建、角色锚点、场景锚点、视频生成、下载合成，含踩坑避雷指南。"
triggers:
  - "libtv"
  - "LibTV"
  - "视频生成"
  - "AI视频"
  - "视频创作"
  - "libtv视频"
  - "短视频脚本"
  - "分镜脚本"
  - "video production"
autoTrigger: true
---

# OpenClaw LibTV Video Production Agent Script Headless CLI Workflow Local First Privacy First

> LibTV Video Production | OpenClaw Agent Script | Headless CLI | AI Video Workflow | Character Anchor | Image2Video | Seedance 2.0 Mini | Zero Dependency | Cross Platform

基于 OpenClaw + LibTV CLI 完成 AI 短视频的标准化流程。从画布搭建到最终视频下载，含已验证的最佳实践和避坑指南。

## 核心原则

1. **角色锚点是基础**：没有锚点的视频生成，人物必然不统一
2. **image2video 是唯一正确的模式**：text2video 无法引用锚点图
3. **prompt 必须包含完整对白**：即使 image2video 模式也不妥协台词质量
4. **角色库图片按需自定义比例**，只有视频输出统一 9:16
5. **先建节点不触发，确认后再跑**：避免反复消耗积分

## 标准工作流（7 步）

### 1. 准备素材
- 选题来源（场景库/脚本）
- 视频写法规则
- Logo 文件路径
- 角色设定（年龄、外貌、服装、性格）
- 场景设定（环境、道具、光线、氛围）

### 2. 创建 LibTV 项目

```bash
# 创建 workspace
libtv workspace create "项目名"
# 记录返回的 workspaceId

# 绑定 workspace
libtv workspace use <workspaceId>

# 创建画布（归属到 workspace）
libtv project create "画布名" --workspace <workspaceId>
# 记录返回的 uuid

# 绑定画布
libtv project use <projectUuid>
```

### 3. 搭建四区画布（创建分组 + 节点）

**分组创建：**
```bash
libtv group create "输入区"
libtv group create "脚本区"
libtv group create "分镜区"
libtv group create "生产区"
```

**输入区节点（文本类型）：**
- 选题来源
- 视频写法规则
- 角色设定（每个角色一个节点）

**脚本区节点：** 完整分镜脚本

**分镜区节点：** 每个镜头画面描述+对白

**向分组绑定节点：**
```bash
libtv group <groupNodeKey> --node <nodeKey>
```

**WARNING 文本节点写入内容：**
```bash
# 正确语法 - content 必须是 JSON 数组格式
libtv node <nodeKey> -u content='["行1","行2","行3"]'

# 错误：不存在 --content 参数
libtv node <nodeKey> --content "..."
```

### 4. 生成角色锚点（人物呈现板）

**这是全流程最关键的一步。角色锚点决定后续所有视频中人物的一致性。**

**模型选择：** `Z-image Turbo`（真人写实风格，避免 Seedream 偏动画）
**格式：** 人物呈现板（四合一：正面+半侧面+侧面+全身）
**比例：** 1:1（角色库不强制 9:16）

```bash
# 创建锚点节点
libtv node create "角色锚点-A-呈现板" -t image
libtv node create "角色锚点-B-呈现板" -t image

# 配置参数
libtv node <nodeKey> \
  -s 'model=Z-image Turbo' \
  -s ratio=1:1 \
  -s count=1 \
  --prompt 'Character reference board, photorealistic, one single Chinese [Gender], [Age] years old. Four panel layout on clean light gray background: Top-left full body front view, [Clothing], [Body type]. Top-right close-up headshot, [Face features]. Bottom-left three-quarter side profile. Bottom-right pure side profile. All panels same consistent person, identical face and clothing. Professional studio lighting, 8k hyperrealistic DSLR, no text no labels.'

# 触发生成
libtv node <nodeKey> --run
```

**WARNING 常见问题：**
- 如果生成图偏动画/插画风格 -> 换 Z-image Turbo，prompt 加 `photorealistic, DSLR, hyperrealistic`
- 如果格式不是四合一板式 -> 在 prompt 中明确 `four panel layout`
- 不要生成分角度的多张图，一张呈现板即可

### 5. 生成场景锚点

```bash
libtv node create "场景锚点-场景名" -t image
libtv node <nodeKey> \
  -s 'model=Z-image Turbo' \
  -s ratio=9:16 \
  --prompt '场景描述，光线氛围，道具细节...'
libtv node <nodeKey> --run
```

### 6. 创建视频节点并触发生成

**WARNING 这是全流程最容易出错的一步。三个关键决策：**

| 决策 | 正确 | 错误 |
|------|------|------|
| modeType | image2video | text2video（无法连锚点图） |
| 模型 | Seedance 2.0 Mini | 带 VIP 标签的模型（额外积分） |
| prompt | 完整对白 + 角色描述 | 仅动作描述（台词丢失） |
| 时长 | 8-12 秒/镜 | 3-5 秒/镜（太碎） |

**单个镜头配置模板：**

```bash
# 创建视频节点
libtv node create "镜N-V-镜头名称" -t video

# 必须先连接锚点图
libtv node <videoNodeKey> \
  --left-add <角色A呈现板Key> \
  --left-add <角色B呈现板Key> \
  --left-add <场景锚点Key>

# 然后切 modeType + 设 prompt + 触发
libtv node <videoNodeKey> \
  -s modeType=image2video \
  -s 'model=Seedance 2.0 Mini' \
  -s ratio=9:16 \
  -s resolution=720p \
  -s duration=<秒数> \
  -s enableSound=on \
  --prompt '场景描述。角色A（服装+外貌特征）动作+对白。角色B（服装+外貌特征）动作+对白。光照氛围，竖屏。' \
  --run
```

**WARNING 必须先用 --left-add 连接锚点，再切 modeType 和 prompt。连接后 CLI 会自动将图片 URL 填入 imageList。**

**镜头数量建议：**
- 总时长控制在 60-70 秒
- 单个镜头 8-12 秒（太短不连贯、太长节奏拖沓）
- 相邻短对话镜头合并为一个连续镜头
- 最终镜头数 8-10 个为宜

### 7. 下载和合成

```bash
# 批量下载
libtv download -n "镜1-V-xxx"
libtv download -n "镜2-V-xxx"
# ... 逐个下载

# 导入剪映
# 1. 按顺序排列拼接
# 2. 自动语音识别添加字幕
# 3. 微调转场和节奏
# 4. 片尾加 Logo
# 5. 导出
```

## 踩坑避雷清单

### 坑1：文本节点写入
- WRONG: `--content "..."` -- 不存在此参数
- RIGHT: `-u content='["..."]'` -- JSON 数组格式

### 坑2：角色锚点风格
- WRONG: Seedream 4.5 生成"三视图""九宫格" -> 容易滑向插画风格
- RIGHT: Z-image Turbo + `photorealistic, DSLR, hyperrealistic` prompt

### 坑3：角色锚点格式
- WRONG: 正面+半侧面+侧面分3张图 -> 引用麻烦
- RIGHT: 一张四合一呈现板 -> 一个锚点解决所有角度

### 坑4：modeType 错误
- WRONG: text2video 连接锚点图 -> 报错"无法使用纯文生视频"
- RIGHT: image2video 连接锚点图 -> 自动将图片填入 imageList

### 坑5：切换 modeType 后 prompt 缩水
- WRONG: image2video 只写动作描述 -> 台词丢失
- RIGHT: image2video 仍然写完整对白 + 角色描述

### 坑6：Seedance 缓存
- 相同参数 + 相同 prompt -> 直接返回缓存，taskId 不变
- 解决：稍微改变 prompt（去掉括号或调标点），观察 taskId 是否新

### 坑7：模型名含空格需引号
- WRONG: `-s model=Seedance 2.0 Mini` -> 报错
- RIGHT: `-s 'model=Seedance 2.0 Mini'`

### 坑8：并发限制
- 同时触发过多 --run 可能报"正在发起任务请稍候"
- 串行触发或间隔 1-2 秒

## 积分预估

| 步骤 | 单次消耗 | 说明 |
|------|----------|------|
| 图片生成 | ~10/张 | 角色锚点 + 场景锚点 |
| 视频生成 | ~16.7/秒 | Seedance 2.0 Mini, 720p |
| 示例（8镜66秒） | ~1100 | 理想一轮 |
| 示例（含3轮重试） | ~3100 | 踩坑实际情况 |

**建议：** 先在单角色简单场景下验证全流程（1-2 镜），确认 modeType + 锚点引用正确后，再批量生成。大幅减少重试积分损失。

## 模型速查

| 用途 | 推荐模型 | 关键特性 |
|------|----------|----------|
| 角色锚点图 | Z-image Turbo | 真人写实，10s 生成 |
| 场景锚点图 | Z-image Turbo | 真人写实 |
| 视频生成 | Seedance 2.0 Mini | 9:16, 4-15s, image2video, 音频 |
| 图片生成（备选） | Seedream 4.5 | 多角色一致性，但有动画倾向 |

## CLI 速查

```bash
# 项目
libtv workspace create "name"     # 创建 workspace
libtv workspace use <id>          # 绑定 workspace
libtv project create "name" -w <id>  # 创建画布
libtv project use <uuid>          # 绑定画布

# 节点
libtv node create "name" -t text|image|video  # 创建节点
libtv node <key> -u content='["..."]'         # 更新文本
libtv node <key> -s key=value                 # 设置参数
libtv node <key> --prompt "..."               # 设置 prompt
libtv node <key> --left-add <sourceKey>       # 连接上游节点
libtv node <key> --run                        # 触发生成
libtv node delete <key>                       # 删除节点
libtv node list                               # 列出所有节点

# 分组
libtv group create "name"           # 创建分组
libtv group <groupKey> --node <key> # 绑定节点到分组

# 下载
libtv download -n "节点名"          # 下载节点资源

# 查询
libtv model search --type image|video  # 查可用模型
libtv model "modelName"                # 查模型参数详情
```

## 关键约束

| 项目 | 约束 |
|------|------|
| 角色锚点图比例 | 按内容自适应（不强制 9:16） |
| 视频比例 | 统一 9:16 竖屏 |
| 视频音频 | image2video 模式可开启环境音+对话 |
| 字幕 | LibTV 不支持后期字幕，需剪映处理 |
| 视频合成 | 需下载后在剪映等工具中拼接 |
| 积分归属 | 使用当前登录账户积分 |
