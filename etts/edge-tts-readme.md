# Edge TTS Worker

Edge TTS Worker 是一个部署在 Cloudflare Worker 上的代理服务，它将微软 Edge TTS 服务封装成兼容 OpenAI 格式的 API 接口。通过本项目，您可以在没有微软认证的情况下，轻松使用微软高质量的语音合成服务。

## 📑 目录

- [✨ 特点](#-特点)
- [🚀 快速部署](#-快速部署)
- [🧪 测试脚本使用](#-测试脚本使用)
- [🔧 API 使用说明](#-api-使用说明)
- [📝 注意事项](#-注意事项)
- [❓ 常见问题](#-常见问题)
- [📄 许可证](#-许可证)

## ✨ 特点

- 绕过大陆地区访问限制，免去微软服务认证步骤
- 提供 OpenAI 兼容的接口格式
- 完全免费 - 基于 Cloudflare Worker 免费计划
- 安全可控 - 支持自定义 API 密钥
- 多语种支持 - 中文、英文、日文、韩文等
- 快速部署 - 几分钟内即可完成

## 🚀 快速部署

### 1. 创建 Worker
1. 登录 [Cloudflare Dashboard](https://dash.cloudflare.com/)
2. 进入 `Workers & Pages`
3. 点击 `Create Worker`
4. 为 Worker 取个名字（比如 `edge-tts`）

### 2. 部署代码
1. 删除编辑器中的默认代码
2. 复制 [worker.js](worker.js) 中的代码并粘贴
3. 点击 `Save and deploy`

### 3. 设置 API Key（可选）
1. 在 Worker 的设置页面中找到 `Settings` -> `Variables`
2. 点击 `Add variable`
3. 名称填写 `API_KEY`，值填写你想要的密钥
4. 点击 `Save and deploy`

### 4. 配置自定义域名（可选）

#### 前提条件
- 你的域名已经托管在 Cloudflare
- 域名的 DNS 记录已经通过 Cloudflare 代理（代理状态为橙色云朵）

#### 配置步骤
1. 在 Worker 的详情页面中
2. 点击 `设置` 标签
3. 找到 `域和路由` 部分
4. 点击 `添加` 按钮
5. 选择 `自定义域`
6. 输入你想要使用的域名（比如 `tts.example.com`）
7. 点击 `添加域`
8. 等待证书部署完成（通常几分钟内）

完成后，你可以通过以下两种方式访问服务：
- Workers 域名：`https://你的worker名字.你的用户名.workers.dev`
- 自定义域名：`https://tts.example.com`

> 注意：自定义域名必须使用 HTTPS，Cloudflare 会自动提供 SSL 证书。

## 🧪 测试脚本使用

为了方便测试不同的语音效果，项目提供了一个测试脚本。这个脚本会使用不同的语音来合成相同的文本，帮助你快速对比各种语音效果。

### 使用方法

1. 下载测试脚本 `test_voices.sh`
2. 给脚本添加执行权限：
```bash
chmod +x test_voices.sh
```
3. 运行脚本：
```bash
./test_voices.sh <Worker地址> [API密钥]
```

示例：
```bash
# 使用 API 密钥
./test_voices.sh https://your-worker.workers.dev your-api-key

# 不使用 API 密钥
./test_voices.sh https://your-worker.workers.dev
```

脚本会为每个支持的语音生成测试音频文件，你可以通过播放这些文件来选择最适合的语音。

## 🔧 API 使用说明

### 基础用法

**最简单的调用方式：**
```bash
curl -X POST https://你的worker地址/v1/audio/speech \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your-api-key" \
  -d '{
    "model": "tts-1",
    "input": "你好，世界！",
    "voice": "zh-CN-XiaoxiaoNeural"
  }' --output output.mp3
```

### 高级功能

**语音情绪控制**
```bash
curl -X POST https://你的worker地址/v1/audio/speech \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your-api-key" \
  -d '{
    "model": "tts-1",
    "input": "这是一段开心的话！",
    "voice": "zh-CN-XiaoxiaoNeural",
    "style": "cheerful",
    "speed": 1.2
  }' --output happy.mp3
```


### 参数说明

| 参数 | 类型 | 必填 | 说明 | 默认值 | 示例值 |
|------|------|------|------|--------|--------|
| model | string | 是 | 模型名称（固定值） | - | tts-1 |
| input | string | 是 | 要转换的文本内容 | - | "你好，世界！" |
| voice | string | 是 | 语音角色名称 | - | zh-CN-XiaoxiaoNeural |
| response_format | string | 否 | 音频输出格式 | mp3 | mp3 |
| speed | number | 否 | 语速调节 (0.5-2.0) | 1.0 | 1.2 |
| pitch | number | 否 | 音调调节 (0.5-2.0) | 1.0 | 1.1 |
| style | string | 否 | 语音情绪风格 | general | cheerful |

### 语音角色说明

#### OpenAI 兼容语音映射

请根据实际需要，在worker.js中添加/修改对应的映射关系。

| OpenAI 语音 | 对应微软语音角色 | 特点描述 |
|------------|-----------------|----------|
| alloy      | zh-CN-XiaoxiaoNeural | 晓晓 - 温暖自然的女声 |
| echo       | zh-CN-YunxiNeural | 云希 - 稳重大气的男声 | 
| fable      | zh-CN-XiaoyiNeural | 晓伊 - 亲切温柔的女声 |
| onyx       | zh-CN-YunyangNeural | 云扬 - 专业权威的男声 |
| nova       | zh-CN-XiaohanNeural | 晓涵 - 清新活泼的女声 |
| shimmer    | zh-CN-XiaomengNeural | 晓梦 - 甜美动人的女声 |

### 使用注意事项

1. **语音与文本匹配**
   - 中文语音(zh-CN)仅支持中文文本
   - 英文语音(en-US)仅支持英文文本
   - 日文语音(ja-JP)仅支持日文文本
   - 韩文语音(ko-KR)仅支持韩文文本

   错误的语音文本匹配可能导致:
   - 发音不自然
   - 语音合成失败
   - API 返回错误
   - 音频质量下降

2. **多语种支持示例**
   ```javascript
   // 根据文本语言自动选择合适的语音
   function selectVoice(text) {
     if(/[\u4e00-\u9fa5]/.test(text)) {
       return 'zh-CN-XiaoxiaoNeural';  // 中文
     } else if(/^[a-zA-Z\s,.!?]+$/.test(text)) {
       return 'en-US-JennyNeural';     // 英文
     } else if(/[\u3040-\u30ff]/.test(text)) {
       return 'ja-JP-NanamiNeural';    // 日文
     } else if(/[\uAC00-\uD7AF]/.test(text)) {
       return 'ko-KR-SunHiNeural';     // 韩文
     }
     return 'zh-CN-XiaoxiaoNeural';    // 默认中文
   }
   ```

### 支持的语音列表

> 完整的语音支持列表请参考[微软官方文档](https://learn.microsoft.com/zh-cn/azure/cognitive-services/speech-service/language-support?tabs=tts)

| 语音代码 | 描述 | 语言 |
|----------|------|------|
| zh-CN-XiaoxiaoNeural | 晓晓 - 温暖活泼 | 中文 |
| zh-CN-XiaoyiNeural | 晓伊 - 温暖亲切 | 中文 |
| zh-CN-YunxiNeural | 云希 - 男声，稳重 | 中文 |
| zh-CN-YunyangNeural | 云扬 - 男声，专业 | 中文 |
| zh-CN-XiaohanNeural | 晓涵 - 自然流畅 | 中文 |
| zh-CN-XiaomengNeural | 晓梦 - 甜美活力 | 中文 |
| zh-CN-XiaochenNeural | 晓辰 - 温和从容 | 中文 |
| zh-CN-XiaoruiNeural | 晓睿 - 男声，儒雅 | 中文 |
| zh-CN-XiaoshuangNeural | 晓双 - 女声，温柔 | 中文 |
| zh-CN-YunfengNeural | 云枫 - 男声，成熟 | 中文 |
| zh-CN-YunjianNeural | 云健 - 男声，阳光 | 中文 |
| zh-CN-XiaoxuanNeural | 晓萱 - 女声，知性 | 中文 |
| zh-CN-YunxiaNeural | 云夏 - 男声，青春 | 中文 |
| zh-CN-XiaomoNeural | 晓墨 - 女声，优雅 | 中文 |
| zh-CN-XiaozhenNeural | 晓甄 - 女声，自信 | 中文 |
| en-US-JennyNeural | Jenny | 英文 |
| en-US-GuyNeural | Guy | 英文 |
| ja-JP-NanamiNeural | Nanami | 日文 |
| ja-JP-KeitaNeural | Keita | 日文 |
| ko-KR-SunHiNeural | Sun-Hi | 韩文 |
| ko-KR-InJoonNeural | InJoon | 韩文 |

#### 情绪风格参数
| 风格参数 | 效果描述 | 适用场景 |
|---------|---------|---------|
| angry | 愤怒语气 | 情感强烈的对话 |
| chat | 轻松闲聊 | 日常对话交流 |
| cheerful | 开心愉悦 | 欢快场景表达 |
| sad | 悲伤情绪 | 抒情感伤场景 |

更多语音风格和参数设置请参考[微软语音合成标记文档](https://learn.microsoft.com/zh-cn/azure/ai-services/speech-service/speech-synthesis-markup-voice)

## 📝 注意事项

1. 本项目仅供学习和个人使用
2. 不建议用于商业项目
3. API 可能随时失效，请谨慎使用
4. 建议在生产环境使用官方付费 API
5. **重要：请使用与语音对应的语言文本，否则可能会转换失败**
6. 建议文本长度不要太长，以避免请求超时

## ❓ 常见问题

1. **Q: 为什么转换失败了？**  
   A: 常见原因：
   - 使用了与语音不匹配的语言（如用英文语音转换中文文本）
   - API Key 错误或认证头部格式不正确
   - 请求参数格式不正确
   - 文本过长
   - 服务暂时不可用

2. **Q: 支持哪些音频格式？**  
   A: 目前仅支持 MP3 格式

3. **Q: 有请求限制吗？**  
   A: Cloudflare Workers 免费版每天有 100,000 次请求限制

4. **Q: 如何调整语音效果？**  
   A: 可以通过调整 speed 参数（范围 0.5-2.0）来改变语速

5. **Q: 如何使用自定义域名？**  
   A: 有两种方式：
   - 方式一：直接使用 Workers 提供的子域名
   - 方式二：使用自己的域名
     1. 域名需要先托管在 Cloudflare
     2. 在 Worker 设置中添加自定义域名
     3. 等待 DNS 生效（通常几分钟）
     4. 使用自定义域名访问 API

6. **Q: 为什么要使用自定义域名？**  
   A: 使用自定义域名有以下好处：
   - 更专业的品牌形象
   - 更容易记忆的地址
   - 可以随时更换底层服务而保持 API 地址不变
   - 便于管理多个 Workers

7. **Q: 自定义域名需要付费吗？**  
   A: 这取决于你的 Cloudflare 计划：
   - 免费计划：可以使用自定义域名，但有一些限制
   - 付费计划：有更多功能和更高的限制

## 📄 许可证

MIT License

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！



