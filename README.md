# Pilot

**Autonomous Multi-Asset AI Trading Agent**

> *This repository contains documentation and architecture only. The codebase is proprietary — contact me for inquiries.*

---

## Overview

Pilot is a fully autonomous AI-driven trading agent built for personal use, with plans for commercial licensing. It operates across four asset classes — stocks, forex, commodities, and cryptocurrency — using a multi-timeframe strategy with integrated machine learning components and live brokerage execution.

The goal was not just to build something that trades. It was to build something that makes decisions reliably, fails gracefully, and behaves consistently under conditions it hasn't seen before. Those constraints turned Pilot into as much an AI engineering problem as a finance one.

---

## What Makes This an AI Agent

Pilot is not a rules-based script. It is an autonomous decision-making system that:

- **Perceives** — ingests live market data across multiple timeframes simultaneously
- **Reasons** — applies ML models trained on historical patterns to generate probabilistic signals
- **Acts** — executes trades with no human in the loop during operation
- **Self-manages** — adjusts position sizing, applies trailing stops, and locks in break-even automatically based on real-time conditions
- **Reports** — generates performance summaries and emails them autonomously

This architecture raised a core challenge in AI agent design: how do you give a system enough autonomy to be useful while preserving enough control to be safe?

---

## Architecture

```
┌─────────────────────────────────────────────────────┐
│                    PILOT CORE                        │
│                                                      │
│  ┌─────────────┐    ┌──────────────┐                 │
│  │ Data Engine │───▶│ ML Pipeline  │                 │
│  │             │    │              │                 │
│  │ · Live feed │    │ · AR models  │                 │
│  │ · Multi-TF  │    │ · Monte Carlo│                 │
│  │ · OHLCV     │    │ · Signal gen │                 │
│  └─────────────┘    └──────┬───────┘                 │
│                            │                         │
│                    ┌───────▼───────┐                 │
│                    │ Risk Manager  │                  │
│                    │               │                 │
│                    │ · Position sz │                 │
│                    │ · Sharpe/      │                │
│                    │   Sortino/     │                │
│                    │   Calmar       │                │
│                    │ · Trailing SL  │                │
│                    │ · Break-even   │                │
│                    └───────┬───────┘                 │
│                            │                         │
│                    ┌───────▼───────┐                 │
│                    │ IBKR Executor │                  │
│                    │  (ib_insync)  │                  │
│                    └───────────────┘                 │
└─────────────────────────────────────────────────────┘
          │                           │
  ┌───────▼──────┐           ┌────────▼──────┐
  │  Flask       │           │  Email        │
  │  Dashboard   │           │  Reporter     │
  │  (Auth'd)    │           │               │
  └──────────────┘           └───────────────┘
```

---

## Key Components

### ML Pipeline
- **Autoregressive (AR) forecasting** — trained on historical price data to generate short-horizon predictions across timeframes
- **Monte Carlo simulation** — used in the Chart Predictor module to model outcome distributions rather than single-point predictions
- **Multi-timeframe signal fusion** — signals from different timeframes are weighted and combined before execution decisions are made

### Risk Management
Pilot implements institutional-grade risk metrics:
- **Sharpe Ratio** — risk-adjusted return measurement
- **Sortino Ratio** — downside deviation focus
- **Calmar Ratio** — return vs. maximum drawdown
- **Trailing stop logic** — dynamic stop-loss that follows price movement
- **Break-even lock** — automatically moves stop to entry price once a threshold profit is reached

### Execution Layer
- Live brokerage integration via **Interactive Brokers** (`ib_insync`)
- Order management with real-time position tracking
- Win/loss counters and trade logging

### Dashboard
- Flask-based web interface with authentication
- Real-time performance metrics
- Chart Predictor tab for AR forecast visualization
- Accessible remotely via Cloudflare tunnel

---

## Design Decisions & What I Learned

These are the decisions that shaped Pilot as an AI engineering project, not just a trading one.

**1. Autonomy has to be earned incrementally**
I didn't deploy Pilot with full autonomy on day one. I built paper trading first, studied failure modes, tightened the risk manager, then moved to live execution. The process taught me that AI agents need staged autonomy — each level of independence should be validated before expanding.

**2. The hardest problem was failure modes, not performance**
Getting Pilot to make profitable decisions was easier than getting it to handle conditions it wasn't trained on. Market gaps, data feed interruptions, and unexpected volatility required explicit handling — the model had no concept of these edge cases on its own. Building that robustness was the most valuable engineering work in the project.

**3. Risk management is a constraint layer, not an afterthought**
Early versions of Pilot had risk logic bolted on after the signal generation. This was wrong. The risk manager now sits between signal generation and execution as a hard gate — no signal reaches IBKR without clearing risk checks. This architectural change came from watching unconstrained outputs cause problems in testing.

**4. Explainability matters even in production**
The dashboard exists not just for monitoring but for understanding. When Pilot makes a decision I don't expect, I need to be able to trace why. Building that observability forced me to think carefully about what the agent was actually doing versus what I assumed it was doing.

**5. Statefulness is complex in autonomous agents**
Pilot has to maintain state across sessions — open positions, risk parameters, performance history. Managing that state reliably, especially across interruptions and reconnections, was a significant engineering challenge that had nothing to do with ML and everything to do with building a trustworthy agent.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Language | Python |
| ML / Forecasting | scikit-learn, NumPy, pandas |
| Brokerage Integration | ib_insync (Interactive Brokers) |
| Dashboard | Flask, HTML/CSS/JS |
| Remote Access | Cloudflare Tunnel |
| Reporting | SMTP email automation |

---

## Current Status

Pilot is operational for live trading. Active development continues on:
- Expanding asset class coverage
- Improving AR model accuracy across volatile market conditions
- Refining the risk parameter calibration framework
- Documentation for eventual commercial licensing

---

## About This Project

Pilot was built entirely by me as an independent project, with AI assistance (Claude, Anthropic) used as a development collaborator throughout. The experience of building an autonomous agent with AI assistance, then studying how that agent makes decisions, directly shaped my interest in AI safety and reliable agent design.

This is the first project in what I intend to be a public body of work around intelligent systems.

---

## Contact

Interested in Pilot, collaborating, or discussing AI agent design?

📧 smithenaz@gmail.com
🐙 github.com/TheNyansafo
