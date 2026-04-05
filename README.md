# Multi-Agent E-Commerce Recommendation System

## 多Agent电商推荐与营销系统 — 从零到面试全攻略

> **面向小白**的企业级多Agent项目，覆盖 Python / Java / Go 三语言实现，配套八股文30题、简历模板、STAR法面试话术。

---

### 系统架构

```
                    ┌──────────────────┐
                    │   Supervisor      │
                    │   协调Agent       │
                    └────────┬─────────┘
            ┌────────┬───────┴───────┬────────┐
            ▼        ▼               ▼        ▼
     ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐
     │ 用户画像  │ │ 商品推荐  │ │ 营销文案  │ │ 库存决策  │
     │ Agent    │ │ Agent    │ │ Agent    │ │ Agent    │
     │ ──────── │ │ ──────── │ │ ──────── │ │ ──────── │
     │ Redis    │ │ Milvus   │ │ LLM     │ │ MySQL    │
     │ 实时特征  │ │ 向量检索  │ │ 个性化   │ │ 库存校验  │
     └─────┬────┘ └────┬─────┘ └────┬─────┘ └────┬─────┘
           └──────┬─────┴──────┬─────┘            │
                  ▼            ▼                   │
            ┌─────────────────────────────────────┐
            │         结果聚合 + A/B测试引擎        │
            └──────────────────┬──────────────────┘
                               ▼
                         个性化响应
```

### 技术亮点

- **并行+聚合编排**: Supervisor模式，4 Agent两阶段并行，端到端延迟 P99 < 2s
- **实时特征工程**: Redis Sorted Set + 滑动窗口（1h/24h/7d）+ RFM模型
- **A/B测试引擎**: 流量分桶 + Thompson Sampling动态调优，支持三层实验
- **个性化文案**: 5套Prompt模板 x 用户分群，广告法合规校验
- **三语言实现**: Python (LangGraph) / Java (Spring AI) / Go (goroutine)

---

## 快速开始

### Python版（推荐入门）

```bash
cd python

# 1. 安装依赖
pip install -r requirements.txt

# 2. 配置环境变量
cp .env.example .env
# 编辑 .env 填入 LLM API Key

# 3. 启动服务
python main.py

# 4. 测试推荐接口
curl -X POST http://localhost:8000/api/v1/recommend \
  -H "Content-Type: application/json" \
  -d '{"user_id": "user_001", "scene": "homepage", "num_items": 5}'
```

### Java版

```bash
cd java

# 1. 配置 application.yml 中的 API Key
# 2. 构建运行
mvn spring-boot:run

# 3. 测试
curl -X POST http://localhost:8080/api/v1/recommend \
  -H "Content-Type: application/json" \
  -d '{"userId": "user_001", "numItems": 5}'
```

### Go版

```bash
cd go

# 1. 设置环境变量
export ECOM_LLM_API_KEY=your_api_key_here
export ECOM_LLM_BASE_URL=https://api.minimax.chat/v1

# 2. 运行
go run cmd/main.go

# 3. 测试
curl -X POST http://localhost:8080/api/v1/recommend \
  -H "Content-Type: application/json" \
  -d '{"user_id": "user_001", "num_items": 5}'
```

### Docker一键部署

```bash
docker-compose up -d
```

---

## 项目结构

```
multi-agent-ecommerce/
├── README.md                      # 本文件
├── docker-compose.yml             # 一键启动
│
├── docs/                          # 面试全套文档
│   ├── interview-guide.md         # 面试指南(八股文30题+STAR法)
│   ├── resume-template.md         # 简历模板(应届+社招)
│   ├── architecture.md            # 架构设计文档
│   └── code-walkthrough.md        # 代码讲解(面向小白)
│
├── python/                        # Python实现 (LangGraph + FastAPI)
│   ├── main.py                    # FastAPI入口
│   ├── agents/                    # 4个Agent实现
│   │   ├── base_agent.py          # 基类(重试/超时/降级)
│   │   ├── user_profile_agent.py  # 用户画像Agent
│   │   ├── product_rec_agent.py   # 商品推荐Agent
│   │   ├── marketing_copy_agent.py# 营销文案Agent
│   │   └── inventory_agent.py     # 库存决策Agent
│   ├── orchestrator/              # 编排层
│   │   ├── supervisor.py          # Supervisor并行编排
│   │   └── graph.py               # LangGraph状态图
│   ├── services/                  # 业务服务
│   │   ├── ab_test.py             # A/B测试引擎
│   │   ├── feature_store.py       # Redis特征存储
│   │   └── metrics.py             # 监控指标
│   ├── models/schemas.py          # Pydantic数据模型
│   ├── config/settings.py         # 配置管理
│   └── tests/                     # 测试
│
├── java/                          # Java实现 (Spring AI + Spring Boot 3)
│   ├── pom.xml
│   └── src/main/java/com/ecommerce/
│       ├── agent/                 # 4个Agent实现
│       ├── orchestrator/          # CompletableFuture并行编排
│       ├── service/               # A/B测试服务
│       └── config/                # 配置+Controller
│
└── go/                            # Go实现 (goroutine并行)
    ├── go.mod
    ├── cmd/main.go                # 入口
    ├── agent/                     # 4个Agent实现
    ├── orchestrator/              # goroutine+WaitGroup并行编排
    ├── handler/                   # Gin HTTP Handler
    ├── service/                   # A/B测试服务
    └── model/                     # 数据结构
```

---

## API 接口

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/api/v1/recommend` | 获取个性化推荐 |
| POST | `/api/v1/recommend/graph` | LangGraph pipeline推荐 (Python only) |
| GET | `/api/v1/experiments` | 查看A/B实验状态 |
| GET | `/api/v1/metrics` | 查看系统监控指标 (Python only) |
| GET | `/health` | 健康检查 |

### 请求示例

```json
{
  "user_id": "user_001",
  "scene": "homepage",
  "num_items": 5,
  "context": {
    "recent_views": ["手机", "耳机"],
    "avg_order_amount": 500
  }
}
```

### 响应示例

```json
{
  "request_id": "uuid",
  "user_id": "user_001",
  "products": [
    {"product_id": "P001", "name": "iPhone 16 Pro", "category": "手机", "price": 7999}
  ],
  "marketing_copies": [
    {"product_id": "P001", "copy": "科技旗舰,影像新标杆,此刻开启你的Pro体验"}
  ],
  "experiment_group": "treatment_llm",
  "total_latency_ms": 1523.4
}
```

---

## 面试资料

本项目配套完整的面试准备材料:

| 文档 | 内容 | 适用场景 |
|------|------|---------|
| [面试指南](docs/interview-guide.md) | 八股文30题 + STAR法话术 + 追问应对 | 面试前通读 |
| [简历模板](docs/resume-template.md) | 应届/社招简历模板 + 岗位适配建议 | 写简历时参考 |
| [架构设计](docs/architecture.md) | 系统架构 + 数据流 + 稳定性设计 | 讲解架构时参考 |
| [代码讲解](docs/code-walkthrough.md) | 逐文件讲解 + 面试话术 | 被问代码时参考 |

---

## 参考项目

本项目架构设计参考了以下企业级项目:

- [NVIDIA Retail Agentic Commerce](https://github.com/NVIDIA-AI-Blueprints/Retail-Agentic-Commerce) — NVIDIA企业级电商Agent
- [Spring AI Alibaba Multi-Agent Demo](https://github.com/spring-ai-alibaba/spring-ai-alibaba-multi-agent-demo) — 阿里巴巴Java多Agent示例
- [京东商家智能助手](https://juejin.cn/post/7470344960563871784) — 京东Multi-Agent技术博客
- [DualAgent-Rec](https://github.com/GuilinDev/Dual-Agent-Recommendation) — 双Agent推荐系统

---

## License

MIT License
