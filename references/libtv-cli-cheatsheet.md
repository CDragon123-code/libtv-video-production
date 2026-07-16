# LibTV CLI 速查表

## 项目创建

```bash
libtv workspace create "name"      # 创建 workspace → 返回 workspaceId
libtv workspace use <id>           # 绑定 workspace
libtv project create "name" -w <id> # 创建画布 → 返回 uuid
libtv project use <uuid>           # 绑定画布
libtv project list -w <id>         # 列出 workspace 下所有画布
```

## 节点操作

```bash
# 创建
libtv node create "name" -t text|image|video [-x <X> -y <Y>]

# 文本节点写入
libtv node <key> -u content='["行1","行2"]'

# 设置参数
libtv node <key> -s key=value
# 模型名含空格需引号: -s 'model=Seedance 2.0 Mini'
# 常用: -s ratio=9:16 -s duration=10 -s enableSound=on
# modeType: -s modeType=image2video

# 设置 prompt
libtv node <key> --prompt "..."

# 连接上游节点
libtv node <key> --left-add <sourceKey>

# 触发生成
libtv node <key> --run

# 查询
libtv node <key>           # 查看节点详情
libtv node list            # 列出所有节点
libtv node list -g <group> # 列出分组内节点

# 删除
libtv node delete <key>
```

## 分组操作

```bash
libtv group create "name"             # 创建分组
libtv group <key> --node <nodeKey>    # 绑定节点
libtv group list                      # 列出分组
```

## 下载

```bash
libtv download -n "节点名"   # 下载到当前目录
```

## 模型查询

```bash
libtv model search --type image   # 图片模型
libtv model search --type video   # 视频模型
libtv model "模型名"               # 模型详细参数
```

## 注意事项

- `--prompt` 和 `-s` 不可同时与 `-u` 混用（作用不同字段）
- 先 `--left-add` 连接锚点，再切 image2video，顺序不可反
- 空格模型名必须单引号包裹
