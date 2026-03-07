---
name: wxmp-draft-publisher
description: This skill should be used when the user needs to create drafts, update drafts, or publish articles to WeChat Official Account (微信公众号) through the API. It provides workflows for managing WeChat official account drafts including uploading materials, creating drafts, updating drafts, and publishing to followers.
---

# 微信公众号草稿箱发布 Skill

本 Skill 提供微信公众号草稿箱管理的完整工作流程，包括素材上传、草稿创建、更新和发布等功能。

## 🎯 主要特性

- ✅ **多公众号支持** - 管理多个公众号账号
- ✅ **AI 自动配图** - 摄影写实风格，根据文章内容生成配图
- ✅ **默认封面降级** - 未配置 AI 时使用默认封面
- ✅ **Markdown 格式化** - 自动转换为优美的 HTML
- ✅ **标准尺寸调整** - 自动调整为微信封面 900 × 383 像素

## 何时使用本 Skill

当用户需要进行以下操作时使用：
- 创建微信公众号草稿
- 更新已存在的草稿内容
- 发布草稿到公众号
- 上传图片素材到素材库
- 查询发布状态
- 删除草稿

## 准备工作

在使用本 Skill 之前，确保已准备好：
- 微信公众号的 AppID 和 AppSecret
- 已在微信公众平台开通相关权限
- 拥有有效的 access_token（可通过 API 获取）
- ModelScope API Key（可选，用于自动生成配图）

**配置 ModelScope API Key（推荐）：**

方式 1：设置环境变量
```bash
export MODELSCOPE_API_KEY='your-api-key-here'
```

方式 2：命令行传递
```bash
python3 scripts/ai_cover_generator.py article.md cover.jpg YOUR_API_KEY
```

方式 3：代码中传递
```python
publisher = WeChatDraftPublisher(APPID, APPSECRET, ai_api_key="your-api-key")
```

**获取 API Key：** https://www.modelscope.cn/my/myaccesstoken

## 何时使用本 Skill

当用户需要进行以下操作时使用：
- 创建微信公众号草稿
- 更新已存在的草稿内容
- 发布草稿到公众号
- 上传图片素材到素材库
- 查询发布状态
- 删除草稿
- 自动生成文章配图

## 工作流程

### 0. 格式化 Markdown 内容（可选）

使用 `scripts/markdown_formatter.py` 将 Markdown 文本格式化为适合微信公众号的 HTML。

**命令行使用：**
```bash
python3 scripts/markdown_formatter.py <输入文件> [输出文件]
```

**支持的 Markdown 元素：**
- 标题（# - ######）
- 列表（- 或 *）
- 引用（>）
- 粗体（**）和斜体（*）
- 链接（[text](url)）
- 图片（![alt](url)）
- 代码块（```）

**格式化功能：**
- 规范化标题层级和空行
- 统一列表符号为 `-`
- 规范化引用块格式
- 自动转换为微信公众号兼容的 HTML

**在创建草稿时自动转换：**
```python
article = {
    'title': '文章标题',
    'content': markdown_content,      # Markdown 内容
    'content_type': 'markdown',       # 指定内容类型
    'thumb_media_id': '...',
    # ...
}

# 调用时启用格式化
publisher.add_draft([article], format_markdown=True)
```

### 0.1 使用自定义 CSS（可选）

支持通过外部 CSS 文件自定义样式，实现更灵活的排版效果。

**使用方法：**
```bash
# 使用默认样式（现代简约风格）
python3 scripts/markdown_formatter.py article.md

# 使用自定义 CSS 样式
python3 scripts/markdown_formatter.py article.md custom_style.css
```

### 0.2 自动生成配图（可选）

支持使用 AI 自动根据文章内容生成配图，使用 ModelScope Z-Image-Turbo 模型。

**独立使用：**
```bash
# 命令行生成配图
python3 scripts/ai_cover_generator.py <文章文件> <输出图片路径> [API_KEY]

# 示例
python3 scripts/ai_cover_generator.py article.md cover.jpg
```

**在创建草稿时自动生成：**

```python
import sys
sys.path.insert(0, 'scripts')
from publish_draft import WeChatDraftPublisher

# 初始化发布器（提供 AI API Key）
publisher = WeChatDraftPublisher(
    APPID,
    APPSECRET,
    ai_api_key="ms-your-api-key"  # ModelScope API Key
)

# 读取文章内容
with open('article.md', 'r', encoding='utf-8') as f:
    content = f.read()

article = {
    'title': '文章标题',
    'content': content,
    'content_type': 'markdown',
    # 不提供 thumb_media_id，让系统自动生成
}

# 创建草稿（自动生成封面图片）
result = publisher.add_draft([article], format_markdown=True, auto_generate_cover=True)
print(f"草稿创建成功: {result['media_id']}")
```

**配图生成说明：**
- 自动从文章标题和内容中提取关键词
- 根据关键词生成专业的摄影写实风格配图
- 自动调整图片尺寸为 900 × 383 像素（微信封面标准）
- 自动上传到微信素材库并获取 media_id

**注意事项：**
- 需要有效的 ModelScope API Key
- 配图生成需要等待约 30-60 秒
- 如果配图生成失败，可以使用手动上传的封面图片

**自定义 CSS 说明：**
- 在 Skill 的 `assets/` 目录中创建 CSS 文件
- CSS 类名使用 `nice` 前缀
- 支持全局样式、段落、标题、列表、引用、链接、代码等完整配置
- 参考 markdown.com.cn 的样式规范

**支持的样式类：**
- `.nice` - 全局容器样式
- `#nice p` - 段落样式
- `#nice h1/h2/h3` - 标题样式
- `#nice ul/ol` - 列表样式
- `#nice li` - 列表项样式
- `#nice blockquote` - 引用块样式
- `#nice a/strong/em` - 链接和强调样式
- `#nice code` - 行内代码样式

### 1. 上传封面图片

使用 `scripts/upload_material.py` 上传封面图片到素材库。

**命令行使用：**
```bash
python3 scripts/upload_material.py <APPID> <APPSECRET> image <图片路径>
```

**返回结果：**
- `media_id`: 素材 ID（用于草稿的 thumb_media_id）
- `url`: 图片的访问地址

**注意事项：**
- 封面图片必须是永久素材（type=image）
- 图片大小限制：2MB 以内
- **标准尺寸**：900 × 383 像素（2.35:1）
- **自动调整**：脚本会自动将图片裁剪为标准尺寸
- 支持格式：JPG, PNG

**解决错误码 53402：**
如果遇到 "封面裁剪失败" 错误，脚本已内置自动调整功能，会自动将图片裁剪为 900 × 383 像素。也可以使用 `scripts/image_processor.py` 手动调整图片：

```bash
# 检查图片信息
python3 scripts/image_processor.py <图片路径>

# 调整图片
python3 scripts/image_processor.py input.jpg output.jpg
```

详细解决方案请参考 `assets/troubleshooting_53402.md`

### 2. 创建草稿

使用 `scripts/publish_draft.py` 创建新的草稿。

**命令行使用：**
```bash
python3 scripts/publish_draft.py <APPID> <APPSECRET> create <thumb_media_id> <标题> <内容文件>
```

**参数说明：**
- `thumb_media_id`: 封面图片的 media_id（从步骤 1 获取）
- `标题`: 文章标题
- `内容文件`: 包含文章内容的文件路径（支持 HTML 格式）

**返回结果：**
- `media_id`: 草稿 ID（用于后续更新和发布）

**文章结构参数：**
- `title`: 文章标题（必填）
- `content`: 文章内容，支持 HTML（必填）
- `thumb_media_id`: 封面图片 media_id（必填）
- `author`: 作者（可选）
- `digest`: 摘要（可选，不填则自动抓取）
- `show_cover_pic`: 是否显示封面（0/1，默认 0）
- `need_open_comment`: 是否打开评论（0/1，默认 0）
- `only_fans_can_comment`: 是否只有粉丝可以评论（0/1，默认 0）

**支持的 HTML 标签：**
- 基本文本：`<p>`, `<span>`, `<br>`, `<strong>`, `<em>`
- 标题：`<h1>` 到 `<h6>`
- 列表：`<ul>`, `<ol>`, `<li>`
- 图片：`<img>`
- 引用：`<blockquote>`
- 其他：`<a>`, `<div>`, `<section>`

**注意事项：**
- 一次最多创建 8 篇文章（多图文）
- 内容中的图片必须使用永久素材的 URL
- 避免使用过多的 HTML 格式化标签
- 文章内容需要通过审核才能发布

**创建 Markdown 草稿（自动格式化）：**

如果使用 Markdown 编写文章，可以自动转换为 HTML 并格式化：

```python
import sys
sys.path.insert(0, 'scripts')
from publish_draft import WeChatDraftPublisher

# 方式1: 使用手动上传的封面
publisher = WeChatDraftPublisher(APPID, APPSECRET)

# 方式2: 使用 AI 自动生成封面（提供 AI API Key）
publisher = WeChatDraftPublisher(
    APPID,
    APPSECRET,
    ai_api_key="ms-your-api-key"
)

# 读取 Markdown 文件
with open('article.md', 'r', encoding='utf-8') as f:
    markdown_content = f.read()

article = {
    'title': '文章标题',
    'content': markdown_content,      # Markdown 内容
    'content_type': 'markdown',       # 指定为 Markdown
    'thumb_media_id': 'thumb_media_id',  # 可选：手动上传的封面
    'show_cover_pic': 1,
    'need_open_comment': 1,
    'only_fans_can_comment': 0
}

# 创建草稿（自动格式化 Markdown 并转换为 HTML）
result = publisher.add_draft([article], format_markdown=True)
print(f"草稿 media_id: {result['media_id']}")

# 或者自动生成封面（不提供 thumb_media_id）
article_auto = {
    'title': '文章标题',
    'content': markdown_content,
    'content_type': 'markdown',
    'show_cover_pic': 1,
    'need_open_comment': 1,
    'only_fans_can_comment': 0
}

result = publisher.add_draft([article_auto], format_markdown=True, auto_generate_cover=True)
print(f"草稿 media_id: {result['media_id']}")
```

### 3. 更新草稿（可选）

如需修改草稿内容，使用 `scripts/publish_draft.py` 的更新功能。

**更新单篇文章：**
```bash
python3 scripts/publish_draft.py <APPID> <APPSECRET> update <media_id> <index> <新标题> <新内容文件>
```

**参数说明：**
- `media_id`: 草稿 ID
- `index`: 要更新的文章位置（从 0 开始）
- `新标题`: 新的文章标题
- `新内容文件`: 新的内容文件路径

**注意事项：**
- index 参数用于多图文消息中的文章定位
- 可以更新草稿中的任何字段

### 4. 发布草稿

使用 `scripts/publish_draft.py` 将草稿发布到公众号。

**命令行使用：**
```bash
python3 scripts/publish_draft.py <APPID> <APPSECRET> publish <media_id>
```

**返回结果：**
- `publish_id`: 发布任务 ID（用于查询状态）
- `msg_data_id`: 消息数据 ID

**注意事项：**
- 发布是异步操作，需要等待审核
- 审核通过后文章才会对粉丝可见
- 审核失败会返回具体原因
- 避免频繁发布，受 API 频率限制

### 5. 查询发布状态（可选）

使用 `scripts/publish_draft.py` 查询发布进度。

**命令行使用：**
```bash
python3 scripts/publish_draft.py <APPID> <APPSECRET> status <publish_id>
```

**发布状态码：**
- `0`: 发布成功
- `1`: 发布中（异步处理中）
- `2`: 审核失败
- `3`: 审核中
- `4`: 已撤回
- `5`: 通过审核
- `6`: 用户删除

**注意事项：**
- 建议轮询查询状态直到确认发布结果
- 审核时间通常几分钟到几小时不等
- 审核失败需要修改内容后重新发布

### 6. 删除草稿（可选）

如需删除不需要的草稿，使用 `scripts/publish_draft.py`。

**命令行使用：**
```bash
python3 scripts/publish_draft.py <APPID> <APPSECRET> delete <media_id>
```

**注意事项：**
- 删除操作不可恢复
- 已发布的草稿无法删除，只能撤回

## API 参考

详细的 API 文档参考 `references/api_reference.md`，包含：
- 所有 API 的完整参数说明
- 错误码对照表
- 请求和响应示例
- 最佳实践建议

## 错误处理

脚本会自动处理常见的错误情况：

1. **Access Token 错误**
   - 错误码：40001, 40014, 42001
   - 解决方案：检查 AppID 和 AppSecret 是否正确，或重新获取 access_token

2. **素材上传失败**
   - 错误码：44001, 45001, 46001
   - 解决方案：检查图片格式、大小是否符合要求

3. **草稿创建失败**
   - 错误码：44003, 45002-45006, 45008
   - 解决方案：检查标题、内容长度是否符合限制

4. **发布失败**
   - 错误码：85002-85010
   - 解决方案：检查权限、内容是否违规

## 最佳实践

### Access Token 管理
- 在服务端缓存 access_token，有效期 2 小时
- 提前 10-15 分钟刷新，避免过期
- 建议使用数据库或 Redis 缓存

### 内容优化
- 标题简洁有力，建议 20 字以内
- 摘要突出重点，建议 50 字以内
- 正文分段清晰，每段 3-5 句
- 配图美观，建议 2-4 张图片
- 避免使用过多的格式化标签

### 发布策略
- 在用户活跃度较低的时间段发布（如上午 10-11 点）
- 避免频繁发布，建议每天不超过 1 篇
- 发布前预览检查，确认内容无误
- 保存草稿 media_id，方便后续管理

### 审核技巧
- 确保内容符合微信公众平台规范
- 避免使用敏感词汇和违规内容
- 图片版权清晰，无侵权风险
- 如遇审核失败，仔细阅读失败原因并修改

## 故障排查

### 问题：获取 access_token 失败
**原因：** AppID 或 AppSecret 错误，或账号权限不足
**解决方案：**
1. 登录微信公众平台检查 AppID 和 AppSecret
2. 确认账号已开通相关权限
3. 检查网络连接是否正常

### 问题：上传图片失败
**原因：** 图片格式或大小不符合要求
**解决方案：**
1. 确认图片格式为 JPG 或 PNG
2. 确保图片大小在 2MB 以内
3. 检查图片文件是否损坏

### 问题：草稿创建成功但发布失败
**原因：** 内容审核不通过或频率限制
**解决方案：**
1. 检查文章内容是否符合规范
2. 避免频繁发布操作
3. 使用查询状态接口查看具体失败原因

### 问题：发布一直处于审核中
**原因：** 审核需要时间，或内容需要人工审核
**解决方案：**
1. 耐心等待，通常审核需要几分钟到几小时
2. 检查内容是否涉及敏感话题
3. 联系微信客服咨询

## 扩展功能

如需扩展功能，可以参考以下 Python 脚本：

1. **批量发布**：修改脚本支持批量创建和发布草稿
2. **定时发布**：结合定时任务实现自动发布
3. **内容管理**：开发后台管理系统管理草稿
4. **数据分析**：统计文章阅读量和用户互动数据
5. **AI 配图**：自动根据文章内容生成配图并调整为微信封面尺寸

## 自动尺寸调整说明

### 微信封面标准
- **标准尺寸**：900 × 383 像素
- **宽高比**：2.35:1
- **文件大小**：建议 2MB 以内
- **支持格式**：JPG, PNG

### 智能裁剪策略
AI 生成的配图会自动调整尺寸：
1. 检测原始图片的宽高比
2. 计算需要的裁剪区域
3. 保持图片中心区域（重要内容）
4. 调整为 900 × 383 像素
5. 使用高质量 JPEG 格式保存（95% 质量）

### 示例输出
```
生成配图提示词: A professional business illustration, showing technology...
配图生成任务已提交，任务ID: 5749878
等待配图生成中... (1/30)
...
配图生成成功: https://muse-ai.oss-cn-hangzhou.aliyuncs.com/img/...
原始尺寸: 760 × 1280 像素
已调整为微信封面标准尺寸: 900 × 383 像素
配图已保存到: cover.jpg
```

## 相关资源

- 微信公众平台：https://mp.weixin.qq.com/
- 微信开放平台：https://open.weixin.qq.com/
- 开发者社区：https://developers.weixin.qq.com/community
- ModelScope: https://www.modelscope.cn/
