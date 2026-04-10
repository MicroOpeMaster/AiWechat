# 微信AI聊天风格克隆机器人

基于阿里云百炼微调模型，实现微信消息自动监听和智能回复。

## 功能

- 扫描微信聊天记录，训练个性化AI模型
- 自动监听微信私聊/群聊消息
- 使用微调后的模型智能回复
- 支持上下文对话历史

## 安装

```bash
# 克隆项目
git clone https://github.com/MicroOpeMaster/AiWechat.git
cd AiWechat

# 创建虚拟环境
python -m venv .venv
.venv\Scripts\activate  # Windows

# 安装依赖
pip install -r requirements.txt
```

## 配置

1. 复制配置文件
```bash
copy .env.example .env
```

2. 编辑 `.env` 填写配置
```env
# 阿里云百炼
DASHSCOPE_API_KEY=你的API密钥
DASHSCOPE_FINETUNED_MODEL=你的微调模型ID

# 微信机器人配置
BOT_NAME=@你的微信名           # 群聊识别，必须带@
ALIAS_WHITELIST=好友1,好友2    # 私聊联系人白名单（逗号分隔）
ROOM_WHITELIST=群1,群2         # 群白名单（逗号分隔）
AUTO_REPLY_PREFIX=             # 回复前缀，留空则全部回复
LISTEN_INTERVAL=1.0            # 监听间隔（秒）
```

3. 微信客户端版本要求
- 需要 **3.9.11.17** 版本的微信PC客户端
- 下载地址：[wechat-windows-versions](https://github.com/tom-snow/wechat-windows-versions/releases)

## 运行

```bash
# 全自动监听模式（推荐）
python main.py --mode auto

# 热键触发模式
python main.py --mode hotkey
```

## 导出微信聊天记录

使用 [WeFlow](https://github.com/hicccc77/WeFlow) 导出微信聊天记录：

1. 下载并安装 WeFlow
2. 打开微信PC客户端，登录账号
3. 运行 WeFlow，选择要导出的聊天
4. 导出格式选择 JSON
5. 将导出的文件放入 `data/raw/` 目录

## 数据清洗

将导出的聊天记录放入 `data/raw/` 目录后，运行数据处理脚本：

```bash
python scripts/process_data.py
```

处理后的训练数据会保存到 `data/processed/train_data.jsonl`

## 模型微调

1. 登录 [阿里云百炼平台](https://bailian.console.aliyun.com/)
2. 进入「模型微调」模块
3. 上传 `data/processed/train_data.jsonl` 训练数据
4. 选择基座模型（推荐 qwen-turbo 或 qwen-plus）
5. 创建微调任务，等待训练完成
6. 获取微调后的模型ID，填入 `.env` 的 `DASHSCOPE_FINETUNED_MODEL`

## 项目结构

```
AiWechat/
├── main.py                # 主入口
├── src/
│   ├── auto_listener.py   # 全自动监听模块
│   ├── wxauto_bot.py      # 微信机器人核心
│   ├── model_api.py       # 百炼 API
│   ├── config.py          # 全局配置
│   └── utils.py           # 工具模块
├── scripts/
│   ├── process_data.py    # 数据处理
│   └── analyze_data.py    # 数据分析
├── data/
│   ├── raw/               # 原始数据
│   └── processed/         # 训练数据
├── .env.example           # 配置示例
└── requirements.txt       # Python 依赖
```

## 自动回复逻辑

1. **群聊消息**：只有在白名单群内被 @ 时才回复
2. **私聊消息**：只有在白名单内的联系人才回复
3. **前缀匹配**：可设置 `AUTO_REPLY_PREFIX`，只有以此前缀开头的消息才触发

## 注意事项

- 微信自动回复有封号风险，建议使用小号测试
- wxauto 需要 Windows 系统和微信PC客户端
- 微信客户端窗口需要保持可见状态
- 参考: [wxauto](https://github.com/cluic/wxauto)

## License

MIT