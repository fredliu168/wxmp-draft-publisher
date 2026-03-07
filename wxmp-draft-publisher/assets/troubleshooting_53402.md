# 错误码 53402 解决方案

## 错误信息

```
错误: 新建草稿失败: {'errcode': 53402, 'errmsg': '封面裁剪失败，请检查裁剪参数后重试'}
```

## 问题原因

微信公众号对封面图片有严格的尺寸要求：
- **标准尺寸**：900 × 383 像素
- **比例要求**：2.35:1
- **文件大小**：不超过 2MB
- **格式要求**：JPG 或 PNG

如果图片尺寸不符合要求，微信服务器会裁剪失败，返回错误码 53402。

## 解决方案

### 方案 1：使用自动调整功能（推荐）

更新后的 `upload_material.py` 脚本已内置图片自动调整功能：

```bash
python3 scripts/upload_material.py <APPID> <APPSECRET> image <图片路径>
```

脚本会自动：
1. 检查图片尺寸
2. 智能裁剪为 900 × 383 像素
3. 保持图片主要内容居中
4. 上传符合要求的封面

### 方案 2：手动调整图片

使用 `image_processor.py` 工具手动调整：

```bash
# 检查图片信息
python3 scripts/image_processor.py <图片路径>

# 调整图片并保存
python3 scripts/image_processor.py input.jpg output.jpg
```

### 方案 3：使用图片编辑软件

手动调整图片尺寸为 900 × 383 像素：

1. 打开图片编辑工具（Photoshop、GIMP 等）
2. 调整画布大小或裁剪图片为 900 × 383
3. 确保比例为 2.35:1
4. 保存为 JPG 格式，质量 95 以上

## 图片规格要求

| 项目 | 要求 |
|------|------|
| 宽度 | 900 像素 |
| 高度 | 383 像素 |
| 比例 | 2.35:1 |
| 文件大小 | ≤ 2MB |
| 格式 | JPG, PNG |
| 颜色模式 | RGB |

## 快速测试

### 测试图片是否合规

```bash
python3 scripts/image_processor.py test.jpg
```

输出示例：
```
=== 检查图片信息 ===
尺寸: 1200 × 600
比例: 2.00
格式: JPEG
模式: RGB
大小: 1.23 MB
符合封面要求: 否

=== 调整图片尺寸 ===
原始图片尺寸: 1200 × 600
调整后图片尺寸: 900 × 383
图片已保存到: test.jpg

完成！图片路径: test.jpg
```

## 完整使用流程

### 正确的发布流程

```bash
# 1. 调整并上传封面图片（自动调整）
python3 scripts/upload_material.py <APPID> <APPSECRET> image cover.jpg

# 输出：media_id: wx_cover_media_id_123

# 2. 创建草稿
python3 scripts/publish_draft.py <APPID> <APPSECRET> create wx_cover_media_id_123 "标题" content.html

# 输出：media_id: draft_media_id_456

# 3. 发布草稿
python3 scripts/publish_draft.py <APPID> <APPSECRET> publish draft_media_id_456
```

## 常见问题

### Q1: 为什么需要 900 × 383 这个尺寸？

这是微信公众号的标准封面尺寸，比例为 2.35:1，与电影银幕比例相同，视觉效果最好。

### Q2: 可以使用其他尺寸吗？

虽然其他尺寸也可能上传成功，但可能会导致：
- 封面显示不完整
- 图像被裁剪变形
- 影响整体美观

### Q3: PNG 和 JPG 哪个更好？

- **JPG**：适合照片，文件更小
- **PNG**：适合带透明背景的图片

建议使用 JPG 格式，压缩率更好。

### Q4: 如果图片很大怎么办？

脚本会自动裁剪，但建议：
1. 裁剪后再上传，速度更快
2. 压缩到 1-1.5MB 为佳
3. 保持图片清晰度

### Q5: 如何批量处理多张图片？

使用脚本循环处理：

```bash
for img in *.jpg; do
    python3 scripts/image_processor.py "$img"
done
```

## 技术细节

### 图片裁剪算法

脚本使用智能裁剪算法：

1. **计算缩放比例**：保持图片填满目标尺寸
2. **等比缩放**：避免图片变形
3. **居中裁剪**：保留图片中心内容
4. **高质量重采样**：使用 LANCZOS 算法

### 代码示例

```python
# 计算缩放比例
scale = max(target_width / width, target_height / height)

# 等比缩放
img = img.resize((new_width, new_height), Image.Resampling.LANCZOS)

# 居中裁剪
left = (new_width - target_width) // 2
top = (new_height - target_height) // 2
img = img.crop((left, top, right, bottom))
```

## 更新日志

### v1.1 (最新)
- ✅ 添加图片自动调整功能
- ✅ 添加图片规格检查工具
- ✅ 改进错误提示信息
- ✅ 优化裁剪算法

### v1.0
- ✅ 基础素材上传功能
- ✅ 草稿创建和发布功能

## 需要帮助？

如果问题仍然存在，请检查：
1. 图片文件是否损坏
2. 网络连接是否正常
3. 微信公众号权限是否正确
4. AppID 和 AppSecret 是否有效

参考详细文档：`references/api_reference.md`
