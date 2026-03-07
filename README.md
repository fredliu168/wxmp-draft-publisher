# 微信公众号草稿箱发布 Skill - 完整使用指南

## 📋 项目概述

一个功能完整的微信公众号草稿箱发布工具，支持 AI 自动配图、多公众号管理、Markdown 格式化等功能。

---

## 🎯 主要功能

### 1. ✅ AI 自动配图
- 根据文章内容智能生成配图
- 使用 ModelScope Z-Image-Turbo 模型
- 自动调整为微信封面标准尺寸（900 × 383 像素）
- 支持环境变量配置 API Key

### 2. ✅ 多公众号支持
- 管理多个公众号账号
- 通过配置文件或账号名称切换
- 每个公众号独立配置 AppID、AppSecret、ModelScope API Key

### 3. ✅ 默认封面降级
- 未配置 AI 时自动使用默认封面
- 海浪主题（海浪、小船、蓝天、海鸥）
- 宁静祥和的商务风格
- 通用性强，适合各种文章

### 4. ✅ Markdown 格式化
- 自动转换为优美的 HTML
- 支持自定义 CSS 样式
- 微信公众号兼容

### 5. ✅ 标准尺寸调整
- 自动调整图片为 900 × 383 像素
- 智能裁剪，保持中心区域
- 高质量 JPEG 输出

---

## 🚀 快速开始

### 方式 1：从 GitHub 克隆（推荐）

```bash
# 1. 克隆仓库
git clone https://github.com/your-username/wxmp-draft-publisher.git

# 2. 进入目录
cd wxmp-draft-publisher

# 3. 安装依赖
pip install -r requirements.txt

# 4. 配置公众号
python3 scripts/config_manager.py add <名称> <AppID> <AppSecret> <描述>
```

### 方式 2：使用本项目（推荐）

如果你想直接使用本项目：

```bash
# 1. 配置环境变量（AI 配图）
export MODELSCOPE_API_KEY='ms-your-api-key-here'

# 2. 添加公众号
python3 wxmp-draft-publisher/scripts/config_manager.py add account1 <your_app_id> <your_app_secret> "我的公众号"

# 3. 读取文章
python3 wxmp-draft-publisher/scripts/publish_draft.py account1 account2 create <thumb_media_id> "标题" article.md

# 4. 发布草稿
python3 wxmp-draft-publisher/scripts/publish_draft.py account1 account2 publish <media_id>
```

---

## 📚 文档索引

### 核心文档
- **SKILL.md** - Skill 完整说明文档
- **references/api_reference.md** - API 详细参考
- **assets/troubleshooting_53402.md** - 常见问题解决方案

### 配置文档
- **环境变量配置说明.md** - ModelScope API Key 配置指南
- **快速配置指南.md** - 30 秒快速配置
- **多公众号配置指南.md** - 多账号管理完整指南
- **Git操作指南.md** - Git 操作说明

### 功能说明
- **默认封面说明.md** - 默认封面功能说明
- **生成海浪封面说明.md** - 海浪主题封面生成说明

### 测试脚本
- **test_default_cover.py** - 默认封面功能测试
- **test_upload_final.py** - 完整上传测试
- **test_multi_account.py** - 多账号测试

### GitHub 脚本
- **create_github_repo.sh** - 自动创建 GitHub 仓库脚本

---

## 🔧 配置指南

### 1. ModelScope API Key 配置

**方式 1：环境变量（推荐）**
```bash
export MODELSCOPE_API_KEY='ms-your-api-key-here'
```

**方式 2：命令行参数**
```bash
python3 wxmp-draft-publisher/scripts/publish_draft.py \
  APPID APPSECRET \
  create \
  <thumb_media_id> \
  "标题" \
  article.md
```

**方式 3：配置文件**
```bash
python3 wxmp-draft-publisher/scripts/config_manager.py \
  add account1 \
  <your_app_id> \
  <your_app_secret> \
  "我的公众号" \
  ms-your-api-key-here
```

### 2. 微信公众号配置

**添加公众号：**
```bash
python3 wxmp-draft-publisher/scripts/config_manager.py \
  add <名称> <AppID> <AppSecret> [描述] [ModelScope_API_Key]
```

**查看所有公众号：**
```bash
python3 wxmp-draft-publisher/scripts/config_manager.py list
```

**设置默认公众号：**
```bash
python3 wxmp-draft-publisher/scripts/config_manager.py set_default <名称>
```

**删除公众号：**
```bash
python3 wxmp-draft-publisher/scripts/config_manager.py delete <名称>
```

---

## 🎨 使用示例

### 示例 1：创建草稿并自动生成封面

```python
import sys
sys.path.insert(0, 'wxmp-draft-publisher/scripts')

from publish_draft import WeChatDraftPublisher

# 初始化（使用默认公众号）
publisher = WeChatDraftPublisher(
    app_id='<your_app_id>',
    app_secret='<your_app_secret>',
    ai_api_key='ms-your-api-key-here'  # 或使用环境变量
)

# 读取文章
with open('article.md', 'r', encoding='utf-8') as f:
    content = f.read()

# 创建草稿（自动生成封面）
article = {
    'title': '文章标题',
    'content': content,
    'content_type': 'markdown',
    'show_cover_pic': 1,
    'need_open_comment': 1,
    'only_fans_can_comment': 0
}

result = publisher.add_draft([article], format_markdown=True, auto_generate_cover=True)
print(f"草稿 ID: {result['media_id']}")
```

### 示例 2：使用指定的公众号

```python
from publish_draft import WeChatDraftPublisher

# 使用特定公众号
publisher = WeChatDraftPublisher(account_name='account2')

article = {
    'title': '文章标题',
    'content': '文章内容',
    'content_type': 'markdown',
}

result = publisher.add_draft([article], format_markdown=True, auto_generate_cover=True)
```

### 示例 3：使用默认封面（不配置 AI）

```python
from publish_draft import WeChatDraftPublisher

# 不提供 ai_api_key，会使用默认封面
publisher = WeChatDraftPublisher(
    app_id='<your_app_id>',
    app_secret='<your_app_secret>'
)

article = {
    'title': '文章标题',
    'content': '文章内容',
    'content_type': 'markdown',
}

# auto_generate_cover=True + 无 ai_api_key → 使用默认封面
result = publisher.add_draft([article], format_markdown=True, auto_generate_cover=True)
```

---

## 📂 项目结构

```
wxmp-draft-publisher/
├── scripts/                      # 核心脚本
│   ├── publish_draft.py         # 草稿发布主脚本
│   ├── config_manager.py         # 配置管理器
│   ├── ai_cover_generator.py     # AI 配图生成器
│   ├── upload_material.py        # 素材上传脚本
│   └── markdown_formatter.py     # Markdown 格式化器
├── assets/                      # 静态资源
│   ├── default_cover.jpg         # 默认封面图片
│   ├── article_template.html      # 文章模板
│   ├── user_custom_style.css    # 自定义样式示例
│   └── example_article.md        # 示例文章
├── references/                   # 参考资料
│   └── api_reference.md         # API 详细文档
└── SKILL.md                    # Skill 说明文档
```

---

## 🌊 默认封面

**主题：** 海浪、小船、蓝天、海鸥

**设计理念：** 宁静祥和的商务风格

**尺寸：** 900 × 383 像素（微信封面标准）

**位置：** `wxmp-draft-publisher/assets/default_cover.jpg`

**使用场景：**
- 未配置 ModelScope API Key 时自动使用
- 适合各种类型的文章
- 温和、专业的视觉风格

---

## 🔒 安全注意事项

### 1. 敏感信息保护

**已保护的文件（`.gitignore`）：**
- ✅ `wxmp_accounts.json` - 公众号配置（包含 AppSecret）
- ✅ `.env` - 环境变量配置
- ✅ `*.key` - 密钥文件

**推荐做法：**
- 使用环境变量存储 API Key
- 配置文件使用示例模板（`wxmp_accounts_example.json`）
- 不要将敏感信息提交到 Git

### 2. 配置文件分离

**实际配置（不提交）：**
- `wxmp_accounts.json`
- `.env`

**模板文件（可提交）：**
- `wxmp_accounts_example.json`
- `config.example.json`

---

## 🧪 测试

### 测试脚本

```bash
# 1. 测试默认封面功能
python3 test_default_cover.py

# 2. 测试多账号功能
python3 test_multi_account.py

# 3. 测试完整上传流程
python3 test_upload_final.py
```

### 测试检查项

- [ ] Access Token 获取成功
- [ ] 封面上传成功
- [ ] 草稿创建成功
- [ ] 默认封面正确显示
- [ ] Markdown 格式化正确
- [ ] 可以在公众号后台看到草稿

---

## 🚀 Git 上传

### 方式 1：自动化脚本

```bash
# 运行 GitHub 仓库创建脚本
bash create_github_repo.sh
```

### 方式 2：手动上传

```bash
# 1. 初始化 Git
git init

# 2. 添加 .gitignore
git add .gitignore

# 3. 添加源码
git add wxmp-draft-publisher/

# 4. 提交
git commit -m "feat: 添加微信公众号草稿箱发布 Skill"

# 5. 推送
git push -u origin master
```

---

## ❓ 常见问题

### Q1: 如何获取 ModelScope API Key？

**A:** 访问 https://www.modelscope.cn/my/myaccesstoken

### Q2: 如何配置多个公众号？

**A:** 使用配置管理器
```bash
python3 wxmp-draft-publisher/scripts/config_manager.py add <名称> <AppID> <AppSecret>
```

### Q3: 默认封面不工作？

**A:** 检查以下内容：
1. `wxmp-draft-publisher/assets/default_cover.jpg` 文件存在
2. 未配置 ModelScope API Key
3. 启用 `auto_generate_cover=True`

### Q4: 如何自定义默认封面？

**A:** 准备 900 × 383 像素的图片，替换 `default_cover.jpg`

---

## 📊 功能对比

| 功能 | 传统方式 | 本 Skill |
|------|---------|---------|
| 配图生成 | 手动设计 | AI 自动生成 |
| 封面尺寸 | 需手动调整 | 自动 900 × 383 |
| 多账号管理 | 不支持 | 支持 |
| Markdown 转换 | 需要工具 | 自动格式化 |
| 配置管理 | 复杂 | 简单配置文件 |

---

## 🎉 开始使用

现在你已经准备好使用完整的微信公众号草稿箱发布功能了！

**快速开始：**
1. 配置公众号
2. 安装依赖
3. 创建草稿
4. 发布到公众号

**获取帮助：**
- 查看 SKILL.md 了解详细功能
- 查看各功能说明文档了解特定功能
- 查看故障排查文档解决问题

祝使用愉快！🚀
