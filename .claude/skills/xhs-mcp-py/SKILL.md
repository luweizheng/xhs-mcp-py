---
name: xhs-auto-ops
description: 小红书内容发布与管理助手。当用户要求登录、发小红书、搜索小红书、评论点赞收藏等任何小红书相关操作时使用。
metadata: {"openclaw": {"emoji": "📕", "requires": {"bins": ["convert"]}}}
---

# xhs-mcp-py 使用方式

## 安装

首次使用需要确认环境中有 Python 解释器。

```bash
# 使用 pip 安装
pip install xhs-mcp

# 安装 Playwright 浏览器
playwright install chromium
```

## 发布或评论前必须先登录

**重要：** 所有操作都需要先登录小红书账号。

### 检查登录状态

```bash
# 命令行
xhs-mcp status
```

如果返回 `is_logged_in: false`，需要先登录。

### MCP 调用方式（给 AI / MCP 客户端）

在 MCP 客户端中，建议按下面顺序调用：

1. 调用 `check_login_status`
2. 如果 `is_logged_in: false`
   - 桌面环境：调用 `login_with_browser`
   - Claude Code / Open Code：让用户在终端执行 `xhs-mcp login-qrcode --terminal`
3. 登录成功后，再调用发布/互动相关工具

### 登录方式

```bash
# 方式一：启动浏览器扫码登录（有浏览器的桌面环境）
xhs-mcp login-browser

# 方式二：在命令行终端显示二维码（推荐 Claude Code / Open Code）
# 如果提取二维码有问题，方式一浏览器登录
# 注意：需要安装 zbar 库
# macOS: brew install zbar
# Ubuntu/Debian: apt install libzbar0
# Fedora/Rocky: dnf install zbar
# 如果未安装 zbar，会自动保存二维码图片并提示路径
xhs-mcp login-qrcode --terminal

# 方式三：保存二维码图片（OpenClaw 生成二维码图片，再发给用户）
# 如果发送二维码有问题，方式一浏览器登录
xhs-mcp login-qrcode --save /tmp/qrcode.png
```

登录成功后，cookies 会保存到本地文件，后续操作会自动复用。

### MCP 工具登录

如果通过 MCP 客户端使用：

**方式一：浏览器登录）**
调用 `login_with_browser` 工具：
- 会弹出浏览器窗口显示二维码

**方式二：命令行登录（Claude Code / Open Code 可使用这种方式）**
在终端执行：
```bash
xhs-mcp login-qrcode --terminal
```
- 二维码直接显示在终端

### 登录状态检查流程

1. 先调用 `check_login_status` 检查是否已登录
2. 如果 `is_logged_in: false`，提醒用户需要登录
3. 根据环境选择登录方式：
   - 桌面环境：调用 `login_with_browser` 工具（会弹出浏览器窗口）
   - Claude Code / Open Code：执行命令 `xhs-mcp login-qrcode --terminal`
4. 登录成功后再执行其他操作

## 发布操作

### 发布图文内容

**MCP 工具：** `publish_content`

**参数：**

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `title` | string | ✅ | 文章标题（最多20个字） |
| `content` | string | ✅ | 文字正文内容 |
| `images` | list[string] | ✅ | 图片路径列表（本地绝对路径） |
| `tags` | list[string] | ❌ | 话题标签列表 |
| `schedule_at` | string | ❌ | 定时发布时间（ISO8601格式） |

## 浏览/搜索

### 获取首页推荐列表

**MCP 工具：** `list_feeds`

**参数：** 无

### 搜索内容

**MCP 工具：** `search_feeds`

**参数：**

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `keyword` | string | ✅ | 搜索关键词 |
| `sort_by` | string | ❌ | 排序：综合/最新/最多点赞/最多评论/最多收藏 |
| `note_type` | string | ❌ | 类型：不限/视频/图文 |
| `publish_time` | string | ❌ | 时间：不限/一天内/一周内/半年内 |
| `search_scope` | string | ❌ | 范围：不限/已看过/未看过/已关注 |
| `location` | string | ❌ | 位置：不限/同城/附近 |

### 获取笔记详情

**MCP 工具：** `get_feed_detail`

**参数：**

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `feed_id` | string | ✅ | 笔记 ID |
| `xsec_token` | string | ✅ | 访问令牌 |
| `load_comments` | bool | ❌ | 是否加载评论（基础模式，前10条） |
| `load_all_comments` | bool | ❌ | 是否加载全部评论（滚动加载） |
| `limit` | int | ❌ | 限制一级评论数量（默认20） |
| `click_more_replies` | bool | ❌ | 是否展开二级回复 |
| `reply_limit` | int | ❌ | 跳过回复数过多的评论（默认10） |
| `scroll_speed` | string | ❌ | 滚动速度：slow/normal/fast |

### 获取用户主页

**MCP 工具：** `get_user_profile`

**参数：**

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `user_id` | string | ✅ | 用户 ID |
| `xsec_token` | string | ✅ | 访问令牌 |

## 点赞/收藏

### 点赞/取消点赞

**MCP 工具：** `like_feed`

**参数：**

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `feed_id` | string | ✅ | 笔记 ID |
| `xsec_token` | string | ✅ | 访问令牌 |
| `unlike` | bool | ❌ | 是否取消点赞（默认 false） |

### 收藏/取消收藏

**MCP 工具：** `favorite_feed`

**参数：**

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `feed_id` | string | ✅ | 笔记 ID |
| `xsec_token` | string | ✅ | 访问令牌 |
| `unfavorite` | bool | ❌ | 是否取消收藏（默认 false） |

### 发布视频内容

**MCP 工具：** `publish_with_video`

**参数：**

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `title` | string | ✅ | 文章标题（最多20个字） |
| `content` | string | ✅ | 文字正文内容 |
| `video` | string | ✅ | 本地视频绝对路径 |
| `tags` | list[string] | ❌ | 话题标签列表 |
| `schedule_at` | string | ❌ | 定时发布时间（ISO8601格式） |

### 发布文字配图

**MCP 工具：** `publish_text_card`

**参数：**

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `cover_text` | string | ✅ | 封面文字 |
| `pages` | list[string] | ❌ | 正文页列表（最多17页） |
| `style` | string | ❌ | 卡片样式：基础/边框/备忘/手写/便签/涂写/简约/光影/几何 |
| `title` | string | ❌ | 文章标题 |
| `content` | string | ❌ | 文字正文内容 |
| `tags` | list[string] | ❌ | 话题标签列表 |

## 评论

### 发表评论

**MCP 工具：** `post_comment`

**参数：**

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `feed_id` | string | ✅ | 笔记 ID |
| `xsec_token` | string | ✅ | 访问令牌 |
| `content` | string | ✅ | 评论内容 |

### 回复评论

**MCP 工具：** `reply_comment`

**参数：**

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `feed_id` | string | ✅ | 笔记 ID |
| `xsec_token` | string | ✅ | 访问令牌 |
| `content` | string | ✅ | 回复内容 |
| `comment_id` | string | ❌ | 目标评论 ID |
| `user_id` | string | ❌ | 目标用户 ID |

`comment_id` 和 `user_id` 至少提供一个。

## 登录辅助工具

### 获取登录二维码（Base64 图片）

**MCP 工具：** `get_login_qrcode`

通常用于“自己展示二维码 + 轮询登录状态”的场景。

### 删除 cookies

**MCP 工具：** `delete_cookies`

用于清理本地 cookies，强制重新登录。