# 🍷 Wine Agent Starter Kit

**Build an AI wine purchasing agent in 5 minutes.**

An open-source starter template showing how to build an autonomous wine-purchasing agent using [Claude](https://anthropic.com) and the [Case Connect](https://trycase.ai) platform.

The agent can search wines, check regulatory compliance across all 50 US states, verify inventory, and simulate wholesale purchases — all through natural conversation.

https://github.com/user-attachments/assets/demo-placeholder

---

## What It Does

Ask the agent things like:

- *"Find me a Cabernet under $60 wholesale"*
- *"Can I buy wine directly from a California winery if I'm a retailer in Ohio?"*
- *"Order 5 cases of the Chardonnay to my shop in San Francisco"*
- *"Which states allow direct winery-to-retailer sales?"*

The agent uses Claude's tool-use capability to:

1. **Search** the wine catalog by varietal, price, winery, or keyword
2. **Check compliance** via the live [Case Connect Compliance API](https://case-ai-compliance-api.fly.dev/docs) — real US regulatory data
3. **Verify inventory** and calculate pricing
4. **Simulate orders** with built-in compliance + inventory checks

---

## Quick Start

### 1. Clone and install

```bash
git clone https://github.com/trycase-ai/wine-agent-starter-kit.git
cd wine-agent-starter-kit
pip install -r requirements.txt
```

### 2. Add your API key

```bash
export ANTHROPIC_API_KEY=your_key_here
```

Get a key at [console.anthropic.com](https://console.anthropic.com)

### 3. Run the demo

```bash
python demo.py
```

Or chat interactively:

```bash
python demo.py --interactive
```

---

## How It Works

The project has three files:

| File | What it does |
|---|---|
| `agent.py` | The AI agent — wraps Claude with a system prompt and tool-use loop |
| `tools.py` | 8 tools the agent can call (compliance API + mock catalog) |
| `demo.py` | Demo script with pre-built scenarios + interactive mode |

### Architecture

```
User message
    ↓
┌─────────────────────────┐
│  Claude (agent.py)      │
│  Reads the request,     │
│  decides which tools    │
│  to call                │
└────────┬────────────────┘
         ↓
┌─────────────────────────┐
│  Tools (tools.py)       │
│                         │
│  🔴 LIVE:               │
│  • check_compliance     │  ← Hits case-ai-compliance-api.fly.dev
│  • get_state_info       │
│  • get_license_types    │
│  • get_direct_states    │
│                         │
│  🟡 MOCK:               │
│  • search_wines         │  ← Replace with Case Connect API
│  • get_wine_details     │     when it launches
│  • check_inventory      │
│  • simulate_order       │
└─────────────────────────┘
```

The compliance tools hit the **real, live** [Case Connect Compliance API](https://case-ai-compliance-api.fly.dev/docs). The catalog/ordering tools use mock data — swap them for the real Case Connect API when it launches.

---

## Tools Reference

### Live (Case Connect Compliance API)

| Tool | Description |
|---|---|
| `check_compliance` | Check if a winery→retailer transaction is legal in a given state |
| `get_state_info` | Get compliance overview for any US state |
| `get_license_types` | Look up winery, retailer, or distributor license types |
| `get_direct_to_retailer_states` | List states allowing direct winery-to-retailer sales |

### Mock (replace with Case Connect API)

| Tool | Description |
|---|---|
| `search_wines` | Search catalog by varietal, price, winery, keyword |
| `get_wine_details` | Get full details for a specific wine |
| `check_inventory` | Check case availability for a wine |
| `simulate_order` | Place a simulated wholesale order (checks compliance + inventory) |

---

## Using in Your Own Project

### As a module

```python
from agent import WineAgent

agent = WineAgent()
response = agent.chat("What Pinot Noirs do you have?")
print(response)

# Continue the conversation (agent remembers context)
response = agent.chat("Check if I can order that to Ohio")
print(response)

# Start fresh
agent.reset()
```

### Customizing the agent

The system prompt in `agent.py` controls the agent's personality and behavior. Edit it to match your use case:

```python
class WineAgent:
    SYSTEM_PROMPT = """Your custom prompt here..."""
```

### Swapping in real API endpoints

When the Case Connect ordering API launches, update the functions in `tools.py`:

```python
# Before (mock):
def search_wines(query=None, varietal=None, ...):
    results = MOCK_CATALOG.copy()
    ...

# After (real API):
def search_wines(query=None, varietal=None, ...):
    params = {"q": query, "varietal": varietal, ...}
    resp = requests.get("https://api.trycase.ai/v1/wines", params=params)
    return resp.json()
```

---

## Example Conversation

```
You: I'm looking for a full-bodied red wine under $50 wholesale
     for my restaurant in San Francisco. I have a Type 47 license.

  🔧 Using tool: search_wines
  🔧 Using tool: check_compliance

Agent: I found a great option for you! The 2020 Hillside Pinot Noir
from Fog & Stone Vineyards is available at $37.50 wholesale per bottle
($450/case).

I've checked compliance and you're good to go — California Type 02
wineries can sell directly to Type 47 (on-sale general) retailers
under the self-distribution privilege. No distributor needed.

Want me to check inventory and place an order?

You: Yes, 3 cases please.

  🔧 Using tool: check_inventory
  🔧 Using tool: simulate_order

Agent: Order confirmed! Here's your summary:

• Wine: 2020 Hillside Pinot Noir
• Winery: Fog & Stone Vineyards
• Cases: 3
• Total: $1,350.00
• Payment: Via Stripe Connect (guaranteed instant)
• Compliance: ✅ Passed
```

---

## About Case Connect

[Case Connect](https://trycase.ai) is the API for programmatic wine purchasing. We connect high-end wineries directly with modern retailers — better margins for both sides, guaranteed instant payment, and full regulatory compliance built in.

- **Compliance API** (free): [case-ai-compliance-api.fly.dev/docs](https://case-ai-compliance-api.fly.dev/docs)
- **API Docs**: [trycase-ai.github.io/Case-AI-Compliance-api](https://trycase-ai.github.io/Case-AI-Compliance-api/)
- **Website**: [trycase.ai](https://trycase.ai)
- **Contact**: hello@trycase.ai

---

## License

MIT — use it however you want.
