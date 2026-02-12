# 角色卡API搭建指南
## 概述
这份指南将详细、清晰地教你搭建角色卡API，该API将被AI_Chat软件调用。你需要实现两个核心接口：角色卡索引接口（供用户获取所有角色卡的基础信息）和角色卡详情接口（供用户获取单张角色卡的完整信息）。本指南力求通俗易懂，即使是零基础的开发者也能理解和操作。

## 一、核心接口总览
你需要搭建两个核心API接口，它们的关系如下：
1. **角色卡索引接口**：返回所有角色卡的基础信息列表（预览图、名称、简介、详情链接）
2. **角色卡详情接口**：通过索引接口提供的链接，返回单张角色卡的完整信息

## 二、接口1：角色卡索引接口
### 2.1 接口作用
这个接口是用户获取角色卡列表的入口，被请求后需要返回所有角色卡的基础信息，供AI_Chat软件展示角色卡列表。
搭建好后，只需要把该接口链接提供给用户，用户即可使用你的API。

### 2.2 请求方式
- **请求方法**：GET
- **接口路径示例**：`/api/role-cards/index`（你可以自定义路径，只要保持固定即可）

### 2.3 返回格式要求
返回数据必须是**JSON数组格式**，数组中每个对象对应一张角色卡的基础信息，字段定义如下：

| 字段名 | 类型 | 必选 | 说明 |
|--------|------|------|------|
| previewImage | 字符串 | 是 | 角色卡预览图的完整网络链接（如https开头），确保链接可直接访问 |
| roleCardName | 字符串 | 是 | 角色卡的名称（如“全能助手”），名称需唯一且易识别 |
| roleCardIntroduction | 字符串 | 是 | 角色卡的简短简介（建议100字以内），清晰描述角色核心特点 |
| roleCardLink | 字符串 | 是 | 该角色卡详情接口的完整网络链接，用户通过此链接获取角色卡完整信息 |

### 2.4 完整返回示例
```json
[
  {
    "previewImage": "https://raw.githubusercontent.com/glacierbingchuan-ai/Character_Cards/refs/heads/main/image/test.png",
    "roleCardName": "全能助手",
    "roleCardIntroduction": "这是一个全能的私人助手角色卡，无所不能，总是能完美解决你遇到的所有问题，让你的生活变得轻松愉快。",
    "roleCardLink": "https://raw.githubusercontent.com/glacierbingchuan-ai/Character_Cards/refs/heads/main/card/assistant.json"
  },
  {
    "previewImage": "https://raw.githubusercontent.com/glacierbingchuan-ai/Character_Cards/refs/heads/main/image/caring_brother.png",
    "roleCardName": "贴心哥哥",
    "roleCardIntroduction": "这是一个贴心保护的哥哥角色卡，总是把你放在第一位，为你解决所有困难，在你需要时永远在身边，是你最坚强的后盾。",
    "roleCardLink": "https://raw.githubusercontent.com/glacierbingchuan-ai/Character_Cards/refs/heads/main/card/caring_brother.json"
  }
]
```

### 2.5 注意事项
- 所有链接必须是**可公开访问**的（不能是本地链接如`localhost`、`127.0.0.1`）
- 数组可以包含1张或多张角色卡，数量无上限
- 字段名**必须完全一致**（包括大小写，如`previewImage`不能写成`previewimage`或`PreviewImage`）

## 三、接口2：角色卡详情接口
### 3.1 接口作用
通过角色卡索引接口中的`roleCardLink`链接访问此接口，返回该角色卡的全部详细信息，供AI_Chat软件加载角色卡的完整配置。

### 3.2 请求方式
- **请求方法**：GET
- **接口路径**：即索引接口中`roleCardLink`字段填写的链接（如`/api/role-cards/detail/assistant`）

### 3.3 返回格式要求
返回数据必须是**JSON对象格式**，字段定义如下：

| 字段名 | 类型 | 必选 | 说明 |
|--------|------|------|------|
| name | 字符串 | 是 | 角色卡名称（需与索引接口中的`roleCardName`一致） |
| roleCardImageUrl | 字符串 | 是 | 角色卡预览图链接（需与索引接口中的`previewImage`一致） |
| roleCardIntroduction | 字符串 | 是 | 角色卡简介（需与索引接口中的`roleCardIntroduction`一致） |
| roleCardUsageReportUrl | 字符串 | 否 | 此接口暂未开放，请留空 |
| roleCardTags | 数组（字符串） | 是 | 角色卡的标签列表，用于分类和检索（如["全能助手", "生活助手"]） |
| roleCardPromptContent | 字符串 | 是 | 角色主提示词，包含角色设定、回复格式要求等核心逻辑（需严格按示例格式编写） |
| roleCardEnhancedPromptContent | 字符串 | 是 | 角色强化提示词，用于补充角色设定、对话风格要求等 |
| roleCardAvailableEmojis | 数组（字符串） | 是 | 角色卡可用的表情包链接列表，每个元素为表情包的完整网络链接 |

### 3.4 完整返回示例
```json
{
  "name": "全能助手",
  "roleCardImageUrl": "https://raw.githubusercontent.com/glacierbingchuan-ai/Character_Cards/refs/heads/main/image/assistant.png",
  "roleCardIntroduction": "这是一个全能的私人助手角色卡，无所不能，总是能完美解决你遇到的所有问题，让你的生活变得轻松愉快。",
  "roleCardUsageReportUrl": "",
  "roleCardTags": ["全能助手", "无所不能", "完美解决", "生活助手", "轻松愉快"],
  "roleCardPromptContent": "你可用的表情包：test_1.jpg test_2.jpg 你是我的私人助手，一个无所不能的人。无论是工作上的问题，还是生活中的琐事，你都能完美解决。记得我加班时，你会贴心地为我准备晚餐和咖啡，确保我不会饿肚子。我忘记重要会议时，你会提前提醒我，并帮我准备好所有资料。我家里的电器坏了，你会立即联系维修人员，第二天就能修好。我想旅行时，你会为我规划好详细的行程，预订好机票和酒店，甚至连当地的特色美食都帮我查好。我遇到人际关系问题时，你会给我建议，帮我分析情况。你总是那么高效，不管我提出什么要求，你都能迅速完成，而且做得非常好。有了你，我的生活变得轻松愉快，再也不用为各种琐事烦恼。\n\n输出格式要求：回复体为JSON格式，可以包含[reply(用于决定是否回复本条消息)]、[messages(要回复的消息内容，可以添加多个对象用于消息分条发送，content仅限发文字消息时用，meme仅限发表情消息时用)]、[events(用于设置约定，约定时间到了系统会主动发起聊天)]三个字段。{\"reply\": true/false, \"messages\": [{\"content\": \"纯文字消息内容，仅文字无任何链接/图片混排\", \"delay_ms\": 数字}, {\"meme\": \"表情包文件名.jpg\", \"delay_ms\": 数字}], \"events\": [{\"name\": \"线上约定具体内容（详细无模糊，如2026年2月1日早上7点互相发早安+早安类表情包叫对方起床）\", \"time\": \"yyyy-MM-dd HH:mm:ss\"}]}。请严格按照此格式输出，确保JSON格式正确。",
  "roleCardEnhancedPromptContent": "请回顾当前对话上下文，保持全能助手的无所不能设定，使用第二人称‘你’称呼用户，语气专业而贴心。在对话中要突出对用户需求的快速响应和完美解决，让用户感受到被全方位照顾的爽感。可以加入具体的生活和工作场景细节，让对话更加真实生动，充满便利和愉快的感觉。",
  "roleCardAvailableEmojis": ["https://raw.githubusercontent.com/glacierbingchuan-ai/Character_Cards/refs/heads/main/meme/test_1.jpg","https://raw.githubusercontent.com/glacierbingchuan-ai/Character_Cards/refs/heads/main/meme/test_2.jpg"]
}
```

### 3.5 关键注意事项
1. `roleCardPromptContent`中的输出格式要求**必须严格遵守**，AI_Chat软件会依赖此格式解析回复内容
2. `roleCardAvailableEmojis`中的链接需与`roleCardPromptContent`中提到的表情包文件名对应
3. 所有文本字段中的特殊字符（如双引号、换行符）需按JSON规范转义（示例中已做好转义，可参考）
4. 日期格式需严格遵循`yyyy-MM-dd HH:mm:ss`（如`2026-02-01 07:00:00`）

## 四、API部署与测试
### 4.1 部署要求
- 接口需部署在**公网可访问**的服务器/平台（如阿里云、腾讯云、GitHub Pages、Vercel等）
- 确保接口响应状态码为200（正常），无跨域限制（可添加CORS配置）

### 4.2 测试方法
1. 使用浏览器直接访问索引接口链接，检查返回的JSON格式是否正确
2. 点击索引接口中的`roleCardLink`链接，检查详情接口返回的JSON是否完整
3. 推荐使用Postman、Apifox等工具测试接口，验证请求/返回是否符合要求

## 五、常见问题解答
### Q1：字段名写错了会怎么样？
A1：AI_Chat软件会按固定字段名解析数据，字段名错误会导致软件无法识别对应信息（如预览图不显示、角色名称为空），**必须严格按照文档中的字段名编写**。

### Q2：链接无法访问怎么办？
A2：检查链接是否为完整的https链接、是否有拼写错误、对应的文件是否已上传到服务器/仓库。

### Q3：JSON格式报错怎么办？
A3：使用[JSON在线校验工具](https://www.json.cn/)校验返回的JSON数据，修复格式错误（如缺少逗号、引号不闭合、数组/对象嵌套错误等）。

### 总结
1. 需搭建两个核心接口：角色卡索引接口（返回JSON数组）和角色卡详情接口（返回JSON对象），字段名和格式必须严格匹配文档要求。
2. 所有链接需为公网可访问的完整链接，JSON数据需通过格式校验，避免语法错误。
3. 部署后需测试接口的可访问性和数据正确性，确保AI_Chat软件能正常调用。
