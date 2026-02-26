---
name: post-to-xhs
description: 小红书内容发布与管理助手。当用户要求登录、发小红书、搜索小红书、评论点赞收藏等任何小红书相关操作时使用。
metadata: {"openclaw": {"emoji": "📕", "requires": {"bins": ["convert"]}}}
---

# xhs-mcp-py 使用方式

## 安装

首次使用需要确认环境中有 Python 解释器。

```bash
# 使用 pip 安装
pip install -U xhs-mcp-py

# 安装 Playwright 浏览器
playwright install chromium
```

## 发布前必须先登录

**重要：** 所有操作都需要先登录小红书账号。

### 检查登录状态

```bash
# 命令行
xhs-mcp status
```

命令会输出 `✅ 已登录` 或 `❌ 未登录`。

如果显示未登录，需要先登录。

### 登录方式

```bash
# 方式一：启动浏览器扫码登录（有浏览器的桌面环境）
xhs-mcp login-browser




# 方式二：保存二维码图片（OpenClaw 生成二维码图片，再发给用户）
# 如果发送二维码有问题，方式一浏览器登录
xhs-mcp login-qrcode --save /tmp/qrcode.png
```

`login-qrcode` 参数：

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `-s/--save` | string | ❌ | 保存二维码图片到指定路径 |
| `--terminal/--no-terminal` | flag | ❌ | 是否在终端显示二维码（默认 terminal） |

登录成功后，cookies 会保存到本地文件，后续操作会自动复用。

## 发布操作

### 发布图文内容

命令：`xhs-mcp publish`

**参数：**（`-i/--image`、`--tag` 可多次指定）

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `-t/--title` | string | ✅ | 文字标题 |
| `-c/--content` | string | ✅ | 文字正文内容 |
| `-i/--image` | string | ✅ | 图片路径（可多次指定） |
| `--tag` | string | ❌ | 话题标签（可多次指定） |
| `--headless/--no-headless` | flag | ❌ | 是否无头模式（默认 headless） |

示例：

```bash
xhs-mcp publish -t "标题" -c "正文" -i /abs/1.jpg -i /abs/2.jpg --tag 旅行 --tag 美食
```

### 发布视频内容

命令：`xhs-mcp publish-video`

**参数：**

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `-t/--title` | string | ✅ | 文字标题 |
| `-c/--content` | string | ✅ | 文字正文内容 |
| `-v/--video` | string | ✅ | 视频路径 |
| `--tag` | string | ❌ | 话题标签（可多次指定） |
| `--headless/--no-headless` | flag | ❌ | 是否无头模式（默认 headless） |

示例：

```bash
xhs-mcp publish-video -t "标题" -c "正文" -v /abs/video.mp4
```

### 发布文字配图（文字卡片）

命令：`xhs-mcp publish-text-card`

**参数：**（`-p/--page`、`--tag` 可多次指定）

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `-c/--cover` | string | ✅ | 封面文字 |
| `-p/--page` | string | ❌ | 正文页文字（可多次指定，最多17页） |
| `-s/--style` | string | ❌ | 卡片样式：基础\|边框\|备忘\|手写\|便签\|涂写\|简约\|光影\|几何 |
| `-t/--title` | string | ❌ | 文字标题 |
| `--content` | string | ❌ | 文字正文内容 |
| `--tag` | string | ❌ | 话题标签（可多次指定） |
| `--headless/--no-headless` | flag | ❌ | 是否无头模式（默认 headless） |

示例：

```bash
xhs-mcp publish-text-card -c "封面" -p "第一页" -p "第二页" -s "基础" -t "笔记标题"
```

## 浏览/搜索

### 搜索内容

命令：`xhs-mcp search`

**参数：**

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `-k/--keyword` | string | ✅ | 搜索关键词 |
| `--headless/--no-headless` | flag | ❌ | 是否无头模式（默认 headless） |

**输出：**

命令会打印搜索到的笔记列表。每条结果会包含：

- `ID`：对应后续命令里的 `--feed-id`
- `xsec_token`：对应后续命令里的 `--xsec-token`

示例（截断）：

```text
1. 标题...
   作者: xxx | 点赞: 123
   ID: 1234567890abcdef
   xsec_token: xsec_xxx
```

## 互动（点赞/收藏/评论）

以下命令需要你先从搜索结果或笔记链接中拿到 `feed_id` 和 `xsec_token`。

### 点赞/取消点赞

命令：`xhs-mcp like`

**参数：**

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `--feed-id` | string | ✅ | 笔记 ID |
| `--xsec-token` | string | ✅ | 访问令牌 |
| `--unlike` | flag | ❌ | 取消点赞（默认 false 为点赞） |
| `--headless/--no-headless` | flag | ❌ | 是否无头模式（默认 headless） |

示例：

```bash
xhs-mcp like --feed-id FEED_ID --xsec-token XSEC_TOKEN
xhs-mcp like --feed-id FEED_ID --xsec-token XSEC_TOKEN --unlike
```

### 收藏/取消收藏

命令：`xhs-mcp favorite`

**参数：**

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `--feed-id` | string | ✅ | 笔记 ID |
| `--xsec-token` | string | ✅ | 访问令牌 |
| `--unfavorite` | flag | ❌ | 取消收藏（默认 false 为收藏） |
| `--headless/--no-headless` | flag | ❌ | 是否无头模式（默认 headless） |

示例：

```bash
xhs-mcp favorite --feed-id FEED_ID --xsec-token XSEC_TOKEN
xhs-mcp favorite --feed-id FEED_ID --xsec-token XSEC_TOKEN --unfavorite
```

### 发表评论

命令：`xhs-mcp comment`

**参数：**

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `--feed-id` | string | ✅ | 笔记 ID |
| `--xsec-token` | string | ✅ | 访问令牌 |
| `-c/--content` | string | ✅ | 评论内容 |
| `--headless/--no-headless` | flag | ❌ | 是否无头模式（默认 headless） |

示例：

```bash
xhs-mcp comment --feed-id FEED_ID --xsec-token XSEC_TOKEN -c "评论内容"
```

### 回复评论

命令：`xhs-mcp reply-comment`

**参数：**

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `--feed-id` | string | ✅ | 笔记 ID |
| `--xsec-token` | string | ✅ | 访问令牌 |
| `-c/--content` | string | ✅ | 回复内容 |
| `--comment-id` | string | ❌ | 目标评论 ID |
| `--user-id` | string | ❌ | 目标用户 ID |
| `--headless/--no-headless` | flag | ❌ | 是否无头模式（默认 headless） |

`--comment-id` 和 `--user-id` 至少需要提供一个。

示例：

```bash
xhs-mcp reply-comment --feed-id FEED_ID --xsec-token XSEC_TOKEN -c "回复内容" --comment-id COMMENT_ID
```


## 退出登录

命令：`xhs-mcp logout`

会删除本地 cookies。

## （可选）MCP 模式

如果你的运行环境支持 MCP（例如 Claude Desktop / Cursor），可以启动 MCP：

```bash
xhs-mcp serve
```