<!-- 中文说明开始 -->

# 🇨🇳 TradingAgents 中文详细说明

<!-- 徽章区开始 -->
<p align="center">
  <a href="https://github.com/aiwozhonghua81/TradingAgents"><img alt="GitHub" src="https://img.shields.io/badge/GitHub-aiwozhonghua81-181717?style=flat-square&logo=github&logoColor=white"></a>
  <img alt="最近提交" src="https://img.shields.io/github/last-commit/aiwozhonghua81/TradingAgents?style=flat-square&label=%E6%9C%80%E8%BF%91%E6%8F%90%E4%BA%A4">
  <img alt="主语言" src="https://img.shields.io/github/languages/top/aiwozhonghua81/TradingAgents?style=flat-square&label=%E4%B8%BB%E8%AF%AD%E8%A8%80">
  <img alt="星标" src="https://img.shields.io/github/stars/aiwozhonghua81/TradingAgents?style=flat-square&label=%E6%98%9F%E6%A0%87">
  <img alt="仓库体积" src="https://img.shields.io/github/repo-size/aiwozhonghua81/TradingAgents?style=flat-square&label=%E4%BB%93%E5%BA%93%E4%BD%93%E7%A7%AF">
  <img alt="许可证" src="https://img.shields.io/github/license/aiwozhonghua81/TradingAgents?style=flat-square&label=%E8%AE%B8%E5%8F%AF%E8%AF%81">
</p>
<p align="center">
  <img alt="中文说明" src="https://img.shields.io/badge/%E4%B8%AD%E6%96%87%E8%AF%B4%E6%98%8E-%E5%B7%B2%E5%AE%8C%E6%95%B4%E8%A6%86%E7%9B%96-brightgreen?style=flat-square">
  <img alt="给新手看" src="https://img.shields.io/badge/%E7%BB%99%E6%96%B0%E6%89%8B%E7%9C%8B-%E4%B8%8D%E7%94%A8%E6%87%82%E4%BB%A3%E7%A0%81-blue?style=flat-square">
  <img alt="配图" src="https://img.shields.io/badge/%E9%85%8D%E5%9B%BE-%E5%AE%9E%E6%8B%8D%E6%88%AA%E5%9B%BE%E4%B8%8E%E5%A4%B4%E5%9B%BE-ff69b4?style=flat-square">
</p>
<p align="center">
  <img src="assets/schema.png" alt="TradingAgents 头图" width="880">
</p>
<!-- 徽章区结束 -->

> 这一节是为中文读者写的完整说明，放在最前面。
> 往下滚可以看到**上游项目的原始 README（英文原文）**，内容一个字都没有删改。

---

## 🧐 一、这是什么

**一句话：把「一群金融分析师围坐在一张桌子上讨论一只股票该不该买」这件事，用大语言模型（LLM）自动化实现的研究框架。**

想象一个真实的投研团队：

```text
        ┌──────────────── 分析团队（各自看各的数据）────────────────┐
        │  基本面分析师  │  情绪分析师  │  新闻分析师  │  技术分析师  │
        └───────────────────────┬──────────────────────────────────┘
                                ▼
        ┌────────── 研究团队（看多 vs 看空辩论）──────────┐
        │        多头研究员  ⇄  空头研究员              │
        └───────────────────────┬───────────────────────┘
                                ▼
                    ┌──────── 交易员 Trader ────────┐
                    │  综合所有报告，决定买/卖/持    │
                    └───────────────────┬───────────┘
                                        ▼
        ┌────────── 风险管理团队（三种风格辩论）──────────┐
        │   激进派  │   保守派  │   中立派                │
        └───────────────────────┬───────────────────────┘
                                ▼
                    ┌──────── 投资组合经理 ────────┐
                    │  最终批准 / 否决，提交给模拟交易所 │
                    └───────────────────────────────┘
```

TradingAgents 就是把这套流程**用代码实现出来**，每个角色背后是一个 LLM，每个角色有自己的提示词（prompt）、自己的工具（能查什么数据）、自己的报告。

| 概念 | 大白话解释 |
| --- | --- |
| **多智能体（Multi-Agent）** | 让多个各有分工的 AI 角色互相协作、互相质疑，而不是一个 AI 一个人干完所有事。 |
| **LangGraph** | 一个把「AI 角色 + 流转规则」画成有向图并执行的 Python 库。图的节点是角色，边是「做完这步去哪」。 |
| **辩论（Debate）** | 多头和空头轮流发表观点反驳对方，研究经理最后裁决。**这是这个框架的核心机制**——它逼着模型两面都想过一遍。 |
| **回测 / 模拟交易所** | 程序内部有个模拟盘，按分析结果记录「你说你会买，那我们就假设你买了」。 |
| **Point-in-time（时点正确）** | 分析 2024 年 5 月 10 日时，**只能看到那天之前的数据**。这条极其重要，下文「常见问题」里会详细讲。 |
| **决策日志（decision log）** | 每次分析的决策都会记到本地文件里，下次分析同一只股票时，之前的教训会被喂给模型。 |

**它不是什么？**

- 不是自动下单机器人。它只输出「买 / 卖 / 持有」的判断和研究报告，交易执行发生在**模拟**环境里。
- 不是荐股软件。**上游 README 明确声明：不构成任何投资建议。**
- 不是保证赚钱的工具。表现受模型、参数、数据质量、时间段等一堆因素影响。

---

## 👥 二、适合谁用 / 能解决什么问题

### 典型使用场景

| 场景 | 说明 |
| --- | --- |
| **研究「多智能体做决策」这个方法本身** | 这是项目的官方定位。你换不同模型、不同辩论轮数、不同数据集，观察结论怎么变——把它当研究脚手架。 |
| **快速拿到一份多角度的分析报告** | 输入一个股票代码和日期，它会自动跑完基本面 / 情绪 / 新闻 / 技术四条线，出几份分节报告和一份总结。 |
| **想验证「多空辩论」比「单一 AI 观点」更稳」** | 用 `max_debate_rounds` 控制辩论轮数，对比 1 轮和 3 轮的决策差异。 |
| **把自己的决策过程记录下来复用** | 决策日志 + 反思（reflection）机制让它能记住「上次这么判断对/错了」。 |
| **跑不通就中断、之后接着跑** | `--checkpoint` 开启后，每个节点执行完都会存状态。崩溃了不用从头再来。 |
| **在本机 / 私有服务器上跑，用自己的大模型** | 支持 Ollama、vLLM、LM Studio 等本地模型，数据不出本机。 |

### 它**不**适合谁

- **想靠它稳定赚钱的人**：它没有实盘交易能力，也没有任何收益保证。
- **完全不懂金融的小白**：输出里全是 MACD、RSI、Beta、超额收益（Alpha）这些术语。虽然报告可以设为中文输出，但概念本身还是要懂。
- **想要秒级出结果的人**：一次完整分析要调用几十次 LLM，用的是 gpt-5.6 级别的模型，**跑一次可能十几分钟到几十分钟**，token 消耗也不小。

---

## ⚙️ 三、核心功能

### 3.1 分析团队

上游 README 列出的是 4 个角色，代码里实际有 5 个分析师文件（多一个独立的社交媒体分析师）：

| 角色 | 干什么 | 看什么数据 |
| --- | --- | --- |
| **基本面分析师** Fundamentals Analyst | 看公司财报，评估内在价值，找出财务红旗 | 利润表、资产负债表、现金流量表、内部人交易 |
| **市场/技术分析师** Market Analyst | 用技术指标找买卖点和价格形态 | K 线、MACD、RSI 等指标（`stockstats` 库计算） |
| **情绪分析师** Sentiment Analyst | 汇总社交媒体和散户的短期情绪 | StockTwits、Reddit |
| **新闻分析师** News Analyst | 解读全球新闻和宏观事件对市场的影响 | 个股新闻、全球宏观新闻、FRED 宏观数据 |
| **社交媒体分析师** Social Media Analyst | 专门盯社交平台动态（代码里独立成一个 agent） | Reddit / StockTwits 等 |

### 3.2 研究团队（多空辩论）

| 角色 | 干什么 |
| --- | --- |
| **多头研究员** Bull Researcher | 从上涨逻辑出发，论证为什么该买 |
| **空头研究员** Bear Researcher | 从下跌逻辑出发，论证为什么该卖/该谨慎 |
| **研究经理** Research Manager | 听完辩论后做裁决，输出「继续辩论」或「交给交易员」 |

### 3.3 交易员

把四份分析报告 + 研究经理的裁决综合起来，输出具体的交易决策（买/卖/持有，方向、理由）。

### 3.4 风险管理与投资组合

| 角色 | 干什么 |
| --- | --- |
| **激进分析师** Aggressive Debator | 站在激进立场，论证收益潜力 |
| **保守分析师** Conservative Debator | 站在保守立场，论证风险 |
| **中立分析师** Neutral Debator | 提出平衡视角 |
| **投资组合经理** Portfolio Manager | 最终批准或否决提案。批准后提交给**模拟交易所**执行 |

### 3.5 工程能力

| # | 功能 | 大白话说明 |
| --- | --- | --- |
| 1 | **多厂商 LLM 支持** | OpenAI、Google、Anthropic、xAI、DeepSeek、Qwen（阿里 DashScope 国际版 + 中国版）、GLM（智谱）、Kimi、MiniMax（国际 + 中国）、OpenRouter、Mistral、Groq、NVIDIA、Azure OpenAI、AWS Bedrock、Ollama（本地模型），以及**任何 OpenAI 兼容的自建端点**（vLLM、LM Studio、llama.cpp）。 |
| 2 | **两个模型档位** | `deep_think_llm`（复杂推理用）和 `quick_think_llm`（快速任务用）。可以一个用贵模型一个用便宜模型省钱。 |
| 3 | **决策日志 + 反思** | 每次运行结束会追加决策到 `~/.tradingagents/memory/trading_memory.md`。下次分析同一只股票时，自动算出实际收益（原始收益 + 相对 SPY 的超额收益），生成一段反思，把「最近的同股票决策 + 跨股票的教训」注入投资组合经理的提示词。 |
| 4 | **检查点续跑** | `--checkpoint` 开启后，每个节点执行完 LangGraph 就存一次状态。崩溃了从最后一步继续，而不是从头再来。成功跑完后自动清理。 |
| 5 | **多市场支持** | 美股、港股（`0700.HK`）、日股（`7203.T`）、英股（`AZN.L`）、印度、加拿大、澳股、**A 股**（`600519.SS` 贵州茅台 / `600519.SZ`）、加密货币（`BTC-USD`、`ETH-USD`）。 |
| 6 | **多数据源可配置** | 股票行情和指标支持 yfinance / Alpha Vantage；宏观数据用 FRED；预测市场用 Polymarket。**可以精确指定用哪一家**，不会被悄悄路由到别的厂商。 |
| 7 | **时点正确性（look-ahead 防护）** | 分析历史日期时，宏观数据、社交情绪、新闻、决策记忆都会被裁剪到那天之前，不会「偷看未来」。这是 v0.4.0 重点修的东西。 |
| 8 | **输出语言可配** | `output_language` 可以设成中文，报告和最终决策用中文输出（**但智能体之间的内部辩论仍用英文**，作者说这是为了推理质量）。 |
| 9 | **结构化输出** | 研究经理、交易员、投资组合经理都走结构化输出（schema 校验），保证能解析出「买入 / 卖出 / 持有」这类明确信号。 |
| 10 | **CLI + Python API 双入口** | 命令行交互式使用，或者在自己的代码里 `import` 调用。 |
| 11 | **Pydantic 校验 + 60+ 测试** | `tests/` 目录有 62 个测试文件（63 个 `.py`，含 `__init__.py`），覆盖各种边界场景。CI 在 Python 3.10 / 3.11 / 3.12 / 3.13 四个版本上跑测试。 |
| 12 | **Docker 一键跑** | `docker compose run --rm tradingagents` 就行，也能配 Ollama profile 用本地模型。 |

---

## 🚀 四、怎么快速上手

### 4.1 前置条件

- **Python 3.10+**（`pyproject.toml` 里写的是 `requires-python = ">=3.10"`，官方 README 推荐 3.12）
- **至少一个大模型的 API Key**（或者本机装了 Ollama）
- **网络能访问数据源**（yfinance / Reddit / StockTwits 等）

### 4.2 方式一：Python 环境安装（推荐）

**第 1 步：克隆并进入目录**

```bash
git clone https://github.com/TauricResearch/TradingAgents.git
cd TradingAgents
```

> 如果你想用本仓库（fork）的版本，把上面的地址换成 `https://github.com/aiwozhonghua81/TradingAgents.git` 即可。

**第 2 步：建虚拟环境并激活**

```bash
conda create -n tradingagents python=3.12
conda activate tradingagents
```

> 不用 conda 也行：`python -m venv .venv` 然后 `.venv\Scripts\activate`（Windows）或 `source .venv/bin/activate`（macOS / Linux）。

**第 3 步：安装**

```bash
pip install .
```

**第 4 步：配置 API Key**

```bash
cp .env.example .env
```

然后编辑 `.env`，把你要用的那家厂商的 Key 填进去。比如用 OpenAI：

```
OPENAI_API_KEY=sk-...
```

或者直接用环境变量，不建 `.env` 文件：

```bash
export OPENAI_API_KEY=...
export GOOGLE_API_KEY=...
export ANTHROPIC_API_KEY=...
export XAI_API_KEY=...
export DEEPSEEK_API_KEY=...
export DASHSCOPE_API_KEY=...        # Qwen 国际版
export DASHSCOPE_CN_API_KEY=...     # Qwen 中国版
export ZHIPU_API_KEY=...            # GLM 国际版
export ZHIPU_CN_API_KEY=...         # GLM 中国版
export MINIMAX_API_KEY=...          # MiniMax 全球版
export MINIMAX_CN_API_KEY=...       # MiniMax 中国版
export OPENROUTER_API_KEY=...
export ALPHA_VANTAGE_API_KEY=...    # 数据源，不是模型
```

**第 5 步：跑起来**

```bash
tradingagents          # 安装后的命令
# 或者
python -m cli.main     # 直接从源码跑
```

会弹出一个交互界面，让你选股票代码、分析日期、LLM 厂商、研究深度等等。

### 4.3 方式二：Docker

```bash
# 1. 先把 Key 填进 .env
cp .env.example .env

# 2. 跑
docker compose run --rm tradingagents
```

**要用本地模型（Ollama）**：

```bash
docker compose --profile ollama run --rm tradingagents-ollama
```

> 这条命令会同时起 Ollama 容器，并把 `TRADINGAGENTS_LLM_PROVIDER` 设成 `ollama`、`OLLAMA_BASE_URL` 指到容器内的 `http://ollama:11434/v1`。**你还需要自己先 `ollama pull <模型名>` 把模型拉下来。**

**Dockerfile 里的安全设计**（值得注意）：

- 用非 root 用户 `appuser` 运行，不是 root；
- 数据目录 `/home/appuser/.tradingagents` 挂成 named volume，容器删了数据还在；
- `PYTHONDONTWRITEBYTECODE=1` + `PYTHONUNBUFFERED=1`，镜像更小且日志实时输出。

### 4.4 方式三：写进自己的 Python 代码

```python
from tradingagents.graph.trading_graph import TradingAgentsGraph
from tradingagents.default_config import DEFAULT_CONFIG

ta = TradingAgentsGraph(debug=True, config=DEFAULT_CONFIG.copy())

# 第二个返回值是最终决策
_, decision = ta.propagate("NVDA", "2026-01-15")
print(decision)
```

**想自定义参数就改 config**：

```python
config = DEFAULT_CONFIG.copy()
config["llm_provider"] = "openai"          # openai / google / anthropic / deepseek / groq / ollama ...
config["deep_think_llm"] = "gpt-5.6"        # 复杂推理用的模型
config["quick_think_llm"] = "gpt-5.6-luna"  # 快速任务用的模型
config["max_debate_rounds"] = 2             # 多空辩论轮数
config["output_language"] = "Chinese"       # 输出语言
config["max_risk_discuss_rounds"] = 2        # 风险辩论轮数
config["temperature"] = 0.0                 # 采样温度（越低越稳定）

ta = TradingAgentsGraph(debug=True, config=config)
_, decision = ta.propagate("NVDA", "2026-01-15")
print(decision)
```

> `DEFAULT_CONFIG` 已经自动应用了 `TRADINGAGENTS_*` 环境变量覆盖，**所以纯用 `.env` 就能切换模型和端点，不用改代码**。
>
> 环境变量怎么映射到配置项，看 `tradingagents/default_config.py` 里的 `_ENV_OVERRIDES` 字典，一目了然。值的类型按默认值自动转换（`"true"` → `True`，`"3"` → `3`），**拼错会直接报错而不是静默用默认值**。

### 4.5 检查点续跑

```bash
tradingagents analyze --checkpoint           # 这次开启断点续跑
tradingagents analyze --clear-checkpoints    # 跑之前先把旧检查点全清掉
```

续跑时日志会显示 `Resuming from step N for <TICKER> on <date>`；全新跑则显示 `Starting fresh`。

检查点存在 `~/.tradingagents/cache/checkpoints/<TICKER>.db`（每个股票一个 SQLite 文件），基础目录可以用 `TRADINGAGENTS_CACHE_DIR` 改。

### 4.6 命令行参数

| 命令 / 参数 | 作用 |
| --- | --- |
| `tradingagents` | 不带子命令 → 进入交互式选择界面 |
| `tradingagents analyze` | 直接跑分析 |
| `--checkpoint` / `--no-checkpoint` | 开启 / 关闭断点续跑。不写则听 `TRADINGAGENTS_CHECKPOINT_ENABLED` 的 |
| `--clear-checkpoints` | 跑之前删掉所有已保存的检查点 |

### 4.7 环境变量速查

全部在 `.env.example` 里列好了。常用的：

| 变量 | 作用 |
| --- | --- |
| `TRADINGAGENTS_LLM_PROVIDER` | 厂商名。设了之后 CLI 会跳过「选厂商」那一步 |
| `TRADINGAGENTS_DEEP_THINK_LLM` | 复杂推理模型 |
| `TRADINGAGENTS_QUICK_THINK_LLM` | 快速任务模型 |
| `TRADINGAGENTS_LLM_BACKEND_URL` | 自建端点地址（vLLM / LM Studio / llama.cpp 等） |
| `TRADINGAGENTS_OUTPUT_LANGUAGE` | 输出语言，设了 `Chinese` 报告就是中文 |
| `TRADINGAGENTS_MAX_DEBATE_ROUNDS` | 多空辩论轮数 |
| `TRADINGAGENTS_MAX_RISK_ROUNDS` | 风险辩论轮数 |
| `TRADINGAGENTS_CHECKPOINT_ENABLED` | 是否默认开启检查点 |
| `TRADINGAGENTS_TEMPERATURE` | 采样温度，越低越稳定 |
| `TRADINGAGENTS_LLM_MAX_RETRIES` | LLM SDK 重试次数。**被 429 限流时调大它能扛过去** |
| `TRADINGAGENTS_MAX_TOKENS` | 输出 token 上限。防止某个模型无限输出把网关卡死 |
| `TRADINGAGENTS_OPENAI_REASONING_EFFORT` | OpenAI 推理强度（`low` / `medium` / `high`） |
| `TRADINGAGENTS_GOOGLE_THINKING_LEVEL` | Gemini 思考级别 |
| `TRADINGAGENTS_ANTHROPIC_EFFORT` | Claude 推理强度 |
| `TRADINGAGENTS_MEMORY_LOG_PATH` | 决策日志路径 |
| `TRADINGAGENTS_CACHE_DIR` | 检查点缓存目录 |
| `TRADINGAGENTS_BENCHMARK_TICKER` | 强制指定 Alpha 基准（默认按交易所后缀自动选） |
| `OLLAMA_BASE_URL` | 远程 Ollama 地址，不设就用本机 `http://localhost:11434/v1` |
| `FRED_API_KEY` | FRED 宏观数据 Key（免费申请） |

### 4.8 跑测试

```bash
pip install ".[dev]"

pytest                        # 全部测试
ruff check .                  # 代码检查
ruff format --check .         # 格式检查
```

`tests/` 里有 60 多个测试文件，覆盖了这些重点场景：
| 测试文件 | 测什么 |
| --- | --- |
| `test_fundamentals_lookahead.py`、`test_news_lookahead.py`、`test_social_lookahead.py` | **时点正确性**（会不会偷看未来数据） |
| `test_memory_pointintime.py` | 决策记忆的时点守卫 |
| `test_checkpoint_lifecycle.py`、`test_checkpoint_resume.py` | 检查点的保存 / 清理 / 续跑 |
| `test_structured_agents.py`、`test_structured_agent_prompts.py` | 结构化输出与提示词 |
| `test_vendor_routing.py`、`test_vendor_errors.py` | 数据源路由与错误处理 |
| `test_provider_registry.py`、`test_model_validation.py` | 厂商注册表与模型校验 |
| `test_bedrock_provider.py`、`test_ollama_base_url.py`、`test_openai_compatible_provider.py` | 各类厂商接入 |
| `test_ticker_symbol_handling.py`、`test_symbol_normalization_paths.py` | 股票代码解析（多市场） |
| `test_cli_config_precedence.py`、`test_cli_env_skip.py` | CLI 配置优先级、环境变量跳过交互 |
| `test_debate_opening.py`、`test_risk_router_path_map.py` | 辩论开场与路由表完整性 |

---

## 🩺 五、常见问题 / 注意事项

### 5.1 最重要的一条：这不是投资建议

上游 README 的原话是：**TradingAgents 是为研究目的设计的。** 表现受模型选择、温度、时间段、数据质量等一堆因素影响。**不构成投资建议。**

请把它当成「研究多智能体决策的研究脚手架」，而不是「明天该买哪只股票的软件」。

### 5.2 「时点正确」（look-ahead）——最该关心的问题

**问题**：分析 2024 年 5 月 10 日的 AAPL，模型如果能「看到」2026 年的新闻和财报，那这个回测结果就是废的——因为它作弊了。

**TradingAgents 怎么防**：

| 数据源 | 防护措施 |
| --- | --- |
| FRED 宏观数据 | 请求和元数据都锁到「分析日期」对应的数据版本（vintage），不会用后来修订过的值 |
| StockTwits / Reddit | 按 UTC 半开区间规则裁剪到分析窗口内，新闻也用同一套规则 |
| 决策日志 | 每条已解决的记录会记下「结果在什么时候才揭晓」，历史分析只看到交易日期之前已解决的教训 |
| 反思时机 | 持有窗口没走完之前不算结算，避免「重跑一次就提前定输赢」 |
| 最新 OHLCV K 线 | 日期按元素逐个归一化（夏令时和非美市场都安全）；缺最新收盘价时**直接报错而不是退回前一根** |

> **为什么这一段值得单独讲？**
> 因为这是量化回测里最常见的自欺欺人。很多回测框架默认「历史数据随便用」，结果跑出来的收益曲线漂亮得不像话，实际上一上线就亏钱。v0.4.0 把这一层系统性修了一遍，还配了专门的测试。

### 5.3 跑不动 / 报错

| 现象 | 原因和解决办法 |
| --- | --- |
| **分析卡住不动** | 某个模型在无限输出。设 `TRADINGAGENTS_MAX_TOKENS=8192` 限制一下。 |
| **429 限流报错中断** | 设 `TRADINGAGENTS_LLM_MAX_RETRIES=6`（默认各 SDK 一般只有 2 次）。 |
| **数据源报「不可用」** | 免费接口偶尔抽风。可以换数据源：`data_vendors` 里把 `yfinance` 改成 `alpha_vantage`。 |
| **Reddit / StockTwits 拉不到数据** | 这两个站对数据中心 IP 经常限流甚至封禁。`test_reddit_fallback.py` 和 `test_stocktwits_resilience.py` 就是专门测这个降级路径的。 |
| **A 股代码不认** | 用带交易所后缀的完整代码：上交所 `600519.SS`，深交所 `000001.SZ`。 |
| **Windows 控制台报错** | 交互式 CLI 需要真正的控制台缓冲区。用 **Windows Terminal / PowerShell / cmd.exe** 跑，不要在管道或嵌入式终端里跑。 |
| **内存不够** | Docker 里记得挂 volume（`docker-compose.yml` 已经配好）。 |

### 5.4 结果不稳定

**同一个股票、同一个日期，跑两次结果不一样——这是正常的，不是 bug。**

原因有三个，得分开看：

| 来源 | 说明 | 能不能治 |
| --- | --- | --- |
| **模型采样本身随机** | 即使固定温度，各家厂商也不保证两次输出字节一致。**推理模型（默认的 GPT-5.x 系列）波动最大**，因为它们的内部推理过程本身就是采样出来的。 | 部分能。调低 `temperature`。但当前精选的模型大多是推理优先的，基本忽略温度。要更稳定就**显式选一个非推理模型**（CLI 的「Custom model ID」选项）。 |
| **实时数据在动** | 新闻、StockTwits、Reddit 的内容随时间变。**即使固定历史交易日期，社交和新闻源反映的仍是「现在」。** | 治不了。只有价格和技术指标窗口能靠固定分析日期锁住。 |
| **公司身份解析** | 这个已经修好了：股票代码在跑任何 agent 之前就被确定性地解析成公司身份，市场分析师的价格和指标说法也会锚定到经过验证的数据快照。 | 已解决。 |

**结论**：把 TradingAgents 当成「研究脚手架」，别当「可复现的策略」。回测结果不保证能对上任何已发布的数字。

### 5.5 用量与成本

| 事项 | 说明 |
| --- | --- |
| **一次分析很贵** | 内部有 4 个分析师 + 2 个研究员 + 交易员 + 3 个风险辩手 + 投资组合经理，每个都要调 LLM。**辩论轮数是乘法关系**：`max_debate_rounds=3` 会让辩论部分的调用量翻好几倍。 |
| **省钱技巧** | 把 `quick_think_llm` 设成便宜模型、`deep_think_llm` 设成贵模型。 |
| **控制新闻量** | `news_article_limit`（默认 20）、`global_news_article_limit`（默认 10）、`global_news_lookback_days`（默认 7 天）。调小能显著减少 token。 |
| **本地模型** | `llm_provider: "ollama"` + `OPENAI_COMPATIBLE_API_KEY`（本地服务通常不需要 Key）。数据完全不出本机。 |

### 5.6 数据源配置要小心

`data_vendors` 的注释里写得很明确：

> 配置的值就是**精确的厂商链**，请求**不会**被悄悄路由到你没选的厂商。

比如 `"core_stock_apis": "yfinance,alpha_vantage"` 表示按顺序尝试这两个。想让所有可用厂商都参与，写 `"default"`。

**工具级配置 `tool_vendors` 优先级高于分类级 `data_vendors`**。比如：

```python
config["tool_vendors"] = {"get_stock_data": "alpha_vantage"}
```

这样只有 `get_stock_data` 这个工具走 Alpha Vantage，其他照旧。

### 5.7 各家 API Key 的申请

| 厂商 | 变量 | 备注 |
| --- | --- | --- |
| OpenAI | `OPENAI_API_KEY` | |
| Google Gemini | `GOOGLE_API_KEY` | |
| Anthropic | `ANTHROPIC_API_KEY` | |
| xAI (Grok) | `XAI_API_KEY` | |
| DeepSeek | `DEEPSEEK_API_KEY` | |
| Qwen（阿里 DashScope） | `DASHSCOPE_API_KEY`（国际）<br>`DASHSCOPE_CN_API_KEY`（中国） | 两个端点不同，别搞混 |
| GLM（智谱） | `ZHIPU_API_KEY`（国际 / Z.AI）<br>`ZHIPU_CN_API_KEY`（中国 / BigModel） | 同上 |
| MiniMax | `MINIMAX_API_KEY`（全球 / api.minimax.io）<br>`MINIMAX_CN_API_KEY`（中国 / api.minimaxi.com） | 同上 |
| OpenRouter | `OPENROUTER_API_KEY` | 一个 Key 调多家 |
| Alpha Vantage | `ALPHA_VANTAGE_API_KEY` | **这是数据源，不是模型** |
| FRED | `FRED_API_KEY` | **也是数据源**，免费申请：https://fred.stlouisfed.org/docs/api/api_key.html |

**Azure OpenAI**：把 `.env.enterprise.example` 复制成 `.env.enterprise` 填入凭证。

**AWS Bedrock**：需要额外装扩展

```bash
pip install ".[bedrock]"
```

然后设 `llm_provider: "bedrock"`，配置 AWS 凭证（环境变量 / `~/.aws/credentials` / IAM 角色都行）和 `AWS_DEFAULT_REGION`，用 Bedrock 模型 ID，比如 `us.anthropic.claude-opus-4-8-v1:0`。

> **Bedrock 支持两种认证**：Bearer token（`AWS_BEARER_TOKEN_BEDROCK`）或标准 AWS 凭证链。**两者都存在时 Bearer token 优先。**

---

## 🗂️ 六、目录结构速览

```text
TradingAgents/
├── main.py                        # 最小示例：跑一次 NVDA 分析
├── test.py                        # 一个小的性能测试脚本（指标计算耗时）
├── cli/                           # ★ 命令行界面
│   ├── main.py                    #   Typer 应用，交互式选择 + analyze 子命令
│   ├── config.py                  #   CLI 配置（公告接口地址等）
│   ├── models.py                  #   CLI 的数据模型
│   ├── utils.py                   #   工具函数
│   ├── announcements.py           #   拉取官方公告（失败时优雅降级）
│   ├── stats_handler.py           #   token / 时间统计显示
│   └── static/                    #   静态资源
├── tradingagents/                 # ★ 核心库
│   ├── default_config.py          #   默认配置 + TRADINGAGENTS_* 环境变量覆盖
│   ├── reporting.py               #   报告树写出（分节 md + complete_report.md）
│   ├── graph/                     #   ★ LangGraph 图
│   │   ├── setup.py               #     建图：节点、边、条件路由
│   │   ├── trading_graph.py       #     主类 TradingAgentsGraph，propagate() 入口
│   │   ├── propagation.py         #     状态在各节点间的传递
│   │   ├── conditional_logic.py   #     条件逻辑（辩论结束了吗？风险讨论完了吗？）
│   │   ├── analyst_execution.py   #     分析师执行计划（支持只选部分分析师）
│   │   ├── reflection.py          #     反思层：算实际收益 → 生成教训
│   │   ├── signal_processing.py   #     把投资组合经理的评级解析成交易信号
│   │   └── checkpointer.py        #     检查点保存 / 清理 / thread_id
│   ├── agents/                    # ★ 所有智能体
│   │   ├── analysts/              #   基本面 / 市场 / 情绪 / 新闻 / 社交媒体分析师
│   │   ├── researchers/           #   多头 / 空头研究员
│   │   ├── managers/              #   研究经理 / 投资组合经理
│   │   ├── risk_mgmt/             #   激进 / 保守 / 中立风险辩手
│   │   ├── trader/                #   交易员
│   │   ├── utils/                 #   智能体工具函数（取行情、取指标、取财报…）
│   │   └── schemas.py             #   结构化输出的 Pydantic schema
│   ├── dataflows/                 # ★ 数据接入层
│   │   ├── interface.py           #   对外的统一取数入口
│   │   ├── y_finance.py           #   yfinance 行情与指标
│   │   ├── alpha_vantage*.py      #   Alpha Vantage（基本面 / 指标 / 新闻 / 行情）
│   │   ├── fred.py                #   FRED 宏观数据
│   │   ├── polymarket.py          #   Polymarket 预测市场
│   │   ├── stocktwits.py          #   StockTwits 情绪
│   │   ├── reddit.py              #   Reddit 情绪
│   │   ├── stockstats_utils.py    #   技术指标计算
│   │   ├── symbol_utils.py        #   股票代码归一化
│   │   ├── date_window.py         #   ★ 统一的 UTC 半开时间窗口规则
│   │   ├── market_data_validator.py # 行情数据校验
│   │   └── config.py              #   数据层配置
│   └── llm_clients/               # ★ 各家 LLM 客户端
│       ├── factory.py             #   工厂：按 provider 创建客户端（懒加载）
│       ├── base_client.py         #   客户端基类
│       ├── openai_client.py / anthropic_client.py / google_client.py
│       ├── azure_client.py / bedrock_client.py
│       ├── model_catalog.py       #   模型目录（每个厂商有哪些模型）
│       ├── capabilities.py        #   各模型能力（支不支持思考模式等）
│       ├── validators.py          #   模型名校验
│       └── api_key_env.py         #   各厂商 API Key 的环境变量自动探测
├── tests/                         # ★ 60+ 个测试文件
├── assets/                        # README 用到的图片
├── scripts/smoke_structured_output.py  # 结构化输出冒烟测试
├── cli/                           # （见上）
├── pyproject.toml                 # 包定义、依赖、ruff 配置、pytest 配置
├── requirements.txt               # 只有一个「.」，意思是「装本项目自己」
├── Dockerfile                     # python:3.12-slim，非 root 用户运行
├── docker-compose.yml             # tradingagents + ollama profile
├── .env.example                   # 所有环境变量的完整说明
├── .env.enterprise.example        # Azure OpenAI 的配置模板
├── CHANGELOG.md                   # 完整的版本变更记录
└── .github/workflows/ci.yml       # CI
```

**几个关键点：**

| 事项 | 说明 |
| --- | --- |
| **为什么有 `dataflows/` 这一层？** | 所有外部数据访问都收在这一层。上层 agent 不直接碰 yfinance。这样换数据源只改这一层，agent 那边不动。 |
| **为什么 `date_window.py` 单独一个文件？** | 因为「时点正确」的裁剪规则**必须全项目统一**。新闻、社交、宏观都要用同一套 UTC 半开区间规则。规则散落各处就一定会出现某个源漏裁。 |
| **为什么 LLM 客户端要懒加载？** | `factory.py` 的注释说得很清楚：导入工厂时不要把一堆重量级 LLM SDK 都拉进来，也不要因为缺 API Key 就炸。测试收集阶段只 import 工厂，不该触发这些问题。 |
| **`requirements.txt` 为什么只有「.」？** | 意思是「装本项目自己的依赖」。真正的依赖列表在 `pyproject.toml` 里。 |

---

## 📊 七、本仓库说明

### 7.1 这是什么

本仓库是 **[`sdhfsl/TradingAgents`](https://github.com/sdhfsl/TradingAgents)** 的 fork（派生副本）。

| 层级 | 项目 | 地址 |
| --- | --- | --- |
| **本仓库** | `aiwozhonghua81/TradingAgents` | https://github.com/aiwozhonghua81/TradingAgents |
| **直接上游（本仓库 fork 自它）** | `sdhfsl/TradingAgents` | https://github.com/sdhfsl/TradingAgents |
| **原始上游（`sdhfsl/TradingAgents` 又 fork 自它）** | `TauricResearch/TradingAgents` | https://github.com/TauricResearch/TradingAgents |

> **为什么会有两层 fork？**
> `TauricResearch/TradingAgents` 是这个项目最原始、也是主要维护的版本（论文作者团队，社区活跃，曾拿过 GitHub 日榜第一）。`sdhfsl/TradingAgents` 在它基础上派生，本仓库再从 `sdhfsl` 派生，同时保留了对 `TauricResearch` 的追踪关系。

### 7.2 本仓库的改动

**代码层面：目前没有本地独立改动。**

核对结果：

| 核对项 | 结果 |
| --- | --- |
| 本地 `main` 与 `sdhfsl/TradingAgents` 的 `main` 的差异 | **完全一致**（双向差异 0 个 commit，文件差异 0 个） |
| 本地独有的 commit | 无 |
| 已删除的上游 commit | 无 |

也就是说，**本仓库当前的代码状态和直接上游 `sdhfsl/TradingAgents` 的 `main` 分支一模一样**，本仓库唯一的独有改动就是这一节中文说明。

这个仓库的用途是：作为个人自用的镜像 / 备份，方便在不改上游的情况下加中文说明、随时同步上游更新。

**同步上游的方法：**

```bash
git fetch sdhfsl
git merge sdhfsl/main
# 有冲突就解决后
git push origin main
```

### 7.3 README 的结构

本 README 分成上下两半：

| 位置 | 内容 | 语言 |
| --- | --- | --- |
| **上半部分** | 「中文说明开始」～「中文说明结束」之间 | 中文（本节，你正在看的） |
| **下半部分** | 从 `<p align="center">` 那段 Logo 开始往下 | 上游原始 README，英文，**一字未改** |

**关于其他语言的 README：**

上游 README 顶部有一排多语言链接（Deutsch / Español / français / 日本語 / 한국어 / Português / Русский / 中文），它们由 [readme-i18n](https://readme-i18n.com) 这个服务自动生成和管理。

> **注意**：因为这些翻译是由外部服务托管的，**本仓库的 README 改动不会自动同步到那些翻译页面**（翻译服务跟随的是原始上游仓库）。想看中文版可以点那个「中文」链接，但内容是上游的版本，不包含本仓库新增的这一节。

### 7.4 版本记录

本仓库当前对应上游 **v0.4.2**（`pyproject.toml` 里写的是 `version = "0.4.0"`，仓库里已合并了 v0.4.2 的 PR #1310，`git describe` 显示 `v0.4.0-17-gbe952b8`）。完整变更记录在 `CHANGELOG.md` 里，本仓库原样保留。

主要版本里程碑：

| 版本 | 时间 | 主要内容 |
| --- | --- | --- |
| v0.4.x | 2026-08 | 时点正确性修复、决策信号更清晰、检查点续跑真正能用、GPT-5.6 / GLM-5.3 |
| v0.3.x | 2026-07 | Alpha Vantage 前视过滤、图路由崩溃安全、加密货币情绪源、LLM 重试预算可配、Bedrock 认证 |
| v0.3.0 | 2026-06 | 数据访问契约验证、扩展厂商注册表、FRED + Polymarket、模型目录、CI 门禁 |
| v0.2.5 | 2026-05 | 有依据的情绪分析师、Qwen/GLM/MiniMax 双区域支持、环境变量可配、远程 Ollama |
| v0.2.4 | 2026-04 | 结构化输出智能体、LangGraph 检查点续跑、持久化决策日志、Docker、Windows UTF-8 修复 |

### 7.5 许可

上游项目采用 **Apache License 2.0**（仓库根目录的 `LICENSE` 文件），本 fork 同样遵循。

---

## 🔗 八、相关链接

| 资源 | 地址 |
| --- | --- |
| 原始上游仓库 | https://github.com/TauricResearch/TradingAgents |
| 直接上游仓库 | https://github.com/sdhfsl/TradingAgents |
| 论文（arXiv） | https://arxiv.org/abs/2412.20138 |
| 相关论文 Trading-R1 | https://arxiv.org/abs/2509.11420 |
| 官方 Discord | https://discord.com/invite/hk9PGKShPK |
| X（Twitter） | https://x.com/TauricResearch |
| 变更记录 | 本仓库的 `CHANGELOG.md` |
| 免责声明 | https://tauric.ai/disclaimer/ |


## 🖼️ 界面截图一览
下面这些图都是**这个仓库里原本就带的真实截图**，不是画的示意图：

**① 整体框架图**

<img src="assets/schema.png" alt="整体框架图" width="820">

**② 分析师角色**

<img src="assets/analyst.png" alt="分析师角色" width="820">

**③ 研究员角色**

<img src="assets/researcher.png" alt="研究员角色" width="820">

**④ 交易员角色**

<img src="assets/trader.png" alt="交易员角色" width="820">

**⑤ 风控角色**

<img src="assets/risk.png" alt="风控角色" width="820">

<!-- 中文说明结束 -->

---

（原上游 README 从这里开始）
<p align="center">
  <img src="assets/TauricResearch.png" style="width: 60%; height: auto;">
</p>

<div align="center" style="line-height: 1;">
  <a href="https://arxiv.org/abs/2412.20138" target="_blank"><img alt="arXiv" src="https://img.shields.io/badge/arXiv-2412.20138-B31B1B?logo=arxiv"/></a>
  <a href="https://discord.com/invite/hk9PGKShPK" target="_blank"><img alt="Discord" src="https://img.shields.io/badge/Discord-TradingResearch-7289da?logo=discord&logoColor=white&color=7289da"/></a>
  <a href="https://x.com/TauricResearch" target="_blank"><img alt="X Follow" src="https://img.shields.io/badge/X-TauricResearch-white?logo=x&logoColor=white"/></a>
  <a href="https://github.com/TauricResearch/" target="_blank"><img alt="Community" src="https://img.shields.io/badge/GitHub_Community-TauricResearch-14C290?logo=discourse"/></a>
</div>
<br>
<div align="center">
  <a href="https://github.com/TauricResearch" target="_blank"><img alt="TradingAgents #1 Repository of the Day" src="https://trendshift.io/api/badge/repositories/16192" width="250" height="55"/></a>
</div>
<br>
<div align="center">
  <!-- Keep these links. Translations will automatically update with the README. -->
  <a href="https://www.readme-i18n.com/TauricResearch/TradingAgents?lang=de">Deutsch</a> | 
  <a href="https://www.readme-i18n.com/TauricResearch/TradingAgents?lang=es">Español</a> | 
  <a href="https://www.readme-i18n.com/TauricResearch/TradingAgents?lang=fr">français</a> | 
  <a href="https://www.readme-i18n.com/TauricResearch/TradingAgents?lang=ja">日本語</a> | 
  <a href="https://www.readme-i18n.com/TauricResearch/TradingAgents?lang=ko">한국어</a> | 
  <a href="https://www.readme-i18n.com/TauricResearch/TradingAgents?lang=pt">Português</a> | 
  <a href="https://www.readme-i18n.com/TauricResearch/TradingAgents?lang=ru">Русский</a> | 
  <a href="https://www.readme-i18n.com/TauricResearch/TradingAgents?lang=zh">中文</a>
</div>

---

# TradingAgents: Multi-Agents LLM Financial Trading Framework

## News
- [2026-08] **TradingAgents v0.4.0** released with look-ahead / point-in-time fixes across FRED macro, social sentiment, and the decision-log memory; clearer decision signals; working CLI checkpoint resume; Trader price grounding; and the GPT-5.6 and GLM-5.3 models. See [CHANGELOG.md](CHANGELOG.md) for the full list.
- [2026-07] **TradingAgents v0.3.1** released with correctness and stability fixes: Alpha Vantage look-ahead filtering, graph-router crash-safety, graph-shape-aware checkpoint resume, working crypto sentiment sources, a configurable LLM retry budget, Bedrock API-key auth, and Claude Sonnet 5 / Fable 5 support.
- [2026-06] **TradingAgents v0.3.0** released with a verified data-access contract, an expanded provider registry (NVIDIA, Kimi, Groq, Mistral, Bedrock, and any OpenAI-compatible endpoint), FRED and Polymarket data vendors, a current-generation model catalog, and a CI gate.
- [2026-05] **TradingAgents v0.2.5** released with the grounded Sentiment Analyst, GPT-5.5 etc. model coverage, Qwen/GLM/MiniMax dual-region support, `TRADINGAGENTS_*` env-var configurability with API-key auto-detection, remote Ollama support, non-US alpha benchmarks, and ticker path-traversal hardening.
- [2026-04] **TradingAgents v0.2.4** released with structured-output agents (Research Manager, Trader, Portfolio Manager), LangGraph checkpoint resume, persistent decision log, DeepSeek/Qwen/GLM/Azure provider support, Docker, and a Windows UTF-8 encoding fix.
- [2026-03] **TradingAgents v0.2.3** released with multi-language support, GPT-5.4 family models, unified model catalog, backtesting date fidelity, and proxy support.
- [2026-03] **TradingAgents v0.2.2** released with GPT-5.4/Gemini 3.1/Claude 4.6 model coverage, five-tier rating scale, OpenAI Responses API, Anthropic effort control, and cross-platform stability.
- [2026-02] **TradingAgents v0.2.0** released with multi-provider LLM support (GPT-5.x, Gemini 3.x, Claude 4.x, Grok 4.x) and improved system architecture.
- [2026-01] **Trading-R1** [Technical Report](https://arxiv.org/abs/2509.11420) released, with [Terminal](https://github.com/TauricResearch/Trading-R1) expected to land soon.

<div align="center">

🚀 [TradingAgents](#tradingagents-framework) | ⚡ [Installation & CLI](#installation-and-cli) | 🎬 [Demo](https://www.youtube.com/watch?v=90gr5lwjIho) | 📦 [Package Usage](#tradingagents-package) | 🤝 [Contributing](#contributing) | 📄 [Citation](#citation)

</div>

> 🎉 **TradingAgents** officially released! We have received numerous inquiries about the work, and we would like to express our thanks for the enthusiasm in our community.
>
> So we decided to fully open-source the framework. Looking forward to building impactful projects with you!

## TradingAgents Framework

TradingAgents is a multi-agent trading framework that mirrors the dynamics of real-world trading firms. By deploying specialized LLM-powered agents: from fundamental analysts, sentiment experts, and technical analysts, to trader, risk management team, the platform collaboratively evaluates market conditions and informs trading decisions. Moreover, these agents engage in dynamic discussions to pinpoint the optimal strategy.

<p align="center">
  <img src="assets/schema.png" style="width: 100%; height: auto;">
</p>

> TradingAgents framework is designed for research purposes. Trading performance may vary based on many factors, including the chosen backbone language models, model temperature, trading periods, the quality of data, and other non-deterministic factors. [It is not intended as financial, investment, or trading advice.](https://tauric.ai/disclaimer/)

Our framework decomposes complex trading tasks into specialized roles.

### Analyst Team
- Fundamentals Analyst: Evaluates company financials and performance metrics, identifying intrinsic values and potential red flags.
- Sentiment Analyst: Aggregates news headlines, StockTwits, and Reddit chatter into a single sentiment read to gauge short-term market mood.
- News Analyst: Monitors global news and macroeconomic indicators, interpreting the impact of events on market conditions.
- Technical Analyst: Utilizes technical indicators (like MACD and RSI) to detect trading patterns and forecast price movements.

<p align="center">
  <img src="assets/analyst.png" width="100%" style="display: inline-block; margin: 0 2%;">
</p>

### Researcher Team
- Comprises both bullish and bearish researchers who critically assess the insights provided by the Analyst Team. Through structured debates, they balance potential gains against inherent risks.

<p align="center">
  <img src="assets/researcher.png" width="70%" style="display: inline-block; margin: 0 2%;">
</p>

### Trader Agent
- Composes reports from the analysts and researchers to make informed trading decisions, determining the timing and magnitude of trades.

<p align="center">
  <img src="assets/trader.png" width="70%" style="display: inline-block; margin: 0 2%;">
</p>

### Risk Management and Portfolio Manager
- Continuously evaluates portfolio risk by assessing market volatility, liquidity, and other risk factors. The risk management team evaluates and adjusts trading strategies, providing assessment reports to the Portfolio Manager for final decision.
- The Portfolio Manager approves/rejects the transaction proposal. If approved, the order will be sent to the simulated exchange and executed.

<p align="center">
  <img src="assets/risk.png" width="70%" style="display: inline-block; margin: 0 2%;">
</p>

## Installation and CLI

### Installation

Clone TradingAgents:
```bash
git clone https://github.com/TauricResearch/TradingAgents.git
cd TradingAgents
```

Create a virtual environment in any of your favorite environment managers:
```bash
conda create -n tradingagents python=3.12
conda activate tradingagents
```

Install the package and its dependencies:
```bash
pip install .
```

### Docker

Alternatively, run with Docker:
```bash
cp .env.example .env  # add your API keys
docker compose run --rm tradingagents
```

For local models with Ollama:
```bash
docker compose --profile ollama run --rm tradingagents-ollama
```

### Required APIs

TradingAgents supports multiple LLM providers. Set the API key for your chosen provider:

```bash
export OPENAI_API_KEY=...          # OpenAI (GPT)
export GOOGLE_API_KEY=...          # Google (Gemini)
export ANTHROPIC_API_KEY=...       # Anthropic (Claude)
export XAI_API_KEY=...             # xAI (Grok)
export DEEPSEEK_API_KEY=...        # DeepSeek
export DASHSCOPE_API_KEY=...       # Qwen — International (dashscope-intl.aliyuncs.com)
export DASHSCOPE_CN_API_KEY=...    # Qwen — China (dashscope.aliyuncs.com)
export ZHIPU_API_KEY=...           # GLM via Z.AI (international)
export ZHIPU_CN_API_KEY=...        # GLM via BigModel (China, open.bigmodel.cn)
export MINIMAX_API_KEY=...         # MiniMax — Global (api.minimax.io)
export MINIMAX_CN_API_KEY=...      # MiniMax — China (api.minimaxi.com)
export OPENROUTER_API_KEY=...      # OpenRouter
export ALPHA_VANTAGE_API_KEY=...   # Alpha Vantage
```

For Azure OpenAI, copy `.env.enterprise.example` to `.env.enterprise` and fill in your credentials.

For AWS Bedrock, install the extra with `pip install ".[bedrock]"`, set `llm_provider: "bedrock"`, configure AWS credentials (environment variables, `~/.aws/credentials`, or an IAM role) and `AWS_DEFAULT_REGION`, and use a Bedrock model ID, e.g. `us.anthropic.claude-opus-4-8-v1:0`.

For local models, configure Ollama with `llm_provider: "ollama"`. The default endpoint is `http://localhost:11434/v1`; set `OLLAMA_BASE_URL` to point at a remote `ollama-serve`. Pull models with `ollama pull <name>`, and pick "Custom model ID" in the CLI for any model not listed by default.

For any other OpenAI-compatible server (vLLM, LM Studio, llama.cpp, or a custom relay), use `llm_provider: "openai_compatible"` and set the endpoint via `backend_url` (or `TRADINGAGENTS_LLM_BACKEND_URL`), e.g. `http://localhost:8000/v1` for vLLM or `http://localhost:1234/v1` for LM Studio. The model is whatever your server serves. No key is needed for local servers; set `OPENAI_COMPATIBLE_API_KEY` when the endpoint requires one.

Alternatively, copy `.env.example` to `.env` and fill in your keys:
```bash
cp .env.example .env
```

### CLI Usage

Launch the interactive CLI:
```bash
tradingagents          # installed command
python -m cli.main     # alternative: run directly from source
```
You will see a screen where you can select your desired tickers, analysis date, LLM provider, research depth, and more.

### Markets and tickers

TradingAgents works with any market Yahoo Finance covers, using the exchange-suffixed ticker. Company identity and the alpha benchmark resolve automatically per market.

- US: `AAPL`, `SPY`
- Hong Kong: `0700.HK` · Tokyo: `7203.T` · London: `AZN.L`
- India: `RELIANCE.NS`, `.BO` · Canada: `.TO` · Australia: `.AX`
- China A-shares: Shanghai `.SS`, Shenzhen `.SZ` (e.g. `600519.SS` for Kweichow Moutai)
- Crypto: `BTC-USD`, `ETH-USD`

<p align="center">
  <img src="assets/cli/cli_init.png" width="100%" style="display: inline-block; margin: 0 2%;">
</p>

An interface will appear showing results as they load, letting you track the agent's progress as it runs.

<p align="center">
  <img src="assets/cli/cli_news.png" width="100%" style="display: inline-block; margin: 0 2%;">
</p>

<p align="center">
  <img src="assets/cli/cli_transaction.png" width="100%" style="display: inline-block; margin: 0 2%;">
</p>

## TradingAgents Package

### Implementation Details

We built TradingAgents with LangGraph to ensure flexibility and modularity. The framework supports multiple LLM providers: OpenAI, Google, Anthropic, xAI, DeepSeek, Qwen (Alibaba DashScope, international and China endpoints), GLM (Zhipu), MiniMax (global + China), OpenRouter, Ollama for local models, and Azure OpenAI for enterprise.

### Python Usage

To use TradingAgents inside your code, you can import the `tradingagents` module and initialize a `TradingAgentsGraph()` object. The `.propagate()` function will return a decision. You can run `main.py`, here's also a quick example:

```python
from tradingagents.graph.trading_graph import TradingAgentsGraph
from tradingagents.default_config import DEFAULT_CONFIG

ta = TradingAgentsGraph(debug=True, config=DEFAULT_CONFIG.copy())

# forward propagate
_, decision = ta.propagate("NVDA", "2026-01-15")
print(decision)
```

You can also adjust the default configuration to set your own choice of LLMs, debate rounds, etc.

```python
from tradingagents.graph.trading_graph import TradingAgentsGraph
from tradingagents.default_config import DEFAULT_CONFIG

config = DEFAULT_CONFIG.copy()
config["llm_provider"] = "openai"        # e.g. openai, google, anthropic, deepseek, groq, ollama; openai_compatible covers any OpenAI-compatible endpoint (vLLM, LM Studio, llama.cpp, ...)
config["deep_think_llm"] = "gpt-5.6"      # Model for complex reasoning
config["quick_think_llm"] = "gpt-5.6-luna" # Model for quick tasks
config["max_debate_rounds"] = 2

ta = TradingAgentsGraph(debug=True, config=config)
_, decision = ta.propagate("NVDA", "2026-01-15")
print(decision)
```

See `tradingagents/default_config.py` for all configuration options.

## Persistence and Recovery

TradingAgents persists two kinds of state across runs.

### Decision log

The decision log is always on. Each completed run appends its decision to `~/.tradingagents/memory/trading_memory.md`. On the next run for the same ticker, TradingAgents fetches the realised return (raw and alpha vs SPY), generates a one-paragraph reflection, and injects the most recent same-ticker decisions plus recent cross-ticker lessons into the Portfolio Manager prompt, so each analysis carries forward what worked and what didn't.

Override the path with `TRADINGAGENTS_MEMORY_LOG_PATH`.

### Checkpoint resume

Checkpoint resume is opt-in via `--checkpoint`. When enabled, LangGraph saves state after each node so a crashed or interrupted run resumes from the last successful step instead of starting over. On a resume run you will see `Resuming from step N for <TICKER> on <date>` in the logs; on a new run you will see `Starting fresh`. Checkpoints are cleared automatically on successful completion.

Per-ticker SQLite databases live at `~/.tradingagents/cache/checkpoints/<TICKER>.db` (override the base with `TRADINGAGENTS_CACHE_DIR`). Use `--clear-checkpoints` to reset all of them before a run.

```bash
tradingagents analyze --checkpoint           # enable for this run
tradingagents analyze --clear-checkpoints    # reset before running
```

```python
config = DEFAULT_CONFIG.copy()
config["checkpoint_enabled"] = True
ta = TradingAgentsGraph(config=config)
_, decision = ta.propagate("NVDA", "2026-01-15")
```

## Reproducibility

TradingAgents is LLM-driven, so two runs of the same ticker and date can differ. This is expected for a research tool built on language models, not a defect. The variation comes from a few distinct sources, and it helps to separate them.

Language model sampling is non-deterministic. Even at a fixed temperature, providers do not guarantee byte-identical output across calls, and reasoning models (the default GPT-5.x family, and any thinking-mode model) vary the most because their internal reasoning is itself sampled.

Live data moves. News, StockTwits, and Reddit return different content as time passes, so a run today sees different inputs than a run last week even for the same historical trade date. Pin the analysis date to hold the price and indicator window fixed, but the social and news sources still reflect "now".

To reduce variation you can lower the sampling temperature. Set `temperature` in your config (or `TRADINGAGENTS_TEMPERATURE` in `.env`); lower values make models that honor it more repeatable. The current curated models are reasoning-first and largely ignore temperature, so for tighter reproducibility use a non-reasoning model, which you can set explicitly via the Custom model ID option.

```python
config = DEFAULT_CONFIG.copy()
config["llm_provider"] = "openai"
config["temperature"] = 0.0
# Reasoning models ignore temperature. For tighter reproducibility, set a
# non-reasoning deep/quick model explicitly (e.g. via the Custom model ID option).
```

What does not vary anymore: the analyzed company identity is resolved deterministically from the ticker before any agent runs, and the market analyst grounds exact price and indicator claims in a verified data snapshot. Earlier reports of "different companies" or fabricated price levels across runs are addressed by these two mechanisms.

Backtest results are not guaranteed to match any published figure. Returns depend on the model, the temperature, the date range, data quality, and the sampling above. Treat the framework as a research scaffold for studying multi-agent analysis, not as a strategy with a fixed, replicable return.

## Contributing

Contributions are welcome: bug fixes, documentation, and feature ideas; past contributions are credited per release in [`CHANGELOG.md`](CHANGELOG.md).

## Citation

Please reference our work if you find *TradingAgents* provides you with some help :)

```
@misc{xiao2025tradingagentsmultiagentsllmfinancial,
      title={TradingAgents: Multi-Agents LLM Financial Trading Framework}, 
      author={Yijia Xiao and Edward Sun and Di Luo and Wei Wang},
      year={2025},
      eprint={2412.20138},
      archivePrefix={arXiv},
      primaryClass={q-fin.TR},
      url={https://arxiv.org/abs/2412.20138}, 
}
```
