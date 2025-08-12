# Utils 模块

## 概述

`app/utils` 模块是数字人系统的核心工具库，提供了丰富的客户端接口、数据处理工具和业务逻辑支持。该模块封装了与各种外部服务的交互，包括 AI 模型服务、音视频处理、文件管理等功能，为上层业务逻辑提供稳定可靠的基础设施支持。

## 模块架构

### 客户端接口

#### 1. ComfyUI 客户端 (`comfyui_client.py`)
**功能**：与 ComfyUI 图像生成服务的完整交互接口
- **文件上传**：
  - `upload_image(local_path, overwrite, img_type, subfolder)` - 图像文件上传
  - `upload_audio(local_path, overwrite, audio_type, subfolder)` - 音频文件上传
- **图像生成**：
  - `text_to_image_flux_man(text, seed, teacache_strength)` - 男性角色图像生成
  - `text_to_image_flux_women(text, seed, teacache_strength)` - 女性角色图像生成
  - `text_to_image_flux_man_control_pose(text, seed, teacache_strength)` - 带姿态控制的男性图像生成
  - `text_to_image_flux_women_control_pose(text, seed, teacache_strength)` - 带姿态控制的女性图像生成
- **专业化生成**：
  - `run_atmospheric_shot(user_prompt, lora_name)` - 氛围镜头生成
  - `run_text_to_actor_in_mv(user_prompt, first_lora_name, second_lora_name, steps, batch_size)` - MV 演员图像生成
- **核心方法**：
  - `_run_ws_prompt(prompt)` - WebSocket 提示词执行引擎

#### 2. 图像转视频客户端 (`i2v_client.py`)
**功能**：静态图像转动态视频服务
- `infer(image_path, prompt, output_path, frame_num)` - 图像到视频的推理转换
- 支持自定义帧数和输出路径
- 内置超时处理和错误管理

#### 3. 图生对口型视频生成客户端 (`multitalk_client.py`)
**功能**：Wan-MultiTalk 视频生成服务集成
- `video_generate(image_path, audio_path)` - 基于图像和音频生成对口型视频
- `get_final_video(video_folder)` - 获取最终生成的视频文件
- `delete_tmp_folder()` - 清理临时文件夹
- 完整的视频生成工作流管理

#### 4. 图生视频生成客户端 (`wan22_i2v_client.py`)
**功能**：Wan-i2v 视频生成服务集成
- `video_generate(image_path, prompt_text, id)` - 图像到视频的推理转换
- 完整的视频生成工作流管理

#### 5. TTS 客户端 (`tts_client.py`)
**功能**：文本转语音服务接口
- `synthesize(text, audio_prompt_path)` - 基于参考音频合成语音
- `get_tts_instance()` - 全局单例实例获取
- 支持 WAV 格式音频输出
- 自动化的 API 密钥管理

#### 6. 音乐生成客户端 (`music_client.py`)
**功能**：AI 生成音乐服务接口
- `generate_no_prompt(idx, lyric)` - 根据歌词生成音乐
- `generate_text_prompt(idx, lyric, descriptions)` - 根据歌词及描述生成音乐
- `generate_auto_prompt(idx, lyric, prompt_type)` - 根据歌词及音乐风格生成音乐
- `generate_audio_prompt(idx, lyric, audio_path)` - 根据歌词及参考音乐生成音乐

#### 7. 视频对口型客户端 (`latentsync_client.py`)
**功能**：视频内人物对口型服务接口
- `process_video(video_path, audio_path, guidance_scale, inference_steps, seed)` - 为视频对口型
- 完整的视频生成工作流管理

### 业务工具类

#### 8. 数字人 MV 制作工具 (`digital_human_mv_utils.py`)
- **`recognize_lyrics(audio_file_path, model_id)`** - 识别转写歌曲歌词
- **`save_polished_lyrics(model_id, lyrics)`** - 保存修改后歌词
- **`generate_singer_prompts(style, persona_keywords)`** - 生成歌手提示词
- **`generate_singer_images(prompt, model_id)`** - 生成歌手图片
- **`generate_empty_shot_prompts(model_id,style)`** - 生成空镜提示词
- **`generate_empty_shots(model_id, prompts)`** - 生成空镜图片
- **`generate_animation_videos(model_id, image_id)`** - 生成空镜视频
- **`generate_lip_sync_videos(model_id, image_id, start_time, end_time)`** - 生成单个对口型视频
- **`generate_all_lip_sync_videos(model_id)`** - 生成所有对口型视频
- **`generate_merge_suggestion(model_id)`** - 生成MV剪辑建议
- **`merge_all_clips(model_id)`** - 剪辑最终MV

#### 9. 播客工具 (`podcast_utils.py`)
**功能**：播客内容生成的完整工具链
- **核心类 `PodcastUtils(LLMChat)`**：
  - `image_prompt_generate(script, profile, location)` - 根据台本生成图像提示词
  - `image_generate(script, profile, output_path, number_of_image)` - 批量图像生成
  - `video_prompt_generate(script)` - 视频生成提示词创建
  - `video_generate(image_path, audio_path, client)` - 视频内容生成
- **辅助函数**：
  - `audio_generate(scripts, output_name, file_path, output_path)` - 音频合成
  - `concat_videos(video_paths, output_path)` - 视频拼接
  - `srt_time(milliseconds)` - SRT 时间格式转换
  - `parse_srt_file(srt_path)` - SRT 字幕文件解析
  - `burn_subtitles(video_path, srt_path, output_path, font_file)` - 字幕烧录
  - `calculate_frame_num_from_duration(duration)` - 自适应帧数计算

#### 10. 音乐处理工具 (`music_utils.py`)
**功能**：音乐生成相关的枚举和工具
- **生成函数**：
  - `generate_lyrics(prompt)` - 使用LLM生成结构化歌词
  - `transform_lyrics(lyrics)` - 将歌词转换为字符串形式
- **`AudioPromptType` 枚举**：
  - 支持 12 种音乐风格：流行、蓝调、舞蹈、爵士、民谣、摇滚、国风、古风、金属、雷鬼、京剧、自动
  - 提供中英文标签映射

#### 11. 视频处理工具 (`video_utils.py`)
**功能**：视频编辑和处理功能
- **剪辑数字人MV视频**：
  - `find_video_file(video_name, video_extensions)` - 找到目录下的素材视频
  - `get_video_duration(video_path)` - 计算素材片段时长
  - `create_video_segment(video_path, start_time, end_time, output_path, is_empty_shot)` - 处理视频
  - `create_concat_file(segments, concat_file_path)` - 创建视频拼接辅助文件
  - `splice_videos(json_data, audio_file, subtitle_file)` - 拼接素材并添加音频与字幕
- **剪辑数字人全身克隆视频**：
  - `create_clone_video_segment(video_path, output_path)` - 处理视频
  - `splice_clone_videos(audio_file)` - 拼接素材并添加音频

#### 12. 音频处理工具 (`audio_utils.py`)
**功能**：音频编辑和处理功能
- **`asr_recognize(audio_path, no_speech_threshold, temperature, extra_params, return_raw) `** - 对音频进行语音识别并返回文字转写
- **`extract_audio_and_silent_video(input_video_path)`** - 提取视频音轨并静音视频
- **`slice_audio_at_silence(input_file, output_dir, target_duration, min_duration, min_silence_len, silence_thresh)`** - 在静音处切割视频

#### 13. 风格转换处理工具 (`style_transform_utils.py`)
**功能**：转变图片及视频风格
- **生成函数**：
  - `image_transformtion (image_path, output_model_id, prompt, lora_file)` - 图片转绘服务
  - `video_transformtion (video_path, output_model_id, lora_file) ` - 视频转绘服务
- **`LoraStyle` 枚举**：
  - 支持 6 种转绘风格：吉卜力风格、JOJO漫画风格、日本小人风格、黏土风格、LEGO风格、3D吉卜力风格
  - 提供中英文标签映射

#### 14. 其它业务工具 
- **`task_utils.py`** - 任务管理和调度工具

### 系统工具

#### 15. 目录管理工具 (`dir_utils.py`)
**功能**：文件和目录操作的统一管理
- `move_file_to_output_dir(src_path, output_dir)` - 将原文件移动到输出目录
- `save_to_model_dir(model_id, file)` - 保存用户上传的文件并返回路径
- `save_temp_data_to_model_dir(model_id, file)` - 临时数据保存到模型目录
- 基于模型 ID 的目录结构组织
- JSON 数据持久化支持

#### 16. 通用工具 (`utils.py`)
**功能**：系统级通用工具函数
- `calculate_frame_num_from_wav(wav_bytes)` - 计算音频帧数
- `extract_think_answer(text)` - 去除大模型回答里的思考部分
- `new_model_id()` - 生成32位 UUID 作为模型编号
- `image_to_base64(image_path)` - 图像 Base64 编码转换
- `save_base64_image(base64_str, model_id, position)` - 将 base64 字符串转换为图片文件并保存
- `cut_audio(audio_file_path, start_time, end_time, out_filename)` - 按照开始与结束时间截取音频
- `convert_json_data_to_srt(json_data)` - 将歌词json转换为srt格式
- `extract_first_frame(video_path, output_image_path)` - 截取视频第一帧并保存

## 设计特点

### 统一的客户端模式
所有外部服务客户端都采用统一的设计模式：
- 构造函数接收 URL 和可选的 API 密钥
- 统一的错误处理和超时管理
- 标准化的请求/响应格式

### 单例模式支持
关键服务客户端实现单例模式：
```python
# TTS 客户端单例获取
tts_client = get_tts_instance()
```

### 异步处理支持
多个工具类支持异步操作，提高系统并发性能。

## 使用示例

### ComfyUI 图像生成

```python
from app.utils.comfyui_client import ComfyUIClient

client = ComfyUIClient()

# 上传图像
file_info = client.upload_image("/path/to/image.jpg")

# 生成男性角色图像
images = client.text_to_image_flux_man(
    text="A handsome young man in casual clothes",
    seed=12345,
    teacache_strength=0.4
)

# 生成氛围镜头
atmospheric_images = client.run_atmospheric_shot(
    user_prompt="A cat lying on a windowsill in sunset light",
    lora_name="Flux/胶片摄影 F.1_ 电影镜头_3.0.safetensors"
)
```

### 播客内容生成

```python
from app.utils.podcast_utils import PodcastUtils

podcast_utils = PodcastUtils()

# 生成图像提示词
image_prompt = podcast_utils.image_prompt_generate(
    script="欢迎收听今天的播客节目",
    profile="年轻的女性主持人，短发，职业装",
    location="现代化的播客录音室"
)

# 生成播客图像
images = podcast_utils.image_generate(
    script="欢迎收听今天的播客节目",
    profile="年轻的女性主持人",
    number_of_image=4
)
```

### TTS 语音合成

```python
from app.utils.tts_client import get_tts_instance

tts_client = get_tts_instance()

# 合成语音
audio_bytes = tts_client.synthesize(
    text="你好，欢迎收听我们的播客节目",
    audio_prompt_path="/path/to/reference_audio.wav"
)

# 保存音频文件
with open("output.wav", "wb") as f:
    f.write(audio_bytes)
```

### 视频处理

```python
from app.utils.podcast_utils import concat_videos, burn_subtitles

# 拼接多个视频
video_paths = ["video1.mp4", "video2.mp4", "video3.mp4"]
concat_videos(video_paths, "final_video.mp4")

# 烧录字幕
burn_subtitles(
    video_path="input_video.mp4",
    srt_path="subtitles.srt",
    output_path="output_with_subtitles.mp4"
)
```

## 配置管理

所有工具类都通过 `app.config.settings` 进行统一配置：

## 错误处理

### 统一异常处理
所有客户端都实现了统一的异常处理机制：
- 网络超时处理
- HTTP 状态码检查
- 文件操作异常捕获
- 资源清理保证

## 扩展指南

### 添加新的客户端
1. 继承统一的客户端基类模式
2. 实现标准的初始化方法
3. 添加统一的错误处理
4. 实现单例模式（如需要）

### 添加新的工具函数
1. 选择合适的工具文件或创建新文件
2. 遵循现有的命名约定
3. 添加完整的文档字符串
4. 实现单元测试

## 依赖管理

### 核心依赖
- `requests` - HTTP 客户端
- `pathlib` - 路径处理
- `base64` - 编码转换
- `json` - 数据序列化

### 专业依赖
- `moviepy` - 视频处理
- `PIL/Pillow` - 图像处理
- `numpy` - 数值计算
