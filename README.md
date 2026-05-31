# 视角切换器 (Perspective Switcher)

一个Chrome扩展，帮助你从不同视角重新解读热门话题，生成独特的内容观点。

## 功能特点

- 🔄 **反向视角生成** - 从穷人、牛马、学生等视角重新解读话题
- ✨ **相似话题生成** - 生成同类但不雷同的新话题
- 📋 **一键复制** - 快速复制生成的内容
- 🔒 **PRO功能** - 自定义视角（付费功能）

## 安装步骤

### 1. 准备图标文件

由于Chrome扩展需要PNG格式的图标，请准备以下尺寸的图标：
- `icon16.png` (16x16)
- `icon48.png` (48x48)
- `icon128.png` (128x128)

你可以：
1. 使用在线工具将 `icons/icon.svg` 转换为PNG
2. 或者使用任意图片，重命名为对应尺寸

### 2. 加载扩展

1. 打开Chrome浏览器
2. 地址栏输入 `chrome://extensions/`
3. 开启右上角的"开发者模式"
4. 点击"加载已解压的扩展程序"
5. 选择 `perspective-switcher` 文件夹

### 3. 配置API Key

1. 点击扩展图标，然后点击设置按钮 ⚙️
2. 输入你的 OpenAI API Key
3. 点击保存

### 4. 开始使用

1. 打开 X (Twitter) 页面
2. 选中你想要分析的帖子文字
3. 点击扩展图标
4. 选择视角和模式
5. 点击"生成内容"
6. 一键复制，发布你的观点！

## 文件结构

```
perspective-switcher/
├── manifest.json      # 扩展配置文件
├── popup.html         # 弹出窗口界面
├── popup.css          # 弹出窗口样式
├── popup.js           # 弹出窗口逻辑
├── options.html       # 设置页面
├── options.js         # 设置页面逻辑
├── content.js         # 内容脚本
├── content.css        # 内容脚本样式
├── icons/             # 图标文件夹
│   ├── icon.svg       # SVG图标源文件
│   ├── icon16.png     # 16x16图标
│   ├── icon48.png     # 48x48图标
│   └── icon128.png    # 128x128图标
└── README.md          # 说明文档
```

## 获取 OpenAI API Key

1. 访问 https://platform.openai.com/
2. 注册/登录账号
3. 进入 API Keys 页面
4. 创建新的 API Key
5. 复制并保存到扩展设置中

## 收费模式

### 免费版
- 每天3次生成
- 3个预设视角（穷人、牛马、学生）

### PRO版 (¥19.9/月)
- 无限使用次数
- 自定义任意视角
- 更多AI模型选择
- 优先客服支持

## 技术栈

- Chrome Extension Manifest V3
- Vanilla JavaScript
- OpenAI API
- CSS3 渐变动画

## 后续优化建议

1. **添加更多预设视角** - 如：老板视角、投资人视角、老年人视角等
2. **支持更多AI模型** - Claude、DeepSeek等
3. **历史记录功能** - 保存生成过的内容
4. **一键发布** - 直接在X上发布（需要X API）
5. **多语言支持** - 支持英文等其他语言

## 许可证

MIT License
