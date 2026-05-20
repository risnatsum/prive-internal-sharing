# Sample Requests — Prive API

Sample request bodies for the two main endpoints used by this app.
Replace `{tenant}` with your tenant ID from the login response.
Prices below are approximate — replace with live values before testing.

**ISINs used (verify before production use):**
| Stock | ISIN | Currency | Note |
|---|---|---|---|
| Meta | US30303M1027 | USD | |
| Apple | US0378331005 | USD | |
| Alphabet (Google) | US02079K3059 | USD | |
| Nvidia | US67066G1040 | USD | |
| Tesla | US88160R1014 | USD | |
| BYD | HK0002669359 | HKD | H-shares (Hong Kong listed) — verify share class |
| Amazon | US0231351067 | USD | |
| Berkshire Hathaway B | US0846707026 | USD | BRK.B |

Equal weight portfolio: 12.5% per asset.

---

## 1. Fitness Scores

**Endpoint:** `POST https://api-micro.privemanagers.com/restful/quant-proxy/2/{tenant}/optimise/codes?fitnessOnly=true`

Scores an existing portfolio against fitness criteria without running the optimiser.
The `current-portfolio` array uses monetary values, not percentages.
$1,000,000 base portfolio → $125,000 per asset at equal weight.

```json
{
  "parameters": {
    "allow-cash-in-proposed": false,
    "rebalancing": true,
    "backtest-rebalance-interval": 3
  },
  "session": {
    "id": "",
    "currency": "USD"
  },
  "goals": [
    {
      "optimise": true,
      "goal-id": "PortfolioAssessmentId",
      "goal": "PortfolioAssessment",
      "current-portfolio": [
        { "asset-code": "US30303M1027", "asset-code-scheme": "ISIN", "asset-currency": "USD", "account-id": "NA", "value": 125000 },
        { "asset-code": "US0378331005", "asset-code-scheme": "ISIN", "asset-currency": "USD", "account-id": "NA", "value": 125000 },
        { "asset-code": "US02079K3059", "asset-code-scheme": "ISIN", "asset-currency": "USD", "account-id": "NA", "value": 125000 },
        { "asset-code": "US67066G1040", "asset-code-scheme": "ISIN", "asset-currency": "USD", "account-id": "NA", "value": 125000 },
        { "asset-code": "US88160R1014", "asset-code-scheme": "ISIN", "asset-currency": "USD", "account-id": "NA", "value": 125000 },
        { "asset-code": "HK0002669359", "asset-code-scheme": "ISIN", "asset-currency": "HKD", "account-id": "NA", "value": 125000 },
        { "asset-code": "US0231351067", "asset-code-scheme": "ISIN", "asset-currency": "USD", "account-id": "NA", "value": 125000 },
        { "asset-code": "US0846707026", "asset-code-scheme": "ISIN", "asset-currency": "USD", "account-id": "NA", "value": 125000 }
      ]
    }
  ],
  "fitness": [
    { "function": "PPF",  "weight": 0.25 },
    { "function": "PVF",  "weight": 0.25 },
    { "function": "SRF",  "weight": 0.25 },
    { "function": "PDF",  "weight": 0.25 }
  ]
}
```

---

## 2. Optimiser

**Endpoint:** `POST https://api-micro.privemanagers.com/quant2/{tenant}/optimise/2/codes`

Runs the genetic algorithm optimiser across the asset universe.
The `assets` array is the investable universe — the app uses Approach 2 where
the user's portfolio IS the universe.
The `current-portfolio` in goals is a $1M cash starting position (the optimiser
reallocates it from scratch).
Prices are approximate USD values as of May 2026 — replace with live data.
BYD price is in HKD; fx-rate converts to USD (approx 7.8).

```json
{
  "session": {
    "id": "PriveInvestor",
    "currency": "USD",
    "risk-profile": 6,
    "expected-return": 0.08,
    "preferred-currencies": ["USD"]
  },
  "goals": [
    {
      "goal-id": "Goal_SampleRun",
      "goal": "Client Goal",
      "optimise": true,
      "risk": 6,
      "current-portfolio": [
        {
          "asset-code": "USD",
          "asset-code-scheme": "cash",
          "asset-currency": "USD",
          "value": 1000000,
          "account-id": "SA"
        }
      ]
    }
  ],
  "assets": [
    { "asset-code": "US30303M1027", "asset-code-scheme": "ISIN", "asset-currency": "USD", "price": 580.00, "fx-rate": 1 },
    { "asset-code": "US0378331005", "asset-code-scheme": "ISIN", "asset-currency": "USD", "price": 200.00, "fx-rate": 1 },
    { "asset-code": "US02079K3059", "asset-code-scheme": "ISIN", "asset-currency": "USD", "price": 165.00, "fx-rate": 1 },
    { "asset-code": "US67066G1040", "asset-code-scheme": "ISIN", "asset-currency": "USD", "price": 115.00, "fx-rate": 1 },
    { "asset-code": "US88160R1014", "asset-code-scheme": "ISIN", "asset-currency": "USD", "price": 290.00, "fx-rate": 1 },
    { "asset-code": "HK0002669359", "asset-code-scheme": "ISIN", "asset-currency": "HKD", "price": 320.00, "fx-rate": 7.8 },
    { "asset-code": "US0231351067", "asset-code-scheme": "ISIN", "asset-currency": "USD", "price": 200.00, "fx-rate": 1 },
    { "asset-code": "US0846707026", "asset-code-scheme": "ISIN", "asset-currency": "USD", "price": 530.00, "fx-rate": 1 }
  ],
  "parameters": {
    "max-nb-assets-in-portfolio": 8,
    "allow-cash-in-proposed": false,
    "rebalancing": true,
    "backtest-rebalance-interval": 3,
    "rounding": true,
    "minimum-allocation": 0.01,
    "deterministic": true
  },
  "fitness": [
    { "function": "PPF",  "weight": 0.25 },
    { "function": "PVF",  "weight": 0.25 },
    { "function": "SRF",  "weight": 0.25 },
    { "function": "PDF",  "weight": 0.25 }
  ]
}
```

---

## Headers (both requests)

```
Authorization: Bearer {accessToken}
Content-Type: application/json
```

`accessToken` comes from `POST https://www.privemanagers.com/restful/login/`.
