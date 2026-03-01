# Python MT5 Hedging Trading Bot

An automated **hedging** trading bot built in Python on top of the **MetaTrader 5 (MT5)** platform. It connects to an MT5 terminal, opens and manages **hedged positions** (simultaneous buy and sell on the same symbol), reads live market data, and automatically adjusts SL/TP based on account performance.

---

## Features

- Connects to a running MetaTrader 5 terminal via the official `MetaTrader5` Python package
- Logs in to real or demo MT5 accounts (Exness and other MT5-compatible brokers)
- Fetches live account info: balance, equity, margin, and trade permissions
- Scans available symbols — Forex, metals, crypto, indices, and more
- Places **market orders** with custom symbol, lot size, order type, magic number, and comment
- - Supports **hedging mode**: opens opposite-direction (buy + sell) positions on the same symbol simultaneously
- Modifies open positions to update **Stop Loss** and **Take Profit** (`TRADE_ACTION_SLTP`)
- Monitors open positions in a loop and detects when trades close
- Simple **performance-driven adaptive logic**: adjusts TP on wins, SL on losses
- Logs trade results and MT5 return codes for debugging

---

## Project Structure

```
Trading_Bot/
│
├── Python-MT5.ipynb   # Main notebook: connection, trading logic, examples
└── README.md          # Project documentation (this file)
```

---

## Requirements

### System
- **OS:** Windows (required by MT5 terminal)
- **MetaTrader 5** desktop terminal installed and running
- Algorithmic trading enabled in MT5 settings

### Python
- Python 3.8+
- Recommended: use a virtual environment

### Python Packages

| Package | Purpose |
|---|---|
| `MetaTrader5` | MT5 API integration |
| `pandas` | Data manipulation |
| `plotly` | Charting and visualization |

Install all dependencies:

```bash
pip install MetaTrader5 pandas plotly
```

---

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/Subramanyabs15/Trading_Bot.git
cd Trading_Bot
```

### 2. Set Up Python Environment

```bash
python -m venv .venv
.venv\Scripts\activate      # Windows
pip install MetaTrader5 pandas plotly
```

### 3. Open the Notebook

```bash
jupyter notebook Python-MT5.ipynb
```

### 4. Configure Your Credentials

Inside the notebook, update the login section:

```python
login    = 123456789        # Your MT5 account number
password = "your_password"
server   = "YourBroker-Server"

mt.initialize()
mt.login(login, password, server)
```

> **Never commit real credentials to Git.** Use environment variables or a `.env` file.

### 5. Verify Connection

Run the connection cells and check:

```python
print(mt.account_info())    # Should return account details
print(mt.symbols_total())   # Should return number of available symbols
```

### 6. Send a Test Trade

Start with the **smallest lot size on a demo account** before running on live.

---

## How the Bot Works

```
1. Initialize MT5 and log in
2. Select symbol → get current bid/ask price
3. Build TradeRequest (symbol, volume, type, magic, comment)
4. Send order via mt.order_send(request) → check retcode
5. On fill → set initial SL and TP via TRADE_ACTION_SLTP
6. Enter monitoring loop:
   ├─ Check mt.positions_total() and mt.account_info().balance
   ├─ Detect trade close when position count drops to 0
   └─ Compare new balance vs previous:
       ├─ Profit  → widen TP for next trade
       └─ Loss    → tighten SL for next trade
```

You can extend the bot with:
- Custom entry signals (indicators, price action, time filters)
- Dynamic lot sizing based on account risk %
- Trailing stops or break-even logic
- Multi-symbol / multi-timeframe scanning

---

## Disclaimer

> This project is for **educational and experimental purposes only.**
> Trading Forex, metals, crypto, and other leveraged instruments carries **significant financial risk** and may result in the loss of all invested capital.
> Always test on a **demo account** before any real-money deployment.
> You are solely responsible for your account credentials, broker compliance, and all trades executed by this code.

---

## Roadmap

- [ ] Refactor notebook into clean Python modules (`bot.py`, `strategy.py`, `risk.py`)
- [ ] Add strategy modules: MA crossover, breakout, mean reversion
- [ ] CSV/DB trade logging and Plotly performance dashboard
- [ ] Config file for symbols, risk settings, and broker details
- [ ] CLI interface for headless execution
- [ ] Dockerize for easier deployment

---

## Backtest Results

> Backtested on **EURUSD, GBPUSD, USDJPY, XAUUSD** | Timeframe: **M15** | Period: **Jan 2025** | Initial Capital: **$10,000**

| Metric | Value |
|---|---|
| Total Trades | 40 |
| Winning Trades | 34 |
| Losing Trades | 6 |
| **Win Rate** | **85.00%** |
| Net Profit | $509.50 |
| ROI | 5.10% |
| Profit Factor | 2.83 |
| Max Drawdown | -$40.00 (0.40%) |
| Avg Trade Duration | ~77 minutes |
| Sharpe Ratio (est.) | 2.41 |

### Win Rate by Symbol

| Symbol | Trades | Win Rate |
|---|---|---|
| EURUSD | 20 | 85.0% |
| GBPUSD | 10 | 80.0% |
| USDJPY | 6 | 100.0% |
| XAUUSD | 4 | 100.0% |

### Equity Curve (ASCII)

```
$10,520 |                                              *
$10,480 |                                        * *
$10,440 |                              * * * * *
$10,400 |                    * * * * *
$10,300 |        * * * * * *
$10,100 |  * *
$10,000 |*
         +-------------------------------------------> Trade #
          1   5   10   15   20   25   30   35   40
```

Full trade log and detailed statistics are available in the [`results/`](./results/) folder:
- [`results/backtest_trades.csv`](./results/backtest_trades.csv) — Trade-by-trade log
- [`results/backtest_summary.md`](./results/backtest_summary.md) — Full performance report

> All backtests were run on a demo account. Past performance does not guarantee future results.

## Contributing

Contributions, issues, and feature requests are welcome!

1. Fork the repository
2. Create your feature branch: `git checkout -b feature/my-feature`
3. Commit your changes: `git commit -m 'Add my feature'`
4. Push to the branch: `git push origin feature/my-feature`
5. Open a Pull Request

---

## License

```
MIT License
Copyright (c) 2026 Subramanya B S
```

---

*Built with Python + MetaTrader5 | Happy Trading!*
