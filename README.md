# HanLink Agent - 4S店AI线索处理系统

基于 FastAPI + LangGraph + ChromaDB 的智能线索处理系统，用于汽车4S店的客户线索管理。

## 功能特性

- **截流**: 接收抖音等渠道推送的客户消息
- **洗单**: 使用AI提取客户意向车型和预算
- **预警**: 监控竞品提及，及时预警
- **资产化**: 将线索信息存入数据库，实现数字化管理

## 技术栈

- **Web框架**: FastAPI
- **AI引擎**: LangGraph + OpenAI
- **向量数据库**: ChromaDB (RAG检索)
- **关系数据库**: SQLite + SQLAlchemy
- **前端**: Streamlit (可选)

## 项目结构

```
hanlink_agent/
├── app/
│   ├── api/
│   │   └── webhook.py          # Webhook API路由
│   ├── core/
│   │   ├── config.py           # 配置管理
│   │   └── database.py         # 数据库连接
│   ├── models/
│   │   ├── database.py         # 数据库模型
│   │   └── schemas.py          # Pydantic模型和TypedDict
│   ├── services/
│   │   ├── chroma_service.py   # ChromaDB服务
│   │   └── lead_service.py     # 线索业务逻辑
│   ├── agents/
│   │   ├── graph.py            # LangGraph决策流图
│   │   └── nodes.py            # Agent节点定义
│   └── main.py                 # FastAPI应用入口
├── data/                       # 数据目录
├── .env                        # 环境变量
├── requirements.txt            # 依赖列表
└── run.py                      # 启动脚本
```

## 快速开始

### 1. 配置环境变量

编辑 `.env` 文件，填入你的 OpenAI API Key:

```bash
OPENAI_API_KEY=sk-your-api-key
```

### 2. 启动服务

```bash
python run.py
```

服务将在 http://localhost:8000 启动

### 3. 测试接口

```bash
# 健康检查
curl http://localhost:8000/api/v1/webhook/health

# 发送测试消息
curl -X POST http://localhost:8000/api/v1/webhook/douyin \
  -H "Content-Type: application/json" \
  -d '{
    "user_id": "test_user_001",
    "nickname": "张先生",
    "message": "我想了解一下Model 3，预算大概25万左右"
  }'
```

## API 文档

启动服务后访问:
- Swagger UI: http://localhost:8000/docs
- ReDoc: http://localhost:8000/redoc

## LangGraph 决策流程

```
用户消息
    ↓
[extractor_node] 提取意向车型和预算
    ↓
[monitor_node] 检测竞品提及
    ↓
[rag_node] 检索相关销售话术
    ↓
生成回复
```

## 数据库

线索数据存储在 `hanlink.db`，包含以下字段:

- `douyin_uid`: 抖音用户ID
- `nickname`: 用户昵称
- `target_model`: 意向车型
- `budget`: 预算(万元)
- `risk_level`: 风险等级
- `competitor_mention`: 竞品提及
- `last_conversation`: 最近对话
