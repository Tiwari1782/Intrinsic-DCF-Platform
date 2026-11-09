# Intrinsic: Full-Stack DCF Valuation Platform

> A web-based valuation terminal. Type a stock ticker, get auto-fetched financials, an editable three-statement projection model, WACC, a DCF valuation, scenarios, sensitivity analysis, Monte Carlo simulation, reverse DCF, and Excel/PDF export.

**Stack:** Python 3.12 · FastAPI · PostgreSQL · Redis · React 18 · TypeScript · Vite · Tailwind CSS · Docker

**Status:** 🚧 In development (see [Roadmap](#14-roadmap--progress-tracker))

> ⚠️ **Disclaimer:** This is an educational project. Nothing it produces is investment advice. Valuation outputs depend entirely on the assumptions entered.

---

## Table of Contents

1. [What Is This Project?](#1-what-is-this-project)
2. [Why I Am Building It](#2-why-i-am-building-it)
3. [DCF Explained: Basic to Advanced](#3-dcf-explained-basic-to-advanced)
4. [Features](#4-features)
5. [Tech Stack](#5-tech-stack)
6. [System Architecture](#6-system-architecture)
7. [Finance Methodology and Formulas](#7-finance-methodology-and-formulas)
8. [Project Structure](#8-project-structure)
9. [API Design](#9-api-design)
10. [Database Schema](#10-database-schema)
11. [Frontend Design](#11-frontend-design)
12. [Getting Started](#12-getting-started)
13. [Testing and Quality](#13-testing-and-quality)
14. [Roadmap / Progress Tracker](#14-roadmap--progress-tracker)
15. [Git Workflow and Conventions](#15-git-workflow-and-conventions)
16. [Deployment](#16-deployment)
17. [Limitations and Assumptions](#17-limitations-and-assumptions)
18. [Interview Cheat Sheet](#18-interview-cheat-sheet)
19. [Glossary](#19-glossary)
20. [Resources](#20-resources)
21. [License](#21-license)

---

## 1. What Is This Project?

**Intrinsic** is a full-stack application that estimates what a company is *actually worth* (its **intrinsic value**) using a **Discounted Cash Flow (DCF)** model, then compares that value with the current market price to say whether the stock looks undervalued, fairly valued, or overvalued.

**The user flow:**

1. User searches a ticker (e.g. `INFY.NS`, `TCS.NS`, `AAPL`).
2. The backend fetches 4-5 years of historical income statement, balance sheet, and cash flow data.
3. The app analyses historical trends (growth, margins, working capital, capex).
4. The user adjusts assumptions in an interactive panel (growth, margins, WACC inputs, terminal growth).
5. The DCF engine recalculates instantly and shows enterprise value, equity value, and value per share.
6. Advanced modules (scenarios, sensitivity, Monte Carlo, reverse DCF, comps) stress-test the result.
7. The user saves the model and exports it to Excel (with live formulas) or PDF.

Think of it as a **mini professional valuation terminal**, not a spreadsheet.

---

## 2. Why I Am Building It

- **Resume value:** A finance project built by a CS student, with real engineering behind it, stands out from template-based Excel DCFs.
- **Learning goals:**
  - Understand corporate finance fundamentals (FCF, WACC, terminal value).
  - Build a production-style backend (FastAPI, database, caching, tests, CI).
  - Build a heavy, data-rich frontend (React + TypeScript, charts, editable grids).
  - Practise numerical computing with numpy/pandas/SciPy.
- **Target roles:** fintech, financial analytics, equity research support, quant-adjacent, and data roles at finance firms, plus software roles where full-stack skill matters.

---

## 3. DCF Explained: Basic to Advanced

### 3.1 The core idea
A company is worth the cash it will generate in the future, but money in the future is worth less than money today, so future cash is **discounted** back to the present.

### 3.2 Basic DCF (5 steps)
1. **Project Free Cash Flow (FCF)** for the next 5-10 years.
2. **Estimate Terminal Value (TV):** the value of all cash flows beyond the forecast period.
3. **Discount** every cash flow and the TV to today using **WACC**.
4. **Sum** them to get **Enterprise Value (EV)**.
5. **Bridge to equity:** subtract net debt (and other claims), divide by shares outstanding to get **intrinsic value per share**. Compare with market price.

### 3.3 Intermediate DCF
- Three linked financial statements (income statement, balance sheet, cash flow statement) that balance.
- WACC built from **CAPM** (risk-free rate, beta, equity risk premium) and cost of debt, weighted by capital structure.
- Terminal value computed two ways (**Gordon Growth** and **Exit Multiple**) and cross-checked.
- Mid-year discounting convention option.

### 3.4 Advanced DCF
- **Scenario analysis** (Bull / Base / Bear).
- **Sensitivity analysis** (two-variable tables and heatmaps; tornado charts).
- **Monte Carlo simulation** (assumptions as probability distributions, output is a distribution of values).
- **Reverse DCF** (what does the current price imply about growth or margins?).
- **Relative valuation cross-check** (comparable company multiples, football field chart).

---

## 4. Features

### Tier 1: Core (a real, working DCF)
- [ ] Ticker search and historical financial statement fetching (4-5 years)
- [ ] Historical ratio analysis: revenue growth, margins, tax rate, D&A %, capex %, working capital days
- [ ] Projection engine: revenue, EBIT, taxes, D&A, capex, change in NWC, to FCF
- [ ] WACC calculator (CAPM + cost of debt + capital structure weights)
- [ ] Terminal value: Gordon Growth and Exit Multiple
- [ ] Output: EV, equity value, value per share, upside/downside vs. market price

### Tier 2: Advanced (makes it impressive)
- [ ] Three-statement linking (balance sheet balances, cash ties out)
- [ ] Scenario manager: Bull / Base / Bear switcher
- [ ] Sensitivity heatmaps: WACC vs. terminal growth, WACC vs. exit multiple
- [ ] Tornado chart showing which assumptions move value the most
- [ ] Excel export with **live formulas** (auditable by finance recruiters)
- [ ] User accounts (JWT) and saved models
- [ ] Input validation and sanity warnings (e.g. terminal growth above long-run GDP growth)

### Tier 3: Cherry on top (differentiators)
- [ ] **Monte Carlo simulation:** 10,000+ trials, vectorized with numpy; histogram and probability of undervaluation
- [ ] **Reverse DCF:** solve for the growth rate or margin implied by the current market price
- [ ] **Comparable company analysis:** peer P/E, EV/EBITDA, plus a **football field chart**
- [ ] **Beta from scratch:** regress stock returns against the Nifty 50 (or S&P 500)
- [ ] **AI valuation memo:** Claude API turns model outputs into a one-page analyst-style summary with risks
- [ ] **Backtesting:** compare historical DCF outputs with subsequent price moves
- [ ] **PDF report generator** with charts, assumptions, and disclaimers

> **Rule:** finish Tier 1, then Tier 2, then pick 2-3 items from Tier 3. A polished subset beats a half-built everything.

---

## 5. Tech Stack

| Layer | Technology | Purpose |
|---|---|---|
| Backend framework | **FastAPI** | REST API, automatic OpenAPI docs |
| Language | **Python 3.12** | Core language |
| Validation | **Pydantic v2** | Request/response schemas |
| Numerics | **pandas, numpy, SciPy** | Projections, simulation, root-finding, regression |
| Market data | **yfinance** (primary), FMP / Alpha Vantage (fallback) | Financial statements, prices |
| Database | **PostgreSQL** + SQLAlchemy 2 + Alembic | Users, saved models, cached filings (SQLite for early dev) |
| Cache / jobs | **Redis**; Celery or FastAPI BackgroundTasks | API caching, heavy simulations |
| Auth | **JWT** (python-jose / PyJWT), passlib | Accounts and saved models |
| Frontend | **React 18 + TypeScript + Vite** | UI |
| Styling | **Tailwind CSS** | Design system |
| State | **TanStack Query** (server state), **Zustand** (UI state) | Data fetching and local state |
| Forms | **React Hook Form + Zod** | Typed, validated inputs |
| Charts | **Apache ECharts** or Recharts | Heatmaps, histograms, tornado, football field |
| Tables | **AG Grid** | Editable financial statement grids |
| Export | **openpyxl**, **ReportLab / WeasyPrint** | Excel (live formulas) and PDF |
| Quality | pytest, Ruff, mypy, Vitest, ESLint, Prettier | Testing and linting |
| DevOps | Docker, docker-compose, GitHub Actions | Reproducible runs and CI |

---

## 6. System Architecture

```
┌──────────────────────────┐        HTTPS / JSON        ┌───────────────────────────────┐
│   React + TypeScript     │ ─────────────────────────► │        FastAPI backend        │
│   (Vite, Tailwind)       │ ◄───────────────────────── │                               │
│                          │                            │  api/      → routes           │
│  • Assumptions panel     │                            │  services/ → finance logic    │
│  • Statement grids       │                            │  schemas/  → Pydantic         │
│  • Charts & dashboards   │                            │  models/   → SQLAlchemy       │
└──────────────────────────┘                            └──────┬─────────┬──────────┬───┘
                                                               │         │          │
                                                    ┌──────────▼──┐ ┌────▼─────┐ ┌──▼──────────────┐
                                                    │ PostgreSQL  │ │  Redis   │ │ External data   │
                                                    │ users,      │ │ cache,   │ │ yfinance / FMP  │
                                                    │ saved models│ │ job state│ │ Claude API      │
                                                    └─────────────┘ └──────────┘ └─────────────────┘
```

**Design principles**
- **Finance logic lives only in `services/`.** Routes stay thin; the engine is pure, testable Python with no web dependencies.
- **Stateless computation:** the DCF endpoint takes assumptions plus historical data and returns results, so the frontend can recalculate live.
- **Cache external data:** financial statements are cached in Redis/DB to avoid repeated calls and rate limits.
- **Everything typed:** Pydantic on the backend, TypeScript and Zod on the frontend.

---

## 7. Finance Methodology and Formulas

> All formulas used in the engine are documented here so the model can be audited.

### 7.1 Free Cash Flow to Firm (FCFF)
```
NOPAT = EBIT × (1 − tax rate)
FCFF  = NOPAT + D&A − CapEx − ΔNWC
```
- **EBIT:** operating profit.
- **ΔNWC:** change in net working capital (an increase uses cash).

### 7.2 Revenue and cost projections
- Revenue: `Rev_t = Rev_(t−1) × (1 + g_t)`, where growth `g_t` can fade linearly from near-term growth to terminal growth.
- Costs: modelled as % of revenue (COGS, SG&A), giving EBITDA/EBIT margins.
- D&A, CapEx: % of revenue (or tied to PP&E).
- NWC: via days sales outstanding, days inventory, and days payable, or as % of revenue.

### 7.3 Cost of Equity (CAPM)
```
Ke = Rf + β × ERP
```
- **Rf:** risk-free rate (10-year government bond yield; for India, the 10Y G-Sec).
- **β (beta):** `β = Cov(R_stock, R_market) / Var(R_market)`, estimated by regression on historical returns.
- **ERP:** equity risk premium (reference: Damodaran's country risk premium tables).

### 7.3.1 Cost of Debt
```
Kd (after tax) = Kd × (1 − tax rate)
```
Estimated from interest expense / average debt, or a rating-based spread over Rf.

### 7.4 WACC
```
WACC = (E / (D + E)) × Ke + (D / (D + E)) × Kd × (1 − t)
```
- **E:** market capitalisation. **D:** total debt (market or book value).

### 7.5 Discounting
```
PV(FCFF_t) = FCFF_t / (1 + WACC)^t
```
Mid-year convention (optional): use `t − 0.5`.

### 7.6 Terminal Value
**Gordon Growth (perpetuity growth):**
```
TV = FCFF_n × (1 + g) / (WACC − g)
```
Requires `g < WACC`; `g` should be at or below long-run nominal GDP growth.

**Exit Multiple:**
```
TV = EBITDA_n × exit multiple
```
Cross-check: compute the **implied perpetual growth** from the exit multiple, and the **implied multiple** from the Gordon Growth TV.

### 7.7 Enterprise Value → Equity Value → Per Share
```
EV            = Σ PV(FCFF_t) + PV(TV)
Equity Value  = EV − Debt + Cash − Minority Interest − Preferred Stock
Value / share = Equity Value / Diluted Shares Outstanding
Upside (%)    = (Value per share / Market price) − 1
```
Also report **TV as % of EV** (a high share signals a model reliant on terminal assumptions).

### 7.8 Sensitivity Analysis
A grid recomputing value per share across two variables (WACC × terminal growth, and WACC × exit multiple). Rendered as a heatmap. The tornado chart flexes each input ± a fixed range and ranks them by impact.

### 7.9 Monte Carlo Simulation
- Draw N (default 10,000) samples of key assumptions from distributions (e.g. growth ~ Normal / Triangular, margin ~ Triangular, WACC ~ Normal, terminal growth ~ truncated Normal).
- Compute value per share per trial using **vectorized numpy**.
- Output: mean, median, percentiles (P5 / P25 / P75 / P95), histogram, and `P(value > market price)`.

### 7.10 Reverse DCF
Solve for the input (e.g. revenue growth) such that `DCF value = market price`, using a root-finder (`scipy.optimize.brentq`). Answers: *"What is the market already pricing in?"*

### 7.11 Comparable Company Analysis
Peer multiples (EV/EBITDA, P/E, EV/Sales); apply the median to the target's metrics to get a relative valuation range. Shown on a football field chart next to the DCF range.

### 7.12 Three-Statement Integrity Checks
- Assets = Liabilities + Equity in every projected year.
- Ending cash on the balance sheet = ending cash on the cash flow statement.
- Net income flows to retained earnings.

---

## 8. Project Structure

```
intrinsic-dcf-platform/
├── backend/
│   ├── app/
│   │   ├── api/                # Route handlers
│   │   │   ├── companies.py    # search, fetch financials
│   │   │   ├── valuation.py    # run DCF, sensitivity, monte carlo, reverse DCF
│   │   │   ├── models.py       # save/load user models
│   │   │   ├── auth.py         # register / login
│   │   │   └── export.py       # Excel / PDF
│   │   ├── core/               # config, security, dependencies
│   │   ├── services/           # PURE finance logic (no web code)
│   │   │   ├── data_fetcher.py
│   │   │   ├── ratios.py
│   │   │   ├── projections.py
│   │   │   ├── three_statement.py
│   │   │   ├── wacc.py
│   │   │   ├── beta.py
│   │   │   ├── dcf.py
│   │   │   ├── sensitivity.py
│   │   │   ├── monte_carlo.py
│   │   │   ├── reverse_dcf.py
│   │   │   ├── comps.py
│   │   │   ├── excel_export.py
│   │   │   └── memo.py         # AI memo (Claude API)
│   │   ├── models/             # SQLAlchemy ORM models
│   │   ├── schemas/            # Pydantic request/response schemas
│   │   ├── db/                 # session, migrations (Alembic)
│   │   └── main.py             # FastAPI app entry
│   ├── tests/
│   │   ├── test_dcf.py
│   │   ├── test_wacc.py
│   │   ├── test_projections.py
│   │   └── fixtures/           # known-good reference data
│   ├── requirements.txt
│   └── pyproject.toml
├── frontend/
│   ├── src/
│   │   ├── api/                # typed API client
│   │   ├── components/
│   │   │   ├── charts/         # heatmap, tornado, histogram, football field
│   │   │   ├── tables/         # AG Grid statement tables
│   │   │   ├── assumptions/    # input panels, sliders
│   │   │   └── layout/
│   │   ├── hooks/
│   │   ├── pages/              # Search, Model, Scenarios, Saved, Settings
│   │   ├── store/              # Zustand stores
│   │   ├── types/
│   │   └── main.tsx
│   ├── package.json
│   └── vite.config.ts
├── docs/
│   ├── methodology.md          # extended finance notes
│   ├── architecture.png
│   └── screenshots/
├── .github/workflows/ci.yml
├── docker-compose.yml
├── .env.example
├── .gitignore
├── LICENSE
└── README.md
```

---

## 9. API Design

Base URL (local): `http://localhost:8000/api/v1` · Interactive docs: `http://localhost:8000/docs`

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/companies/search?q=` | Search tickers by name/symbol |
| `GET` | `/companies/{ticker}/financials` | Historical IS / BS / CF statements |
| `GET` | `/companies/{ticker}/market` | Price, market cap, shares, beta |
| `GET` | `/companies/{ticker}/ratios` | Historical ratio analysis |
| `POST` | `/valuation/dcf` | Run DCF with supplied assumptions |
| `POST` | `/valuation/wacc` | Compute WACC from inputs |
| `POST` | `/valuation/sensitivity` | Sensitivity grid / tornado data |
| `POST` | `/valuation/monte-carlo` | Run simulation (may be async) |
| `POST` | `/valuation/reverse-dcf` | Solve for implied growth/margin |
| `GET` | `/valuation/comps/{ticker}` | Peer multiples and implied range |
| `POST` | `/valuation/memo` | Generate AI valuation memo |
| `POST` | `/auth/register` · `/auth/login` | Account creation, JWT login |
| `GET/POST/PUT/DELETE` | `/models` | Saved model CRUD |
| `GET` | `/export/{model_id}/excel` | Excel with live formulas |
| `GET` | `/export/{model_id}/pdf` | PDF report |

**Example request: `POST /valuation/dcf`**
```json
{
  "ticker": "INFY.NS",
  "forecast_years": 5,
  "assumptions": {
    "revenue_growth": [0.12, 0.11, 0.10, 0.09, 0.08],
    "ebit_margin": [0.21, 0.21, 0.22, 0.22, 0.22],
    "tax_rate": 0.25,
    "da_pct_revenue": 0.03,
    "capex_pct_revenue": 0.035,
    "nwc_pct_revenue": 0.12,
    "terminal_growth": 0.05,
    "exit_multiple": 18.0,
    "wacc": 0.115,
    "mid_year_convention": false
  }
}
```

**Example response (abridged)**
```json
{
  "enterprise_value": 6850000000000,
  "equity_value": 7100000000000,
  "value_per_share": 1710.4,
  "market_price": 1620.0,
  "upside_pct": 5.6,
  "terminal_value_pct_of_ev": 71.3,
  "projections": [ { "year": 2027, "revenue": 0, "fcff": 0, "pv": 0 } ],
  "warnings": []
}
```
*(Numbers are illustrative.)*

---

## 10. Database Schema

```
users
  id (PK) · email (unique) · password_hash · created_at

saved_models
  id (PK) · user_id (FK → users) · ticker · name
  assumptions (JSONB) · results (JSONB) · scenario ("bull" | "base" | "bear")
  created_at · updated_at

financial_cache
  id (PK) · ticker · statement_type · data (JSONB) · fetched_at

simulation_runs
  id (PK) · model_id (FK → saved_models) · params (JSONB)
  results (JSONB) · status · created_at
```

Migrations are managed with **Alembic**.

---

## 11. Frontend Design

**Pages**
- **Search:** ticker lookup with autocomplete and recent searches.
- **Company Overview:** price chart, key stats, historical ratio dashboard.
- **Model Workspace** (main screen):
  - Left: assumptions panel (sliders and numeric inputs, with live validation).
  - Centre: projected statements in editable AG Grid tables.
  - Right: valuation summary card (EV, equity value, per-share value, upside, TV % of EV).
- **Scenarios:** Bull/Base/Bear side-by-side comparison.
- **Sensitivity:** interactive heatmaps and the tornado chart.
- **Simulation:** Monte Carlo histogram with percentile markers.
- **Reverse DCF and Comps:** implied expectations and the football field chart.
- **Saved Models:** list, duplicate, delete, export.

**UX goals:** recalculation feels instant (debounced calls to the stateless DCF endpoint), warnings appear inline for unrealistic inputs, dark and light themes, fully typed API client, keyboard-friendly tables.

---

## 12. Getting Started

### Prerequisites
- Python 3.12+
- Node.js 20+ and npm (or pnpm)
- Docker and Docker Compose (recommended)
- Git

### Option A: Docker (recommended)
```bash
git clone https://github.com/<your-username>/intrinsic-dcf-platform.git
cd intrinsic-dcf-platform
cp .env.example .env
docker compose up --build
```
- Frontend: http://localhost:5173
- Backend API: http://localhost:8000
- API docs: http://localhost:8000/docs

### Option B: Manual setup

**Backend**
```bash
cd backend
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
alembic upgrade head
uvicorn app.main:app --reload --port 8000
```

**Frontend**
```bash
cd frontend
npm install
npm run dev
```

### Environment variables (`.env.example`)
```
# Backend
DATABASE_URL=postgresql+psycopg://user:password@localhost:5432/intrinsic
REDIS_URL=redis://localhost:6379/0
JWT_SECRET=change-me
JWT_ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=60

# Data providers (optional fallbacks)
FMP_API_KEY=
ALPHA_VANTAGE_API_KEY=

# AI memo (optional)
ANTHROPIC_API_KEY=

# Frontend
VITE_API_BASE_URL=http://localhost:8000/api/v1
```
Never commit `.env`. Keep it in `.gitignore`.

---

## 13. Testing and Quality

**Rule:** a DCF with silent formula errors is worse than no DCF. Every financial function gets unit tests.

- **Backend:** `pytest` with fixtures built from a **hand-built Excel model** of the same company; engine outputs must match Excel within tolerance.
- **Property checks:** balance sheet balances; WACC bounds; TV formula raises an error when `g ≥ WACC`.
- **Frontend:** Vitest and React Testing Library for components; type-checking via `tsc`.
- **Linting/formatting:** Ruff, mypy, ESLint, Prettier.
- **CI (GitHub Actions):** on every push, run lint, type-check, backend tests, and frontend tests/build.

```bash
# Backend
cd backend && pytest -q && ruff check . && mypy app

# Frontend
cd frontend && npm run lint && npm run test && npm run build
```

---

## 14. Roadmap / Progress Tracker

| Phase | Timeline | Goals | Status |
|---|---|---|---|
| 0. Learn | Week 1 | Damodaran lectures; FCF, WACC, TV basics; repo + FastAPI skeleton; data fetcher | ⬜ |
| 1. Engine | Week 2 | Ratios, projections, WACC, DCF in pure Python with unit tests; validate vs. Excel | ⬜ |
| 2. UI | Week 3 | React app, assumptions panel, results dashboard, charts wired to API | ⬜ |
| 3. Advanced | Week 4 | Scenarios, sensitivity heatmaps, tornado, Excel export, auth and saved models | ⬜ |
| 4. Differentiators | Weeks 5-6 | 2-3 Tier 3 features, Docker, CI, deployment, polished README, demo GIF | ⬜ |

Status key: ⬜ not started · 🟨 in progress · ✅ done

**Chosen Tier 3 features (fill in):**
- [ ] ______________
- [ ] ______________
- [ ] ______________

---

## 15. Git Workflow and Conventions

**Branching:** `main` (stable) · feature branches like `feat/wacc-calculator`, `fix/tv-validation`, `docs/readme-update`.

**Conventional commits:**
```
feat: add WACC calculator with CAPM
fix: handle negative FCF in terminal value
docs: add methodology section
test: add DCF reference tests against Excel model
refactor: extract projection logic into service
chore: configure CI workflow
```

**Pull requests:** even working solo, open PRs for features. It builds a clean history that recruiters can read.

**Repository metadata**
- **Name:** `intrinsic-dcf-platform`
- **Description:** Full-stack DCF valuation platform: FastAPI + React. Auto-fetches financials, builds three-statement projections, WACC, scenarios, Monte Carlo simulation, reverse DCF, and Excel/PDF export.
- **Topics:** `dcf` `valuation` `finance` `fintech` `fastapi` `react` `typescript` `python` `monte-carlo-simulation` `financial-modeling` `equity-research` `data-visualization`
- **License:** MIT

---

## 16. Deployment

| Component | Suggested host |
|---|---|
| Frontend | Vercel or Netlify |
| Backend | Render, Railway, or Fly.io (Docker) |
| PostgreSQL | Neon, Supabase, or the host's managed DB |
| Redis | Upstash or the host's managed Redis |

Add the live demo URL and screenshots at the top of this README once deployed.

---

## 17. Limitations and Assumptions

- Free data sources (yfinance) can be incomplete, delayed, or occasionally wrong. Statements should be spot-checked against annual reports.
- DCF outputs are extremely sensitive to WACC and terminal growth; small changes cause large swings. That is why sensitivity analysis exists.
- Not suitable as-is for **banks, insurers, or financial companies** (they need FCFE/dividend-discount or residual-income models) or for **loss-making early-stage companies** with unpredictable cash flows.
- Beta from historical regression is backward-looking.
- Currency, accounting standards (Ind AS vs. US GAAP/IFRS), and fiscal-year differences are handled on a best-effort basis.
- Monte Carlo results depend on the chosen distributions, which are subjective.
- This tool is for education only and is **not investment advice**.

---

## 18. Interview Cheat Sheet

Be ready to answer these confidently:

1. **Why DCF?** It values a business on its fundamentals rather than market sentiment.
2. **Why FCFF and not net income?** Cash flow reflects actual cash available to all capital providers, before financing choices.
3. **How did you choose WACC?** Walk through Rf, beta (and how you computed it), ERP, cost of debt, capital weights.
4. **Why that terminal growth rate?** Must be at or below long-run nominal GDP growth, and always below WACC.
5. **Why is TV such a large share of EV?** Most value sits beyond the forecast window; that's why you cross-check with exit multiples and sensitivity.
6. **What are DCF's weaknesses?** Garbage in, garbage out; sensitivity to assumptions; hard to apply to financials/startups.
7. **What does your Monte Carlo tell you that a single point estimate can't?** The range and probability of outcomes.
8. **What does reverse DCF tell you?** What growth the market is pricing in, which you can sanity-check.
9. **How did you verify correctness?** Unit tests against a hand-built Excel model.
10. **Engineering questions:** why FastAPI, how you cache, how you handle simulation latency, how you structured services vs. routes.

---

## 19. Glossary

| Term | Meaning |
|---|---|
| **DCF** | Discounted Cash Flow, a valuation method using the present value of future cash flows |
| **FCFF** | Free Cash Flow to Firm, cash available to all capital providers |
| **EBIT / EBITDA** | Earnings before interest and taxes / plus depreciation and amortisation |
| **NOPAT** | Net operating profit after tax = EBIT × (1 − t) |
| **NWC** | Net working capital = operating current assets − operating current liabilities |
| **WACC** | Weighted average cost of capital, the discount rate |
| **CAPM** | Capital Asset Pricing Model, used for cost of equity |
| **Beta (β)** | Sensitivity of a stock's returns to market returns |
| **ERP** | Equity risk premium, the extra return investors demand over the risk-free rate |
| **Terminal Value** | Value of cash flows beyond the explicit forecast period |
| **Enterprise Value** | Value of the whole business (debt + equity claims) |
| **Equity Value** | Value attributable to shareholders |
| **Reverse DCF** | Solving for the assumptions implied by the current price |
| **Football field** | Chart showing valuation ranges from multiple methods side by side |
| **Tornado chart** | Bar chart ranking inputs by their impact on the output |

---

## 20. Resources

- **Aswath Damodaran:** free valuation courses, datasets (ERP, betas, industry multiples), and spreadsheets: pages.stern.nyu.edu/~adamodar
- **Breaking Into Wall Street / Wall Street Prep:** DCF and three-statement modelling tutorials
- **Investopedia:** quick definitions
- **FastAPI docs:** fastapi.tiangolo.com
- **React / TanStack Query / ECharts docs**
- Company annual reports (for verifying data), e.g. Infosys, TCS investor relations pages

---

## 21. License

Released under the **MIT License**. See [`LICENSE`](LICENSE).

---

*Last updated: 9 November 2026. Keep this README in sync as features are built: tick the checkboxes in §4 and §14 as you go.*
