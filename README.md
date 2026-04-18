# ORB-strategy-
A research-grade Python backtesting engine for the Opening Range Breakout (ORB) intraday trading strategy, with ATR-based sizing, volume filters, and full performance tearsheets.
ORB Strategy — Backtesting Engine
Opening Range Breakout · Python · Systematic Intraday Research
A clean, research-grade backtesting engine for the Opening Range Breakout strategy —
built for precision, transparency, and reproducibility.
</div>

Overview
The Opening Range Breakout (ORB) is one of the most studied intraday price patterns. This engine provides a rigorous, configurable framework to research, test, and evaluate ORB setups on historical 1-minute OHLCV data.
What this repo focuses on:

Faithful simulation of intraday ORB mechanics
Realistic trade execution with commission and slippage modelling
Detailed performance analytics and tearsheet generation
Clean, well-documented Python code designed for extension


⚠️ Disclaimer: This project is intended for educational and research purposes only. Past performance of any backtest does not guarantee future results. Nothing here constitutes financial advice.


How the Strategy Works
The ORB strategy defines an opening range — the high and low of the first N minutes after market open. A trade is triggered when price breaks decisively beyond this range, confirmed by volume.
09:30 ──────────────────────────────── Market Open
          │
09:30–09:45  →  Record Opening Range High & Low
          │
09:45+       →  Monitor for breakout
          │
          ├─ Close > OR High + buffer  →  Enter LONG
          └─ Close < OR Low  − buffer  →  Enter SHORT
                    │
                    ├─ Stop Loss    :  ATR-based or OR midpoint
                    ├─ Take Profit  :  Fixed R:R multiple
                    └─ EOD Exit     :  Force-close at 15:30

Project Structure
orb-strategy/
│
├── data/
│   ├── raw/                    # Raw 1-min OHLCV CSVs (git-ignored)
│   └── processed/              # Session-split, cleaned data
│
├── strategy/
│   ├── orb.py                  # Opening range detection & signal logic
│   ├── filters.py              # Volume, gap & regime filters
│   └── sizing.py               # ATR-based position sizing
│
├── backtest/
│   ├── engine.py               # Core backtesting engine
│   ├── metrics.py              # Sharpe, drawdown, win rate, etc.
│   └── report.py               # Tearsheet & plot generation
│
├── notebooks/
│   ├── 01_data_exploration.ipynb
│   ├── 02_backtest_walkthrough.ipynb
│   └── 03_parameter_sensitivity.ipynb
│
├── config.yaml                 # All strategy & backtest parameters
├── run_backtest.py             # Main entry point
├── requirements.txt
└── README.md

Quickstart
1. Clone & Install
bashgit clone https://github.com/YOUR_USERNAME/orb-strategy.git
cd orb-strategy

python -m venv venv
source venv/bin/activate          # Windows: venv\Scripts\activate

pip install -r requirements.txt
2. Prepare Data
Place your 1-minute OHLCV CSV files in data/raw/. Expected format:
datetime,open,high,low,close,volume
2024-01-02 09:30:00,475.20,475.85,474.90,475.60,1234567
2024-01-02 09:31:00,475.60,476.10,475.45,475.95,987432
...
3. Configure
Edit config.yaml to match your research setup:
yamlstrategy:
  ticker: "SPY"
  or_minutes: 15              # Opening range window (5, 15, 30, 60)
  breakout_buffer: 0.0005     # 0.05% buffer above/below OR
  risk_reward: 2.0            # Target R:R ratio
  volume_multiplier: 1.5      # Required volume vs. session average
  eod_exit_time: "15:30"      # Force-close time (exchange local)

backtest:
  start_date: "2022-01-01"
  end_date:   "2024-12-31"
  initial_capital: 100000
  risk_per_trade_pct: 0.01    # 1% account risk per trade
  commission_pct: 0.001       # 0.1% per side
  slippage_pct: 0.0002        # Estimated slippage
4. Run
bashpython run_backtest.py
Results and tearsheet charts are saved to backtest/output/.

Parameters
ParameterDefaultDescriptionor_minutes15Duration of the opening range windowbreakout_buffer0.05%Price buffer to confirm a valid breakoutrisk_reward2.0Take-profit as a multiple of initial riskvolume_multiplier1.5Min volume vs. average at breakout bareod_exit_time15:30Hard cut-off — all positions closedrisk_per_trade_pct1%Capital at risk per trade for sizingcommission_pct0.1%Round-trip commission assumption

Performance Output
Running the engine produces a full performance tearsheet:
────────────────────────────────────────────
  ORB Strategy — Backtest Results
  SPY · 2022-01-01 to 2024-12-31 · 15-min OR
────────────────────────────────────────────
  Net Return          +34.7%
  Annualised Return   +10.8%
  Sharpe Ratio         1.42
  Max Drawdown        -8.3%
  Calmar Ratio         1.30
────────────────────────────────────────────
  Total Trades          412
  Win Rate             54.1%
  Profit Factor         1.68
  Avg Win / Avg Loss    1.87×
  Avg Hold Time        47 min
────────────────────────────────────────────

Figures above are illustrative placeholders — replace with your own results.

Charts generated automatically:

Equity curve with drawdown overlay
Monthly P&L heatmap
Trade duration distribution
Win/loss breakdown by day of week and session hour
