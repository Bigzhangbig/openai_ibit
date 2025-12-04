# 百丽宫的Deepseek R1

## 项目简介

你梨居然上新了满血版的r1 671b模型!可喜可贺~
正愁找不到免费可靠的api吗?刚好来白嫖学校的!

## 项目架构图

```
openai_ibit/
├── server.py              # FastAPI 主服务入口，提供 OpenAI 兼容的 API 接口
├── settings.py            # 配置管理模块，负责环境变量读取和模型配置
├── requirements.txt       # Python 依赖包列表
├── Dockerfile             # Docker 容器构建配置文件
├── LICENSE                # 开源许可证文件
├── README.md              # 项目说明文档
│
├── auth/                  # 认证模块目录
│   └── login.py           # 北理工统一身份认证登录封装
│
├── models/                # 模型接口目录
│   ├── ibit.py            # iBit 平台 DeepSeek 模型接口封装
│   └── agent.py           # 智能体广场 DeepSeek-R1 模型接口封装
│
├── tokenizer/             # 分词器目录
│   └── deepseek/          # DeepSeek 模型专用分词器
│       ├── deepseek_tokenizer.py  # 分词器加载和 Token 计数功能
│       ├── tokenizer.json         # 分词器词表配置
│       └── tokenizer_config.json  # 分词器参数配置
│
└── data/                  # 运行时数据目录（自动创建）
    ├── statistics.txt     # 调用统计日志（文本格式）
    └── statistics.json    # 调用统计数据（JSON 格式）
```

## 架构流程图

```
                          ┌─────────────────────────────────────┐
                          │           客户端请求                │
                          │   (OpenAI SDK / NextChat 等)        │
                          └─────────────────┬───────────────────┘
                                            │
                                            ▼
                          ┌─────────────────────────────────────┐
                          │         server.py (FastAPI)         │
                          │   - /v1/models (获取模型列表)       │
                          │   - /v1/chat/completions (对话)     │
                          │   - API Key 验证 (可选)             │
                          └─────────────────┬───────────────────┘
                                            │
                          ┌─────────────────┴───────────────────┐
                          │           settings.py               │
                          │   - 环境变量配置读取                │
                          │   - 模型实例化与注册                │
                          └─────────────────┬───────────────────┘
                                            │
               ┌────────────────────────────┼────────────────────────────┐
               │                            │                            │
               ▼                            ▼                            ▼
┌──────────────────────────┐  ┌──────────────────────────┐  ┌──────────────────────────┐
│    models/ibit.py        │  │    models/agent.py       │  │ tokenizer/deepseek/      │
│  iBit 平台接口封装       │  │  智能体广场接口封装      │  │ deepseek_tokenizer.py    │
│  - 登录认证              │  │  - 对话管理              │  │  - Token 计数            │
│  - 流式/非流式对话       │  │  - 流式/非流式对话       │  │  - 费用计算支持          │
└────────────┬─────────────┘  └────────────┬─────────────┘  └──────────────────────────┘
             │                             │
             ▼                             ▼
┌──────────────────────────┐  ┌──────────────────────────┐
│    auth/login.py         │  │   agent.bit.edu.cn       │
│  统一身份认证登录        │  │   智能体广场 API         │
└────────────┬─────────────┘  └──────────────────────────┘
             │
             ▼
┌──────────────────────────┐
│   ibit.yanhekt.cn        │
│   iBit 平台 API          │
└──────────────────────────┘
```

## 核心模块说明

| 文件路径 | 功能描述 |
|---------|---------|
| `server.py` | FastAPI 主服务，提供 OpenAI 兼容的 Chat Completions API，支持流式和非流式响应 |
| `settings.py` | 配置管理，读取环境变量，初始化模型实例，管理统计文件路径 |
| `auth/login.py` | 封装 bit_login 库，实现北理工统一身份认证登录 |
| `models/ibit.py` | iBit 平台（ibit.yanhekt.cn）的 DeepSeek 模型接口，支持自动重连 |
| `models/agent.py` | 智能体广场（agent.bit.edu.cn）的 DeepSeek-R1 模型接口 |
| `tokenizer/deepseek/deepseek_tokenizer.py` | DeepSeek 模型的分词器，用于计算 Token 数量和费用 |

## 食用方法
```bash
# 不需要验证的情况
docker run -d -p 8000:8000 --name OpeniBIT \
    -e BIT_USERNAME=统一身份验证用户名 \
    -e BIT_PASSWORD=统一身份验证密码 \
    -e TZ=Asia/Shanghai \
    yht0511/open_ibit:latest
# 需要设置api_key
docker run -d -p 8000:8000 --name OpeniBIT \
    -e BIT_USERNAME=统一身份验证用户名 \
    -e BIT_PASSWORD=统一身份验证密码 \
    -e API_KEY=你想设置的api_key \
    -e TZ=Asia/Shanghai \
    yht0511/open_ibit:latest
```

现在,你已经获得了一个免费的ds接口!你现在可以使用openai等库对接该接口,或是简单地使用nextchat: 在设置里填入接口地址和api_key(没有设置则随便输入)即可享用!


## 2025.6.8更新
现在支持智能体广场的模型,设置环境变量`AGENT_APP_KEY`和`AGENT_VISITOR_KEY`即可使用。
默认模型名称:"deepseek-r1",ibit模型名称:"ibit"。