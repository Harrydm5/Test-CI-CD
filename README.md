# Routers 模块

## 概述

`app/routers` 模块是数字人系统的 API 路由层，基于 FastAPI 框架构建，提供了完整的 RESTful API 接口服务。该模块按功能领域划分为多个路由文件，涵盖了数字人内容生成、音视频处理、数据库操作等核心业务功能。

## 模块架构

### 核心路由模块

#### 1. 数字人一般视频生成服务

##### 数字人克隆 (`clone_routes.py`)
**功能**：创建不同种类的数字人克隆
- `POST /clone/image_head` - 图片+音频 动头克隆
- `POST /clone/image_fullbody` - 图片+音频 动全身克隆
- `POST /clone/video_audio` - 视频+音频 克隆
- `POST /clone/image_skeleton` - 图片+骨骼 克隆
- `POST /clone/image_dance` - 图片 + 参考舞蹈 克隆数字人

##### 数字人合成 (`synthesize_routes.py`)
**功能**：多种数字人视频合成模式
- `POST /synthesize/image_head/{model_id}` - 头像模型视频合成
- `POST /synthesize/image_fullbody/{model_id}` - 全身模型视频合成
- `POST /synthesize/video_audio/{model_id}` - 带音频视频模型合成
- `POST /synthesize/image_skeleton/{model_id}` - 骨架模型视频合成
- `POST /synthesize/video_only/{model_id}` - 纯视频模型合成
- `POST /synthesize/image_dance/{model_id}` - 舞蹈模型视频合成

##### 数字人克隆并合成 (`digital_human_routes.py`)
**功能**：一次性完成各种数字人的创建与生成
- `POST /digital_human/image_head` - 头像数字人生成
- `POST /digital_human/image_fullbody` - 全身数字人生成
- `POST /digital_human/video_audio` - 视频音频数字人生成
- `POST /digital_human/image_skeleton` - 骨骼驱动数字人生成
- `POST /digital_human/video_only` - 纯视频数字人生成
- `POST /digital_human/image_dance` - 舞蹈数字人生成

#### 2. 数字人音乐视频生成服务

##### 音乐生成 (`music_routes.py`)
**功能**：AI 音乐创作
- `POST /music/noprompt` - 基于歌词生成音乐
- `POST /music/textprompt` - 基于歌词及文本提示生成音乐
- `POST /music/autoprompt` - 基于歌词及固定风格生成音乐
- `POST /music/audioprompt` - 基于歌词及音频提示生成音乐
- `POST /music/generate` - AI 根据要求生成歌词

##### 数字人 MV 制作 (`digital_human_mv_routes.py`)
**功能**：数字人音乐视频制作的完整工作流程
- `POST /mv/prepare_song` - 识别上传音乐的歌词
- `POST /mv/receive_lyrics` - 接收并处理修改后的歌词
- `POST /mv/create_singer_prompt` - 生成歌手形象提示词
- `POST /mv/create_singer` - 生成歌手形象
- `POST /mv/create_empty_shot_prompt` - 生成空镜头提示词
- `POST /mv/empty_shot` - 生成空镜头图片
- `POST /mv/animation` - 空镜动画视频生成
- `POST /mv/singing` - 数字人单个对口型视频生成
- `POST /mv/singing_all` - 数字人所有对口型视频生成
- `POST /mv/generate_suggestion` - MV 素材拼接建议
- `POST /mv/merge_video` - 视频片段合并成完整 MV

#### 3. 数字人播客视频生成服务

##### 播客制作 (`podcast_routers.py`)
**功能**：播客内容自动化生成
- `POST /podcast/generate` - 根据主题、角色和参考音频生成播客

#### 4. 多模态文件转换服务

##### 文本转语音 (`tts_routers.py`)
**功能**：语音合成和音频处理
- `POST /tts/upload` - 上传音频并生成机器人回复
- `POST /tts` - 基于参考音频生成 TTS

##### 文本转图像 (`text2img_routes.py`)
- `POST /text2img` - 文本描述生成图像

##### 图像转视频 (`img2video_routes.py`)
- `POST /img2video/generate` - 静态图像转换为动态视频

##### 风格转换 (`style_transform_routes.py`)
- `POST /transform/image_transform` - 图像风格转换
- `POST /transform/video_transform` - 视频风格转换

#### 5. 辅助服务

##### 数据库操作 (`database_routers.py`)
**功能**：模型数据管理
- `GET /database/model/{model_id}/reference_text` - 查询模型参考文本
- `PUT /database/model/{model_id}/reference_text` - 更新模型参考文本

##### 上传媒体文件 (`upload_media_routers.py`)
**功能**：接收用户上传的媒体文件（图片/视频/音频）
- `POST /upload_media//upload` - 上传媒体文件

## API 设计特点

### 统一的响应格式
所有 API 接口遵循统一的响应格式，包含：
- 状态码和消息
- 数据载荷
- 错误处理机制

### 文件上传支持
多个接口支持文件上传，包括：
- 图像文件（JPG、PNG 等）
- 音频文件（WAV、MP3、FLAC 等）
- 视频文件（MP4、AVI 等）

### 流式响应
部分接口支持流式响应，如：
- 音频文件下载
- 大文件传输

## 使用示例

### 数字人 MV 制作流程

```python
# 1. 上传音乐
response = requests.post("/upload_media//upload", json={
    "file": "example_file",
})

# 2. 识别歌词
response = requests.post("/mv/prepare_song", json={
    "song_id": "example_song_id",
})

# 3. 生成歌手
# Step 1:
response = requests.post("/mv/create_singer_prompt", json={
    "model_id": "example_model_id",
    "style": "落日夕阳",
    "persona_keywords": "黑色短发、红色围巾、驼色大衣、贝雷帽",
})

# Step 2: 
response = requests.post("/mv/create_singer", json={
    "model_id": "example_model_id",
    "singer_prompt": "prompt from first step",
})

# 4. 生成空镜视频
# Step 1:
response = requests.post("/mv/create_empty_shot_prompt", json={
    "model_id": "example_model_id",
    "style": "落日夕阳"
})

# Step 2:
response = requests.post("/mv/empty_shot", json={
    "model_id": "example_model_id",
    "prompts": "prompts from first step"
})

# Step 3:
response = requests.post("/mv/animation", json={
    "model_id": "example_model_id",
    "image_id": "example_image_id"
})

# 5. 生成剪辑建议
response = requests.post("/mv/generate_suggestion", json={
    "model_id": "example_model_id",
})

# 6. 生成所有对口型视频
response = requests.post("/mv/singing_all", json={
    "model_id": "example_model_id",
})

# 7. 剪辑最终视频
response = requests.post("/mv/merge_video", json={
    "model_id": "example_model_id",
})
```

### 图像风格转换

```python
# 上传图像并应用风格转换
files = {"image_file": open("input.jpg", "rb")}
data = {
    "prompt": "转换为动漫风格",
    "lora_style": "吉卜力3D"
}
response = requests.post("/image_transform", files=files, data=data)
```

### TTS 语音合成

```python
# 基于参考音频生成语音
data = {
    "audio_id": "reference_audio_id",
    "text": "需要合成的文本内容"
}
response = requests.post("/generate_tts", data=data)
```

## 错误处理

所有路由都实现了统一的错误处理机制：

- **400 Bad Request** - 请求参数错误或模型不存在
- **404 Not Found** - 资源未找到
- **500 Internal Server Error** - 服务器内部错误

## 性能优化

### 异步处理
大部分路由使用 `async/await` 模式，支持高并发请求处理。

### 文件处理
- 支持大文件上传
- 临时文件自动清理
- 流式文件传输

### 缓存策略
建议在生产环境中实现：
- 响应缓存
- 文件缓存
- 数据库查询缓存

## 部署说明

### 依赖要求
- FastAPI
- Uvicorn
- Python-multipart（文件上传支持）
- 各种 AI 模型服务依赖

### 配置管理
路由配置通过 `app.config.settings` 统一管理，包括：
- 模型服务端点
- 文件存储路径
- 数据库连接信息

## 扩展指南

### 添加新路由
1. 在相应的路由文件中定义新的端点
2. 实现请求/响应模型（在 `app/schemas` 中）
3. 添加业务逻辑处理
4. 更新 API 文档

## 注意事项

- 所有文件上传接口都有大小限制
- 长时间运行的任务建议使用异步任务队列
- 定期清理临时文件和缓存
- 监控系统资源使用情况
