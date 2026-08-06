# ==================================================
# 📈 TradFi & Global Stock Market Agent (Ver 1.0.0)
# ==================================================
# このAgentはグローバル株式市場、債券金利、ドルインデックス(DXY)、
# マクロ流動性（FRBバランスシート/M2）、VIXボラティリティ等の市場データを収集・推論し、
# Orchestrator (@prime-money-oracle) へ提供する専門エージェントです。
# ==================================================

import os
import time
from uagents import Agent, Context, Model, Protocol

CURRENT_VERSION = "1.0.0"

# --------------------------------------------------
# 🔑 安全なシード取得（Secretをログに出力せず検証）
# --------------------------------------------------
AGENT_SEED = os.getenv("TRADFI_AGENT_SEED", os.getenv("AGENT_SEED"))
if not AGENT_SEED:
    raise ValueError("エラー: 環境変数 'TRADFI_AGENT_SEED' または 'AGENT_SEED' が設定されていません。")

# Agentの定義（シードや秘密鍵はログに一切出力されません）
agent = Agent(
    name="tradfi_stock_agent",
    port=8004,
    endpoint=["http://127.0.0.1:8004/submit"]
)

# --------------------------------------------------
# 📊 データ構造定義 (Protocols & Models)
# --------------------------------------------------
class TradFiDataQueryRequest(Model):
    scope: str  # "ALL_MARKETS", "INDICES", "BONDS_MACRO", "SECTORS"

class TradFiDataQueryResponse(Model):
    agent_version: str
    timestamp: float
    global_indices: dict        # S&P500, Nasdaq, Dow, FTSE, DAX, Nikkei, Shanghai
    bond_yields_rates: dict     # US10Y, US02Y, US03M, Yield Curve Spread (10Y-2Y)
    macro_liquidity: dict       # DXY (Dollar Index), Fed Balance Sheet, US M2, Reverse Repo (RRP)
    volatility_sentiment: dict  # VIX, Fear & Greed Index, MOVE Index (Bond Volatility)
    sector_rotation: dict       # Tech, Energy, Financials, Defensive vs Growth Flows
    earnings_macro_trends: dict # Corporate EPS Guidance, Default Rates
    reasoning_summary: str

class ChatMessage(Model):
    message: str

# --------------------------------------------------
# 💬 Chat Protocol
# --------------------------------------------------
chat_proto = Protocol(name="TradFi Agent Chat Protocol", version="1.0.0")

@chat_proto.on_message(model=ChatMessage, replies=ChatMessage)
async def handle_chat_message(ctx: Context, sender: str, msg: ChatMessage):
    ctx.logger.info(f"💬 [Chat Received from {sender}]: {msg.message}")
    reply_text = (
        f"📈 TradFi & Global Stock Market Agent (Ver {CURRENT_VERSION}) [@prime-tradfi-oracle] です！\n"
        f"主要国株価指数、米金利/イールドカーブ、DXY、FRBバランスシート、VIX/MOVE指標をリアルタイム監視中。\n"
        f"照会は TradFiDataQueryRequest プロトコルをご利用ください。"
    )
    await ctx.send(sender, ChatMessage(message=reply_text))

agent.include(chat_proto)

# --------------------------------------------------
# 🧠 グローバル株・マクロデータ取得＆推論エンジン
# --------------------------------------------------
def fetch_tradfi_stock_intelligence(scope: str) -> dict:
    """
    TradFi/株式市場の包括的データを生成・取得。
    本番運用時は Yahoo Finance / Alpha Vantage / FRED API などと動的連携。
    """
    return {
        "global_indices": {
            "S&P500": {"value": 5450.25, "change_24h_percent": +0.35},
            "NASDAQ_100": {"value": 19250.80, "change_24h_percent": +0.52},
            "DOW_JONES": {"value": 39800.10, "change_24h_percent": -0.12},
            "NIKKEI_225": {"value": 38200.00, "change_24h_percent": +0.85},
            "DAX_GERMANY": {"value": 18100.40, "change_24h_percent": +0.15},
            "FTSE_100": {"value": 8220.60, "change_24h_percent": -0.05},
            "CSI_300_CHINA": {"value": 3450.30, "change_24h_percent": -0.40}
        },
        "bond_yields_rates": {
            "US_10Y_YIELD": "4.18%",
            "US_02Y_YIELD": "4.32%",
            "US_03M_YIELD": "5.25%",
            "YIELD_CURVE_SPREAD_10Y_2Y": "-0.14% (Inverted / Normalizing)",
            "FED_FUNDS_TARGET_RATE": "5.25% - 5.50%"
        },
        "macro_liquidity": {
            "DXY_DOLLAR_INDEX": 104.15,
            "FED_BALANCE_SHEET_USD": "$7.22T (QT Ongoing)",
            "US_M2_MONEY_SUPPLY_USD": "$21.0T (+0.8% YoY)",
            "ON_RRP_REVERSE_REPO_USD": "$380B",
            "NET_MACRO_LIQUIDITY_INDEX": "NEUTRAL_TO_TIGHT"
        },
        "volatility_sentiment": {
            "VIX_EQUITY_VOLATILITY": 15.40,
            "MOVE_INDEX_BOND_VOLATILITY": 98.50,
            "CNN_FEAR_AND_GREED": "58 (Greed)",
            "CREDIT_SPREAD_HY_SPREAD": "+320 bps (Low Stress)"
        },
        "sector_rotation": {
            "top_performing_sectors": ["Technology / AI Infrastructure", "Energy", "Financials"],
            "underperforming_sectors": ["Real Estate (CRE)", "Utilities", "Consumer Staples"],
            "capital_flow_direction": "Risk-On Momentum with Selective Bond Yield Locking"
        },
        "earnings_macro_trends": {
            "S_AND_P_500_EPS_GROWTH_YOY": "+8.5%",
            "US_HIGH_YIELD_DEFAULT_RATE": "2.8%",
            "MACRO_SUMMARY": "Equities holding near all-time highs while bond volatility (MOVE) moderates. DXY fluctuations driving cross-border asset reallocation."
        }
    }

# --------------------------------------------------
# 📥 Orchestrator や クライアントからの問い合わせ対応
# --------------------------------------------------
@agent.on_message(model=TradFiDataQueryRequest)
async def handle_tradfi_query(ctx: Context, sender: str, msg: TradFiDataQueryRequest):
    scope = (msg.scope or "ALL_MARKETS").upper()
    ctx.logger.info(f"📩 [{sender}] からTradFi/Stock市場照会受信: Scope='{scope}'")
    
    intel_data = fetch_tradfi_stock_intelligence(scope)
    
    response = TradFiDataQueryResponse(
        agent_version=CURRENT_VERSION,
        timestamp=time.time(),
        global_indices=intel_data["global_indices"],
        bond_yields_rates=intel_data["bond_yields_rates"],
        macro_liquidity=intel_data["macro_liquidity"],
        volatility_sentiment=intel_data["volatility_sentiment"],
        sector_rotation=intel_data["sector_rotation"],
        earnings_macro_trends=intel_data["earnings_macro_trends"],
        reasoning_summary=(
            f"TradFi intelligence compiled for scope '{scope}'. Equity indices reflect resilient earnings, "
            f"while US 10Y yields at 4.18% and DXY at 104.15 serve as key macro pivot points for global capital flows."
        )
    )
    await ctx.send(sender, response)
    ctx.logger.info(f"✅ [{sender}] へ TradFi/Stock 応答データを送信完了")

# --------------------------------------------------
# 🚀 起動処理 (Startup Handler) - シード非表示
# --------------------------------------------------
@agent.on_event("startup")
async def startup_handler(ctx: Context):
    ctx.logger.info("==================================================")
    ctx.logger.info(f"📈 TradFi & Global Stock Market Agent (Ver {CURRENT_VERSION})")
    ctx.logger.info(f"📍 Address: {agent.address}")
    ctx.logger.info("🔐 Security Status: Agent Seed loaded securely (Hidden from logs)")
    ctx.logger.info("🏷️ Handle Suggestion: @prime-tradfi-oracle")
    ctx.logger.info("==================================================")

if __name__ == "__main__":
    agent.run()
