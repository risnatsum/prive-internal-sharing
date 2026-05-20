# Portfolio Optimizer API Documentation

This documentation provides an exhaustive guide to the configurable variables, optimization factors, and request/response structures for the Portfolio Optimizer API.

## 1. Request Structure Overview

The optimization request is composed of four primary objects: `session`, `parameters`, `goals`, and `fitness`.

---

## 2. Session Object (`session`)
The `session` object contains global settings for the optimization run.

| Variable | Type | Description |
| :--- | :--- | :--- |
| `id` | String | Unique session identifier. |
| `currency` | String | Base currency (3-character ISO, e.g., "USD", "EUR"). |
| `risk-profile` | Integer | Overall risk rating (1-10), mapped to volatility limits. |
| `expected-return` | Number | Target expected return for the portfolio. |
| `from` / `until` | String | Date range for historical backtesting (YYYY-MM-DD). |
| `optimization-date`| String | Specific date to run the optimization (Default: end of last month). |
| `preferred-assets` | Array | List of explicit asset preferences. |
| `preferred-currencies`| Array | List of explicit currency preferences. |
| `concentration-thresholds`| Array | Limits maximum exposure to specific categories in the session currency. |
| `model-tier` | String | Reference to a specific tier of model portfolios. |

---

## 3. Parameters Object (`parameters`)
These settings control the Genetic Algorithm (GA) and structural constraints.

| Variable | Type | Default | Min / Max | Description |
| :--- | :--- | :--- | :--- | :--- |
| `generations` | Integer | 100 | 1 / 400 | Number of iterations for the algorithm. |
| `gen-ini-population-size` | Integer | 256 | 16 / 1024 | Size of the initial random portfolio population. |
| `gen-survivors` | Integer | 128 | - | Portfolios kept at each generation. |
| `genome-mutation-rate` | Number | 0.05 | 0 / 50 | Probability of portfolio mutation per generation. |
| `min-nb-assets-in-portfolio`| Integer | - | 1 / - | Minimum number of assets required. |
| `max-nb-assets-in-portfolio`| Integer | - | 1 / - | Maximum number of assets allowed. |
| `max-concentration-of-assets`| Number | - | - | Max concentration for any single asset. |
| `backtest-rebalance-interval`| Integer | 3 | 1 / 12 | Frequency (months) for rebalancing. |
| `allow-cash-in-proposed` | Boolean | - | - | If true, optimizer may leave cash uninvested. |

---

## 4. Goals Array (`goals`)
Defines the current state and targets for specific investment objectives.

| Variable | Type | Description |
| :--- | :--- | :--- |
| `goal-id` | String | Unique identifier of the goal. |
| `goal` | String | Type (e.g., "CustomerGoal" or "GeneralInvestmentGoal"). |
| `optimise` | Boolean | If true, the optimizer focuses on this goal. |
| `risk` | Integer | Risk profile (1-10) specific to this goal. |
| `current-portfolio` | Array | Array of existing holdings. See below for asset fields. |

### Portfolio Asset Fields
* `asset-code`: Identifier (e.g., ISIN).
* `asset-code-scheme`: Scheme type (e.g., "ISIN", "cash").
* `asset-currency`: Currency of the specific asset.
* `amount` / `value`: Quantity or monetary value.
* `frozen`: Monetary value that the optimizer is not allowed to trade.
* `account-id`: Required for cash lines.

---

## 5. Fitness Factors (`fitness`)
Optimization targets. Each object requires a `function` code and a `weight` (0.0 to 1.0).

### Standard Factors
* **ACAF**: Asset Class Allocation Factor
* **PAF**: Preferred Asset Factor
* **PCF**: Preferred Currency Factor
* **FXMF**: FX Minimization Factor
* **REAF**: Reuse of Existing Assets Factor
* **PPF**: Portfolio Performance Factor
* **PVF**: Portfolio Volatility Factor
* **SRF**: Sharpe Ratio Factor
* **MRVF**: Min Return gap Vol Factor
* **PDF**: Product Diversification Factor
* **MPF**: Maximum Performance Factor
* **MVF**: Minimum Volatility Factor
* **SDF**: Stock Dividend Factor
* **EWF**: Equal Weight Factor
* **TEF**: Tracking Error Factor

### Complex Factors (Require Additional Fields)
| Function | Full Name | Required Field(s) | Description |
| :--- | :--- | :--- | :--- |
| **VCPF** | Volatility Capped Performance | `risk-cap` (Number) | Volatility cap limit (e.g., `0.05`). |
| **ESGF** | ESG Factor | `ESG-allocation` (Number) | Target ESG score/allocation (e.g., `0.6`). |
| **ETFF** | ETF Factor | `ETF-allocation` (Number) | Target ETF allocation (e.g., `0.7`). |
| **HDICORE** | Core/Satellite Factor | `core-allocation` (Number) | Target allocation for Core/Satellite strategy (e.g., `0.5`). |
| **REGF** | Region Factor | `region-allocation` (Array) | Array of objects with `region` (String) and `weight` (Number), e.g., `[{"region": "NORTH_AMERICA", "weight": 0.55}]`. |
| **ACACP** | Asset Class Allocation Current | `asset-class-allocation` (Array) | Array of objects with `asset-class` (String) and `weight` (Number). |
| **COF** | Constraint Optimisation | `min` (Number), `max` (Number), `jsonpath` (String) | Constrains a specific DB field, e.g., `"$.morningStarRating"`. |
| **FF** | FlexField Factor | `min` (Number), `max` (Number), `jsonpath` (String) | Same fields as COF; targets a specific DB field via JSONPath. |

---

## 6. Sample Request JSON
```json
{
  "parameters": {
    "max-nb-assets-in-portfolio": 12,
    "backtest-rebalance-interval": 12,
    "allow-cash-in-proposed": false
  },
  "session": {
    "id": "PriveInvestor",
    "currency": "EUR"
  },
  "goals": [
    {
      "goal-id": "Client2039House",
      "goal": "Client Goal",
      "optimise": true,
      "current-portfolio": [
        {
          "asset-code": "EUR",
          "asset-code-scheme": "cash",
          "asset-currency": "EUR",
          "value": 1000000,
          "account-id": "SA"
        }
      ]
    }
  ],
  "fitness": [
    { "function": "ETFF", "weight": 0.1125, "ETF-allocation": 0.7 },
    { "function": "VCPF", "weight": 0.55, "risk-cap": 0.03 },
    { "function": "ESGF", "weight": 0.1125, "ESG-allocation": 0.6 },
    { "function": "PDF", "weight": 0.135 },
    { "function": "EWF", "weight": 0.09 }
  ]
}
```
