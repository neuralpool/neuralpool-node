---
name: neuralpool-node
description: Monetize idle LLM API capacity or access 100+ models through a unified OpenAI-compatible API. No GPU required.
homepage: https://neuralpool.ai
metadata:
  clawdbot:
    emoji: "🌐"
    requires:
      env: ["NEURALPOOL_AUTH_TOKEN"]
    primaryEnv: "NEURALPOOL_AUTH_TOKEN"
    files: ["scripts/install.sh"]
---

# NeuralPool Node

## What is NeuralPool?

NeuralPool is a decentralized LLM API marketplace. Think of it as an "Airbnb for LLM API access" — a peer-to-peer network where API resource owners and AI developers meet.

### Two ways to participate

**1. As a Node Operator (Earn Money)**

If you have cloud compute credits, access to LLM APIs, or self-hosted inference endpoints sitting idle, you can monetize them:

- Register at [neuralpool.ai](https://neuralpool.ai)
- Install this Node Agent on your machine
- Configure your upstream providers and set your own prices
- Your idle capacity starts earning immediately — paid per token forwarded
- Earnings settle after a T+1 clearing period, then withdraw to your Solana wallet

Your API keys **never leave your machine**. The Node Agent connects to NeuralPool's central server via encrypted gRPC tunnel, receives inference requests, forwards them to your configured upstream provider, and streams the response back. The server handles all billing, user management, and payment settlement.

**2. As an API Consumer (Save Money)**

If you build AI applications and need affordable, reliable access to LLM models:

- Register at [neuralpool.ai](https://neuralpool.ai)
- Deposit USDT (SPL) to your account
- Get a single OpenAI-compatible API key that works across **all models from all providers** on the network
- Pay only for what you use — token-by-token billing with no monthly commitments
- Often significantly cheaper than direct provider pricing due to marketplace competition

### How it works

```
API Consumer                    Central Server                   Node Operator
    │                               │                               │
    │  OpenAI-format request        │                               │
    │──────────────────────────────>│                               │
    │                               │  gRPC tunnel (encrypted)      │
    │                               │──────────────────────────────>│
    │                               │                               │──> Upstream LLM
    │                               │                               │<── Provider API
    │<──────────────────────────────│                               │
    │  OpenAI-format response       │  Billing: token-by-token      │
    │                               │  Settlement: NC credits        │
```

## Platform Features

- **Unified API**: One endpoint, one API key, all models. Protocol translation between different provider formats happens transparently inside Node agents.
- **Self-pricing**: Node operators set their own per-model input/output prices. Market competition drives prices down for consumers.
- **Key security**: Upstream API keys never leave the Node operator's machine. Zero trust architecture.
- **Real-time billing**: PreLock/Settle mechanism ensures accurate token-by-token charging. No over-billing.
- **Solana payments**: Deposit USDT (SPL), withdraw to any Solana wallet after T+1 settlement. NC (NeuralCredit) internal currency: 1 USD = 100 NC.
- **Multi-provider**: Support for any OpenAI-compatible endpoint, Anthropic-format APIs, Google Gemini, local inference engines (vLLM, Ollama), and more.

## Quick Start for Node Operators

### Step 1: Register

Visit [neuralpool.ai](https://neuralpool.ai) and create an account. Navigate to the Nodes section and generate an authentication token.

### Step 2: Install

Run the installer script — it auto-detects your OS and architecture:

```bash
curl -fsSL https://neuralpool.ai/install.sh | bash
```

Or download manually from [GitHub Releases](https://github.com/neuralpool/node/releases):

| Platform | File |
|----------|------|
| Linux x86_64 | `npnode-linux-amd64` |
| Linux ARM64 | `npnode-linux-arm64` |
| macOS ARM64 | `npnode-darwin-arm64` |
| macOS x86_64 | `npnode-darwin-amd64` |
| Windows x86_64 | `npnode-windows-amd64.exe` |

### Step 3: Configure

```bash
npnode setup
```

The interactive wizard will guide you through:
- Setting your NeuralPool auth token
- Adding upstream LLM providers (API key, base URL)
- Selecting models to serve and setting your prices (per 1M tokens, input/output separately)
- Configuring concurrency and timeout limits

### Step 4: Start

```bash
npnode start
```

Your Node connects to the NeuralPool network and begins receiving requests. You earn NC credits for every token forwarded.

### Step 5: Monitor & Withdraw

- Dashboard at [neuralpool.ai](https://neuralpool.ai) shows real-time earnings, request stats, and node health
- Withdraw earnings to any Solana wallet at any time

## Quick Start for API Consumers

1. Register at [neuralpool.ai](https://neuralpool.ai)
2. Deposit USDT (SPL) to your dedicated deposit address
3. Generate an API key from the dashboard
4. Use it as a drop-in replacement for any OpenAI SDK:

```python
from openai import OpenAI

client = OpenAI(
    base_url="https://neuralpool.ai/v1",
    api_key="np-YOUR_API_KEY"
)

response = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Hello!"}]
)
```

## Node Agent Commands

| Command | Description |
|---------|-------------|
| `npnode setup` | Interactive configuration wizard |
| `npnode start` | Start the Node and begin forwarding requests |
| `npnode config` | Display current configuration |
| `npnode reset-quota [model]` | Reset token quota counter for a model (or all) |
| `npnode version` | Print version |

## Pricing & Currency

- **NC (NeuralCredit)**: Internal platform currency. 1 USD = 100 NC = 1,000,000 uNC
- Node operators set prices in uNC per 1M tokens (input and output separately)
- Platform applies a commission (default 15%) on top of node operator prices
- Consumers see and pay the final price including commission

## Security Model

- Upstream API keys stored locally in `node.yaml`, never transmitted to the server
- gRPC communication encrypted via ECDH (X25519) + AES-256-GCM tunnel
- TLS certificates for node-to-server authentication
- HMAC-SHA256 key derivation for user deposit addresses
- Signer server (offline) manages all blockchain private keys

## Requirements

- Any machine with internet access (no GPU required — you're proxying API calls, not running inference locally)
- Go 1.24+ (only if building from source)
- Network access to NeuralPool Central Server (gRPC port 50051)
- Network access to your configured upstream LLM provider APIs

## External Endpoints

| URL | Purpose | Data Sent |
|-----|---------|-----------|
| `neuralpool.ai:50051` | gRPC tunnel to central server | Auth token, model requests/responses, billing data |
| User-configured endpoints | Any LLM provider API the operator configures | LLM prompts, receives completions |

## Links

- **Website**: [neuralpool.ai](https://neuralpool.ai)
- **Documentation**: [docs.neuralpool.ai](https://neuralpool.ai/docs)
- **GitHub**: [github.com/neuralpool](https://github.com/neuralpool)
- **Support**: support@neuralpool.ai

## License

MIT

---

# NeuralPool ノード

## NeuralPoolとは？

NeuralPoolは、分散型LLM APIマーケットプレイスです。「LLM APIアクセスのAirbnb」のような存在で、APIリソースの所有者とAI開発者が出会うP2Pネットワークです。

### 参加方法は2つ

**1. ノードオペレーターとして（収益化）**

クラウドコンピューティングクレジット、LLM APIへのアクセス、またはセルフホスト推論エンドポイントが余っている場合、それを収益化できます：

- [neuralpool.ai](https://neuralpool.ai) でアカウント登録
- このノードエージェントをマシンにインストール
- 上流プロバイダーを設定し、独自の価格を設定
- 闲置キャパシティがすぐに収益を生み始めます — 転送されたトークンごとに課金
- 収益はT+1決済期間後にSolanaウォレットに出金可能

APIキーは**マシンから一切送信されません**。ノードエージェントは暗号化されたgRPCトンネルでNeuralPoolの中央サーバーに接続し、推論リクエストを受信、設定された上流プロバイダーに転送し、レスポンスをストリーミングします。サーバーは課金、ユーザー管理、決済をすべて処理します。

**2. API利用者として（コスト削減）**

AIアプリケーションを構築していて、安価で信頼性の高いLLMモデルアクセスが必要な場合：

- [neuralpool.ai](https://neuralpool.ai) でアカウント登録
- USDT（SPL）をアカウントに入金
- ネットワーク上の**すべてのプロバイダーのすべてのモデル**に対応する単一のOpenAI互換APIキーを取得
- 従量課金 — トークンごとの課金、月額コミットメントなし
- マーケットプレイスの競争により、直接プロバイダー料金より大幅に安価

### ノードオペレーターのクイックスタート

**ステップ1**: [neuralpool.ai](https://neuralpool.ai) で登録し、認証トークンを生成

**ステップ2**: インストール
```bash
curl -fsSL https://neuralpool.ai/install.sh | bash
```

**ステップ3**: 設定
```bash
npnode setup
```

**ステップ4**: 起動
```bash
npnode start
```

**ステップ5**: ダッシュボードでリアルタイム収益を確認し、Solanaウォレットに出金

### プラットフォーム機能

- **統合API**: エンドポイント1つ、APIキー1つで全モデル対応。異なるプロバイダー間のプロトコル変換はノードエージェント内で自動処理
- **自己価格設定**: ノードオペレーターがモデルごとに入力/出力価格を自由設定。市場競争が消費者価格を下げる
- **キーセキュリティ**: 上流APIキーはノードオペレーターのマシンから一切送信されない。ゼロトラストアーキテクチャ
- **リアルタイム課金**: PreLock/Settleメカニズムで正確なトークンごとの課金
- **Solana決済**: USDT（SPL）入金、T+1決済後に任意のSolanaウォレットに出金可能
- **マルチプロバイダー**: OpenAI互換エンドポイント、Anthropic形式API、Google Gemini、ローカル推論エンジン（vLLM、Ollama）などに対応

---

# NeuralPool 节点

## NeuralPool 是什么？

NeuralPool 是一个去中心化的 LLM API 市场。你可以把它想象成"LLM API 访问的 Airbnb"——一个 API 资源拥有者和 AI 开发者直接对接的 P2P 网络。

### 两种参与方式

**1. 作为节点运营者（赚钱）**

如果你有云计算额度、LLM API 访问权限、或自建推理服务处于闲置状态，你可以把它们变现：

- 在 [neuralpool.ai](https://neuralpool.ai) 注册账号
- 在你的机器上安装本节点代理
- 配置你的上游 LLM 供应商，自主定价
- 闲置算力立即开始赚钱——按转发的 token 计费
- 收益经 T+1 结算周期后可提现到你的 Solana 钱包

你的 API 密钥**永远不会离开你的机器**。节点代理通过加密的 gRPC 隧道连接到 NeuralPool 中央服务器，接收推理请求，转发到配置的上游供应商，并流式返回响应。服务器负责所有计费、用户管理和支付结算。

**2. 作为 API 使用者（省钱）**

如果你在构建 AI 应用，需要经济实惠、稳定可靠的 LLM 模型访问：

- 在 [neuralpool.ai](https://neuralpool.ai) 注册账号
- 向账户充值 USDT（SPL）
- 获取一个 OpenAI 兼容的 API 密钥，即可访问网络上**所有供应商的所有模型**
- 按量付费——逐 token 计费，无需月度承诺
- 由于市场竞争，通常比直接使用供应商便宜得多

### 节点运营者快速开始

**第1步**: 在 [neuralpool.ai](https://neuralpool.ai) 注册并生成认证令牌

**第2步**: 安装
```bash
curl -fsSL https://neuralpool.ai/install.sh | bash
```

**第3步**: 配置
```bash
npnode setup
```

**第4步**: 启动
```bash
npnode start
```

**第5步**: 在仪表盘查看实时收益，T+1 结算后提现到 Solana 钱包

### 平台特性

- **统一 API**: 一个端点、一个密钥、所有模型。不同供应商格式之间的协议转换在节点代理内部自动完成
- **自主定价**: 节点运营者自行设置每个模型的输入/输出价格。市场竞争为使用者带来更低价格
- **密钥安全**: 上游 API 密钥从不离开节点运营者的机器。零信任架构
- **实时计费**: PreLock/Settle 机制确保精确的逐 token 计费
- **Solana 支付**: 充值 USDT（SPL），T+1 结算后可提现到任意 Solana 钱包
- **多供应商支持**: 支持 OpenAI 兼容端点、Anthropic 格式 API、Google Gemini、本地推理引擎（vLLM、Ollama）等
