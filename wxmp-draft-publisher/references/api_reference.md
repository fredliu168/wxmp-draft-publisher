# 微信公众号草稿箱发布 API 参考

## 1. 获取 Access Token

### API 地址
```
GET https://api.weixin.qq.com/cgi-bin/token
```

### 请求参数
| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| grant_type | string | 是 | 填 client_credential |
| appid | string | 是 | 公众号的 AppID |
| secret | string | 是 | 公众号的 AppSecret |

### 返回参数
```json
{
  "access_token": "ACCESS_TOKEN",
  "expires_in": 7200
}
```

### 注意事项
- access_token 有效期为 2 小时（7200 秒）
- 建议在服务端缓存 access_token，避免频繁请求
- 正常情况下 access_token 有效期内重复获取会返回相同的结果

---

## 2. 新建草稿

### API 地址
```
POST https://api.weixin.qq.com/cgi-bin/draft/add?access_token=ACCESS_TOKEN
```

### 请求参数
```json
{
  "articles": [
    {
      "title": "文章标题",
      "content": "文章内容，支持HTML",
      "thumb_media_id": "封面图片media_id",
      "author": "作者",
      "digest": "摘要",
      "show_cover_pic": 1,
      "need_open_comment": 1,
      "only_fans_can_comment": 0
    }
  ]
}
```

### 参数说明
| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| title | string | 是 | 文章标题 |
| content | string | 是 | 文章内容，HTML 格式 |
| thumb_media_id | string | 是 | 封面图片的 media_id |
| author | string | 否 | 作者 |
| digest | string | 否 | 摘要，不填则自动抓取 |
| show_cover_pic | int | 否 | 是否显示封面，0 不显示，1 显示（默认 0） |
| need_open_comment | int | 否 | 是否打开评论，0 不打开，1 打开（默认 0） |
| only_fans_can_comment | int | 否 | 是否只有粉丝可以评论，0 所有人，1 粉丝（默认 0） |

### 返回参数
```json
{
  "errcode": 0,
  "errmsg": "ok",
  "media_id": "MEDIA_ID"
}
```

### 注意事项
- 支持一次创建多图文，最多 8 篇
- 内容中的图片必须使用永久素材的 URL
- thumb_media_id 必须是永久素材的 media_id

---

## 3. 更新草稿

### API 地址
```
POST https://api.weixin.qq.com/cgi-bin/draft/update?access_token=ACCESS_TOKEN
```

### 请求参数（更新单篇文章）
```json
{
  "media_id": "MEDIA_ID",
  "index": 0,
  "article": {
    "title": "新标题",
    "content": "新内容",
    "thumb_media_id": "新封面media_id"
  }
}
```

### 请求参数（更新整个图文消息）
```json
{
  "media_id": "MEDIA_ID",
  "articles": [...]
}
```

### 参数说明
| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| media_id | string | 是 | 要更新的草稿 media_id |
| index | int | 是 | 要更新的文章位置（从 0 开始） |
| article | object | 否 | 更新的文章内容 |
| articles | array | 否 | 完整的图文消息列表 |

### 返回参数
```json
{
  "errcode": 0,
  "errmsg": "ok"
}
```

---

## 4. 发布草稿

### API 地址
```
POST https://api.weixin.qq.com/cgi-bin/freepublish/submit?access_token=ACCESS_TOKEN
```

### 请求参数
```json
{
  "media_id": "MEDIA_ID"
}
```

### 参数说明
| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| media_id | string | 是 | 要发布的草稿 media_id |

### 返回参数
```json
{
  "errcode": 0,
  "errmsg": "ok",
  "publish_id": "PUBLISH_ID",
  "msg_data_id": "MSG_DATA_ID"
}
```

### 发布状态说明
- 发布是异步操作，需要通过查询接口获取最终状态
- 发布成功后会返回 publish_id，用于后续查询状态

---

## 5. 查询发布状态

### API 地址
```
POST https://api.weixin.qq.com/cgi-bin/freepublish/get?access_token=ACCESS_TOKEN
```

### 请求参数
```json
{
  "publish_id": "PUBLISH_ID"
}
```

### 参数说明
| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| publish_id | string | 是 | 发布任务 ID |

### 返回参数
```json
{
  "errcode": 0,
  "errmsg": "ok",
  "publish_id": "PUBLISH_ID",
  "publish_status": 0,
  "article_id": "ARTICLE_ID",
  "article_detail": {
    "articles": [...]
  },
  "fail_reason": ""
}
```

### publish_status 状态码
| 状态码 | 说明 |
|--------|------|
| 0 | 发布成功 |
| 1 | 发布中（异步发布中） |
| 2 | 审核失败（fail_reason 有详细说明） |
| 3 | 审核中 |
| 4 | 已撤回 |
| 5 | 通过审核 |
| 6 | 用户删除 |

---

## 6. 删除草稿

### API 地址
```
POST https://api.weixin.qq.com/cgi-bin/draft/delete?access_token=ACCESS_TOKEN
```

### 请求参数
```json
{
  "media_id": "MEDIA_ID"
}
```

### 参数说明
| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| media_id | string | 是 | 要删除的草稿 media_id |

### 返回参数
```json
{
  "errcode": 0,
  "errmsg": "ok"
}
```

---

## 7. 上传永久素材（图片）

### API 地址
```
POST https://api.weixin.qq.com/cgi-bin/material/add_material?access_token=ACCESS_TOKEN&type=thumb
```

### 请求参数
使用 multipart/form-data 格式上传文件

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| media | file | 是 | 图片文件（jpg, png） |
| description | string | 否 | 图片描述 |

### 返回参数
```json
{
  "errcode": 0,
  "errmsg": "ok",
  "media_id": "MEDIA_ID",
  "url": "https://mmbiz.qpic.cn/..."
}
```

### 注意事项
- 封面图片必须是永久素材
- 图片大小限制：2MB 以内
- 支持格式：JPG, PNG
- 建议尺寸：900 × 383 像素（2.35:1）

---

## 8. 上传临时素材

### API 地址
```
POST https://api.weixin.qq.com/cgi-bin/media/upload?access_token=ACCESS_TOKEN&type=image
```

### 请求参数
使用 multipart/form-data 格式上传文件

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| media | file | 是 | 图片文件 |

### 返回参数
```json
{
  "errcode": 0,
  "errmsg": "ok",
  "type": "image",
  "media_id": "MEDIA_ID",
  "created_at": 1234567890
}
```

### 注意事项
- 临时素材有效期仅为 3 天
- 适用于测试或临时使用的场景

---

## 9. 错误码说明

### 通用错误码
| 错误码 | 说明 |
|--------|------|
| -1 | 系统繁忙 |
| 0 | 请求成功 |
| 40001 | AppSecret 错误或 AppSecret 不属于这个应用 |
| 40002 | 不支持的 grant_type |
| 40013 | 不合法的 AppID |
| 40014 | 不合法的 access_token |
| 42001 | access_token 超时 |
| 42002 | refresh_token 超时 |
| 43001 | 需要 GET 请求 |
| 43002 | 需要 POST 请求 |
| 43003 | 需要 HTTPS 请求 |
| 44001 | 多媒体文件为空 |
| 44002 | POST 的数据包为空 |
| 44003 | 图文消息内容为空 |
| 44004 | 文本消息内容为空 |
| 45001 | 多媒体文件大小超过限制 |
| 45002 | 消息内容超过限制 |
| 45003 | 标题字段超过限制 |
| 45004 | 描述字段超过限制 |
| 45005 | 链接字段超过限制 |
| 45006 | 图片链接字段超过限制 |
| 45007 | 语音播放时间超过限制 |
| 45008 | 图文消息超过限制 |
| 45009 | 接口调用超过限制 |
| 45010 | 创建菜单个数超过限制 |
| 45011 | API 调用太频繁，请稍候再试 |
| 45015 | 回复时间超过限制 |
| 45016 | 系统分组，不允许修改 |
| 45017 | 分组名字过长 |
| 45018 | 分组数量超过限制 |
| 46001 | 媒体类型不正确 |
| 46002 | 不合法的媒体文件 id |
| 46003 | 不合法的 media_id 长度 |
| 47001 | 解析 JSON/XML 内容错误 |
| 85002 | 专题草稿素材不合法 |
| 85003 | 专题草稿内容不合法 |
| 85004 | 专题草稿已存在 |
| 85005 | 专题草稿不存在 |
| 85006 | 发布状态不合法 |
| 85007 | 发布任务不存在 |
| 85008 | 媒体 ID 不合法 |
| 85009 | 用户权限不满足 |
| 85010 | 标题为空且内容也为空 |

---

## 使用流程

### 完整发布流程

1. **获取 access_token**
   ```
   GET /cgi-bin/token?grant_type=client_credential&appid=APPID&secret=APPSECRET
   ```

2. **上传封面图片（永久素材）**
   ```
   POST /cgi-bin/material/add_material?type=thumb
   获取 thumb_media_id
   ```

3. **新建草稿**
   ```
   POST /cgi-bin/draft/add
   使用 thumb_media_id 创建草稿
   获取 media_id
   ```

4. **发布草稿**
   ```
   POST /cgi-bin/freepublish/submit
   使用 media_id 发布草稿
   获取 publish_id
   ```

5. **查询发布状态（可选）**
   ```
   POST /cgi-bin/freepublish/get
   使用 publish_id 查询发布状态
   ```

### 最佳实践

1. **access_token 缓存**
   - 在服务端缓存 access_token，避免频繁请求
   - 设置合理的过期时间（建议 1.5 小时）
   - 提前刷新 access_token，避免过期

2. **错误处理**
   - 实现完善的错误处理机制
   - 对于网络错误实现重试机制
   - 记录详细的错误日志

3. **素材管理**
   - 封面图片使用永久素材
   - 测试时可以使用临时素材
   - 保存 media_id 以便复用

4. **发布时机**
   - 发布是异步操作，需要轮询查询状态
   - 避免频繁发布，受频率限制
   - 建议在用户量较少的时间段发布

5. **内容审核**
   - 发布后会进入审核流程
   - 审核失败会返回 fail_reason
   - 审核通过后文章才可见
