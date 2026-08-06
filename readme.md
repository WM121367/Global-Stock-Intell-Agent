# 📈 TradFi & Global Stock Market Agent (`@prime-tradfi-oracle`)

> **Institutional-Grade Global Stock Indices, Sovereign Yields & Macro Liquidity Intelligence Sub-Agent**

[![Version](https://img.shields.io/badge/Version-1.0.0-blue.svg)](https://github.com/your-org/tradfi-stock-agent)
[![Framework](https://img.shields.io/badge/Framework-Fetch.ai%20uAgents-orange.svg)](https://fetch.ai/)

## 📌 Overview

**TradFi & Global Stock Market Agent** is a specialized autonomous AI sub-agent built on Fetch.ai's `uAgents` framework. It provides real-time equity market metrics, treasury yield curve dynamics, macro fiat liquidity data, and market sentiment indicators to master orchestrators such as **World Money Map Orchestrator (`@prime-money-oracle`)**.

### Tracked Market Domains:
1. **Global Equity Indices**: S&P 500, Nasdaq 100, Dow Jones, Nikkei 225, DAX 40, FTSE 100, CSI 300.
2. **Sovereign Debt & Yield Curves**: US 10-Year, 2-Year, 3-Month Yields, 10Y-2Y Spread Inversion, Fed Funds Rate.
3. **Macro Fiat Liquidity**: US Dollar Index (DXY), Fed Balance Sheet (QT/QE), US M2 Money Supply, Reverse Repo (RRP).
4. **Volatility & Sentiment**: CBOE VIX (Equity Risk), ICE MOVE Index (Treasury Risk), CNN Fear & Greed Index, High Yield Credit Spreads.
5. **Sector Rotation**: Institutional flows across AI/Tech, Energy, Financials, and Defensive vs. Cyclical sectors.

---

## 🔒 Security & Privacy Notice

- **Secret Seed Protection**: The agent loads its seed via `TRADFI_AGENT_SEED` or `AGENT_SEED`. All startup handlers and internal logging modules sanitize seed variables, preventing sensitive keys from ever being exposed in logs or console outputs.

---

## ⚙️ Environment Variables Setup

Create a `.env` file in the root directory:

```env
TRADFI_AGENT_SEED="your_custom_secure_seed_for_tradfi_agent_here"
```
📦 Installation & Execution
1. Install Dependencies
```
pip install uagents requests
```
2. Run the TradFi Agent
```
python tradfi_stock_agent.py
```
💬 Communication Protocol
Query Request (TradFiDataQueryRequest)
```
{
  "scope": "ALL_MARKETS"
}
```
Response Schema (TradFiDataQueryResponse)
```
{
  "agent_version": "1.0.0",
  "timestamp": 1722880000.0,
  "global_indices": {
    "S&P500": { "value": 5450.25, "change_24h_percent": 0.35 },
    "NASDAQ_100": { "value": 19250.8, "change_24h_percent": 0.52 },
    "NIKKEI_225": { "value": 38200.0, "change_24h_percent": 0.85 }
  },
  "bond_yields_rates": {
    "US_10Y_YIELD": "4.18%",
    "US_02Y_YIELD": "4.32%",
    "YIELD_CURVE_SPREAD_10Y_2Y": "-0.14%"
  },
  "macro_liquidity": {
    "DXY_DOLLAR_INDEX": 104.15,
    "FED_BALANCE_SHEET_USD": "$7.22T",
    "US_M2_MONEY_SUPPLY_USD": "$21.0T"
  },
  "volatility_sentiment": {
    "VIX_EQUITY_VOLATILITY": 15.4,
    "MOVE_INDEX_BOND_VOLATILITY": 98.5
  },
  "reasoning_summary": "TradFi intelligence compiled..."
}
```
⚖️ Disclaimer
NOT FINANCIAL ADVICE. This software is generated automatically for informational, research, and analytical purposes only. It does not constitute investment, legal, or tax advice.
```
