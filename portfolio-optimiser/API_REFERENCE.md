# Prive API Reference

## Why This Document Exists

The official Prive documentation is incomplete. It does not reliably indicate which fields are truly mandatory in a given request context, does not define the specific string codes required for enum fields, and does not document field-name inconsistencies in responses. This reference captures real-world field names, mandatory fields, valid enum values, undocumented constraints, and gotchas discovered during implementation. Always treat this document as the source of truth for integration behaviour; consult the official docs only for higher-level concepts.

---

## Base URLs

| Environment | Auth Base URL | API Base URL |
|-------------|--------------|--------------|
| PROD | `https://www.privemanagers.com` | `https://api-micro.privemanagers.com` |
| UAT | `https://uat.privemanagers.com` | `https://api-micro-uat.privemanagers.com` |

All endpoints below indicate which base URL applies.

---

## POST /restful/login/

**Base URL:** Auth  
**Auth required:** No

### Request

```json
{
  "username": "string",
  "password": "string"
}
```

### Response Fields

| Field | Notes |
|-------|-------|
| `accessToken` | Bearer token. **Store server-side only — never send to the browser.** Attach as `Authorization: Bearer <token>` on all subsequent API calls. |
| `tenant` | **MANDATORY to capture.** Used as a path parameter in all API endpoints. |
| `status` | See status codes below. |
| `userDetails.username` | Echoes back the authenticated username. |

### Status Codes

| Code | Meaning |
|------|---------|
| `SUCCESS` | Valid login. |
| `FAIL` | Wrong credentials. |
| `NEED_2FA` | 2FA required — resubmit with 2FA code. Field name for the code TBC with Prive. |
| `ACCOUNT_LOCKED` | Contact Prive admin. |
| `CREDENTIALS_LOGIN_FORBIDDEN` | This account requires API key auth, not username/password. |
| `LOGIN_TIMES_OVER_RESTRICTION` | Too many failed attempts; temporary lockout. |

> **Gotcha:** HTTP 200 does NOT mean success. A `FAIL` response also returns HTTP 200. Always check the `status` field — do not rely on HTTP status code alone.

---

## POST /assets/1/{tenant}/codes

**Base URL:** API  
**Auth required:** Yes (Bearer)

Validates a list of assets against the Prive asset universe.

### Request

Array of asset objects:

```json
[
  { "scheme": "ISIN", "value": "US0378331005", "currency": "USD" },
  { "scheme": "TICKER", "value": "AAPL", "currency": "USD" }
]
```

| Field | Valid Values | Notes |
|-------|-------------|-------|
| `scheme` | `"ISIN"` (confirmed), `"TICKER"` (placeholder — confirm exact value with Prive) | |
| `value` | ISIN: 12-char alphanumeric. Ticker: exchange symbol. | |
| `currency` | ISO 4217 3-letter code (e.g. `"USD"`, `"EUR"`, `"GBP"`) | |

### Response

Array of matched asset objects, aligned to valid items in the input. Items present = asset found. Items absent = asset not found in Prive universe.

Because Prive returns field names inconsistently, handle all of the following fallbacks:

| Data | Primary Field | Fallbacks |
|------|--------------|-----------|
| Asset name | `name` | `assetName`, `description`. May be a localised object `{ "en": "...", "default": "..." }` — extract `.en` or `.default`. |
| Price | `latestPrice` | `price`, `close-px`, `lastPrice`, `value` |
| Identifier | `isin` | `ticker`, `value`, `code`, `asset-code` |
| Asset ID | `assetId` | `asset-id`, `id`, `key` |

> **Gotcha:** The response is positional — if you send 5 assets and only 4 are valid, you get 4 items back, NOT 5 with nulls. You must match response items by identifier to determine which asset failed validation.

---

## POST /assets/1/{tenant}/currencies/{pair}

**Base URL:** API  
**Auth required:** Yes (Bearer)

Fetches the FX rate for a currency pair.

### Path Parameter

| Parameter | Format | Example |
|-----------|--------|---------|
| `{pair}` | 6-character string, no slash. Base currency + quote currency. | `USDEUR` = USD to EUR. `GBPUSD` = GBP to USD. |

### Response

```json
{ "rate": 1.0895 }
```

---

## GET /assets/1/{tenant}/assets/price

**Base URL:** API  
**Auth required:** Yes (Bearer)

Fetches the latest price for a single asset.

### Query Parameters

| Parameter | Format | Notes |
|-----------|--------|-------|
| `codeScheme` | `"ISIN"` | |
| `codeValue` | ISIN string | e.g. `US0378331005` |
| `currency` | ISO 4217 | |
| `date` | `YYYY-MM-DD` | **Must be a business day (Mon–Fri).** Pass the last working day before today. Weekends and holidays return an error or empty response. |

### Response

```json
{ "price": 200.50 }
```

or

```json
{ "close-px": 200.50 }
```

Handle both field names.

---

## POST /quant2/{tenant}/optimise/2/codes

**Base URL:** API  
**Auth required:** Yes (Bearer)  
**Timeout:** Minimum 180 seconds — set this on the HTTP client.

This is the core optimisation endpoint. The request is a single JSON object with four top-level keys: `session`, `goals`, `assets`, and `parameters`.

### Request Structure

#### `session` Object

| Field | Type | Notes |
|-------|------|-------|
| `id` | string | Arbitrary identifier. Use `"PriveInvestor"` or similar. |
| `currency` | string | ISO 4217 base currency (e.g. `"USD"`). |
| `expected-return` | float | Target return (e.g. `0.08` = 8%). Used in some fitness functions. |
| `preferred-currencies` | array of strings | Array of ISO 4217 codes. |

#### `goals` Array

One object per goal:

| Field | Type | Notes |
|-------|------|-------|
| `goal-id` | string | Arbitrary unique ID (e.g. `"Goal_123456"`). |
| `goal` | string | Label (e.g. `"GeneralInvestmentGoal"`). |
| `optimise` | boolean | `true` = optimise this goal. |
| `risk` | integer | 1–10. 1 = most conservative, 10 = most aggressive. |
| `current-portfolio` | array | See below. |

#### `current-portfolio` Items

| Field | Type | Notes |
|-------|------|-------|
| `asset-code` | string | ISIN, or currency code for cash. |
| `asset-code-scheme` | string | `"ISIN"` \| `"TICKER"` \| `"cash"` |
| `asset-currency` | string | ISO 4217. |
| `value` | float | Normalise entire portfolio to 1,000,000. Formula: `weight% / 100 × 1,000,000`. |
| `account-id` | string | Arbitrary (e.g. `"SA"`). |

**Cash item mandatory rules:**
- `asset-code-scheme` MUST be `"cash"`
- `asset-code` MUST be the currency code (e.g. `"USD"`)
- `value` calculated the same as other assets
- The cash asset's `price` in the `assets` array MUST be `1.0` — not `0`, not omitted

#### `assets` Array (Investable Universe)

| Field | Type | Notes |
|-------|------|-------|
| `asset-code` | string | ISIN or currency code. |
| `asset-code-scheme` | string | `"ISIN"` \| `"cash"` |
| `asset-currency` | string | ISO 4217. |
| `price` | float | Latest price. **MUST NOT be `0` or `null` — causes optimizer error.** |
| `fx-rate` | float | FX rate to base currency. `1.0` if same currency as base. |

> **Important:** Do NOT include cash assets in the `assets` array. Including cash in the universe causes the optimizer to over-allocate to cash.

#### `parameters` Object

| Field | Type | Notes |
|-------|------|-------|
| `max-nb-assets-in-portfolio` | integer | **MUST be set dynamically to `inputAssets.length`.** Too low = optimizer rejects. Too high = safe but wasteful. |
| `allow-cash-in-proposed` | boolean | `false` = proposed portfolio contains no cash. |
| `rebalancing` | boolean | |
| `backtest-rebalance-interval` | integer | Months between rebalances (e.g. `3` = quarterly). |
| `rounding` | boolean | |
| `minimum-allocation` | float | Minimum weight per asset (e.g. `0.01` = 1%). |
| `max-concentration-of-assets` | float | Maximum allowed concentration of an asset in the portfolio (e.g. `0.30` = 30%). |
| `deterministic` | boolean | `true` = reproducible results across runs. |

#### `fitness` Array

Each item specifies a fitness function and its weight:

```json
{ "function": "<CODE>", "weight": 0.60 }
```

For `VCPF`, include `"risk-cap": <float>`.

**Weights across all fitness items MUST sum to exactly `1.0`.**

| Function Code | Name |
|--------------|------|
| `SHARPE` | Sharpe ratio (risk-adjusted return) |
| `PDF` | Product Diversification Factor |
| `REAI` | Reuse Existing Assets (minimises turnover) |
| `VCPF` | Volatility Capped Performance Factor (requires `risk-cap`) |
| `FXM` | FX Minimisation Factor |
| `MRVI` | Min Return gap Volatility Factor |
| `MPF_20176` | Maximum Performance Factor |
| `EWF` | Equal Weight Factor |

### Response Key Fields

All fields are under `proposed-goals[0]` for the proposed portfolio and `current-goals[0]` for the current portfolio.

| Field | Notes |
|-------|-------|
| `proposed-goals[0].proposed-holdings` | Array of `{ "asset-code": "...", "asset-code-scheme": "...", "asset-weight": 0.45 }`. Weights are decimals 0–1 — multiply by 100 for display. |
| `proposed-goals[0].return` | Annualised return (e.g. `0.095` = 9.5%). |
| `proposed-goals[0].volatility` | Annualised volatility. |
| `proposed-goals[0].sharpe` | Sharpe ratio. |
| `proposed-goals[0].total-fitness-score` | Overall fitness score. |
| `current-goals[0].return` | Same metric for current portfolio. |
| `current-goals[0].volatility` | Same metric for current portfolio. |
| `current-goals[0].sharpe` | Same metric for current portfolio. |

> **Gotcha:** If the optimizer returns fewer assets than expected, it hit the `max-nb-assets-in-portfolio` cap. Always set this dynamically.

> **Gotcha:** Zero-price assets silently cause the optimizer to fail or produce invalid allocations. Validate all asset prices before sending the request.

> **Gotcha:** The optimizer can time out. Set a minimum client-side timeout of 180 seconds and surface a clear timeout message to the user.

---

## POST /restful/quant-proxy/2/{tenant}/optimise/codes?fitnessOnly=true

**Base URL:** API  
**Auth required:** Yes (Bearer)

Accepts the same request structure as the main optimiser endpoint. When `fitnessOnly=true` is passed as a query parameter, returns fitness scores for the current portfolio **without** running the full optimisation. Use for pre-optimisation scoring or portfolio assessment.

---

## POST /b2b/2/{tenant}/stress

**Base URL:** API  
**Auth required:** Yes (Bearer)

Runs stress scenarios against a portfolio. Returns simulated performance under predefined or custom market shock conditions.

> **Status:** Request/response structure partially confirmed. Fields marked **[UNCONFIRMED]** need verification with Prive.

### Request

```json
{
  "portfolio": {
    "holdings": [
      {
        "asset-code": "US0378331005",
        "asset-code-scheme": "ISIN",
        "asset-currency": "USD",
        "weight": 0.20
      }
    ],
    "currency": "USD"
  },
  "scenarios": ["2008_FINANCIAL_CRISIS", "COVID_2020", "RATE_HIKE_2022"]
}
```

| Field | Type | Notes |
|-------|------|-------|
| `portfolio.holdings[].asset-code` | string | ISIN or currency code. |
| `portfolio.holdings[].asset-code-scheme` | string | `"ISIN"` \| `"cash"` |
| `portfolio.holdings[].asset-currency` | string | ISO 4217. |
| `portfolio.holdings[].weight` | float | 0–1. All weights must sum to 1.0. |
| `portfolio.currency` | string | ISO 4217 base currency. |
| `scenarios` | array of strings | **[UNCONFIRMED]** Exact scenario codes TBC with Prive. Common candidates: `"2008_FINANCIAL_CRISIS"`, `"COVID_2020"`, `"RATE_HIKE_2022"`, `"DOT_COM_BUST"`. Request full list from Prive. |

### Response (Structure Unconfirmed)

Expected shape — verify with Prive:

```json
{
  "results": [
    {
      "scenario": "2008_FINANCIAL_CRISIS",
      "portfolioReturn": -0.38,
      "maxDrawdown": -0.52,
      "recoveryMonths": 24
    }
  ]
}
```

> **Gotcha [UNCONFIRMED]:** Prive may require the portfolio to be passed as normalised values (total = 1,000,000) rather than weights. Confirm with Prive before implementing.

---

## POST /restful/classification-proxy/1/{tenant}/portfolio

**Base URL:** API  
**Auth required:** Yes (Bearer)

Classifies a portfolio's holdings by asset class, geographic region, sector, and ESG ratings. Returns a breakdown suitable for displaying allocation charts.

> **Status:** Request/response structure partially confirmed. Fields marked **[UNCONFIRMED]** need verification with Prive.

### Request

```json
{
  "holdings": [
    {
      "asset-code": "US0378331005",
      "asset-code-scheme": "ISIN",
      "asset-currency": "USD",
      "weight": 0.20
    }
  ],
  "currency": "USD",
  "classificationTypes": ["ASSET_CLASS", "REGION", "SECTOR", "ESG"]
}
```

| Field | Type | Notes |
|-------|------|-------|
| `holdings[].asset-code` | string | ISIN or currency code. |
| `holdings[].asset-code-scheme` | string | `"ISIN"` \| `"cash"` |
| `holdings[].asset-currency` | string | ISO 4217. |
| `holdings[].weight` | float | 0–1. All weights must sum to 1.0. |
| `currency` | string | ISO 4217 base currency. |
| `classificationTypes` | array of strings | **[UNCONFIRMED]** Exact type codes TBC with Prive. |

### Response (Structure Unconfirmed)

Expected shape — verify with Prive:

```json
{
  "classifications": {
    "ASSET_CLASS": [
      { "label": "Equity", "weight": 0.65 },
      { "label": "Fixed Income", "weight": 0.30 },
      { "label": "Cash", "weight": 0.05 }
    ],
    "REGION": [
      { "label": "North America", "weight": 0.50 },
      { "label": "Europe", "weight": 0.25 },
      { "label": "Asia Pacific", "weight": 0.25 }
    ],
    "ESG": {
      "environmentScore": 72,
      "socialScore": 68,
      "governanceScore": 74,
      "overallScore": 71
    }
  }
}
```

> **Gotcha [UNCONFIRMED]:** ESG scores may not be available for all assets. Prive may return `null` scores for assets without ESG data — null-check before rendering.

---

## POST /quant2/{tenant}/optimise/2/graphql

**Base URL:** API  
**Auth required:** Yes (Bearer)  
**Query parameter:** `?fitness=null`  
**Timeout:** Minimum 180 seconds.

Open-universe variant of the optimiser. Instead of sending a fixed `assets` array, you send a GraphQL query that asks Prive to dynamically select the investable universe based on asset type, risk level, and currency filters. Prive runs the same optimisation engine — the only structural difference from the REST optimiser is the `assets` block.

Use this endpoint when the user has not pre-selected specific assets and wants the optimiser to source candidates from the full Prive universe.

### How It Differs from the REST Optimiser

| | REST (`/optimise/2/codes`) | GraphQL (`/optimise/2/graphql`) |
|-|---------------------------|--------------------------------|
| Investable universe | Explicit `assets` array you build | Dynamic — Prive queries its own database at runtime |
| Universe size | You control | Controlled via `max-nb-assets-in-portfolio` cap |
| Asset validation step | Required upfront | Not required — Prive handles it internally |
| `asset-universe` field | Not sent | `"CLOSED_UNIVERSE"` |
| `asset-universe-type` field | Not sent | `"filtered"` |

### Request Structure

Top-level shape:

```json
{
  "asset-universe": "CLOSED_UNIVERSE",
  "asset-universe-type": "filtered",
  "assets": { "graphql": { ... } },
  "fitness": [ ... ],
  "goals": [ ... ],
  "parameters": { ... },
  "session": { ... }
}
```

#### `assets.graphql` Object

```json
{
  "name": "dynamicSearchAssetsByCriteriaV3",
  "variables": {
    "filterOperator": "AND",
    "filters": [
      {
        "and": [
          { "fieldName": "type",       "operand": "IN",          "value": ["MUTUAL_FUNDS", "ETF", "EQUITY", "BOND"] },
          { "fieldName": "risk-level", "operand": "LESS_EQUAL",  "value": 3 },
          { "fieldName": "currency",   "operand": "IN",          "value": ["USD", "SGD", "HKD", "EUR", "GBP", "JPY", "KRW"] }
        ]
      }
    ]
  }
}
```

| Field | Notes |
|-------|-------|
| `name` | Always `"dynamicSearchAssetsByCriteriaV3"`. |
| `variables.filterOperator` | Always `"AND"`. |
| `filters[].and[].fieldName` | Field to filter on. Supported: `"type"`, `"risk-level"`, `"currency"`. |
| `filters[].and[].operand` | `"IN"` for array membership, `"LESS_EQUAL"` for numeric comparison. |
| `filters[].and[].value` | For `"type"`: subset of `["MUTUAL_FUNDS", "ETF", "EQUITY", "BOND"]`. For `"risk-level"`: integer 1–10 matching the goal risk. For `"currency"`: union of portfolio currencies + default set. |

**Currency list rule:** Always merge the currencies present in the current portfolio with the default set `["USD", "SGD", "HKD", "EUR", "GBP", "JPY", "KRW"]` and deduplicate.

#### `goals` Array

Same structure as the REST optimiser, **except** `risk` is not set on the goal object here — risk filtering is handled by the GraphQL `risk-level` filter above.

```json
{
  "goal-id": "Goal_1715500000000",
  "goal": "Client Goal",
  "optimise": true,
  "current-portfolio": [ ... ]
}
```

`current-portfolio` items are the same format as the REST optimiser — **exclude cash assets**. The portfolio is normalised to 1,000,000.

#### `parameters` Object

| Field | Value | Notes |
|-------|-------|-------|
| `max-nb-assets-in-portfolio` | `Math.min(nonCashAssets.length, 18)` | Hard cap of 18. Set dynamically. |
| `allow-cash-in-proposed` | `false` | |
| `rebalancing` | `true` | |
| `backtest-rebalance-interval` | `12` | Annual rebalance (months). REST optimiser uses `3`. |
| `rounding` | `true` | |
| `minimum-allocation` | `0.02` | 2% minimum per asset. |
| `deterministic` | `true` | |

> **Gotcha:** `backtest-rebalance-interval` is `12` here (annual) vs `3` in the REST optimiser (quarterly). This affects backtest performance figures — do not mix and compare results across the two endpoints.

#### `session` Object

```json
{
  "id": "PriveInvestor",
  "currency": "USD"
}
```

No `expected-return` or `preferred-currencies` — those are REST-optimiser fields not used here.

### Full Example Request

```json
{
  "asset-universe": "CLOSED_UNIVERSE",
  "asset-universe-type": "filtered",
  "assets": {
    "graphql": {
      "name": "dynamicSearchAssetsByCriteriaV3",
      "variables": {
        "filterOperator": "AND",
        "filters": [
          {
            "and": [
              { "fieldName": "type",       "operand": "IN",         "value": ["MUTUAL_FUNDS", "ETF", "EQUITY", "BOND"] },
              { "fieldName": "risk-level", "operand": "LESS_EQUAL", "value": 5 },
              { "fieldName": "currency",   "operand": "IN",         "value": ["USD", "SGD", "HKD", "EUR", "GBP", "JPY", "KRW"] }
            ]
          }
        ]
      }
    }
  },
  "fitness": [
    { "function": "SHARPE", "weight": 0.60 },
    { "function": "PDF",    "weight": 0.40 }
  ],
  "goals": [
    {
      "goal-id": "Goal_1715500000000",
      "goal": "Client Goal",
      "optimise": true,
      "current-portfolio": [
        { "asset-code": "LU1035775433", "asset-code-scheme": "ISIN", "asset-currency": "USD", "value": 200000, "price": 10.50, "fx-rate": 1.0,    "account-id": "SA" },
        { "asset-code": "LU1035773651", "asset-code-scheme": "ISIN", "asset-currency": "USD", "value": 200000, "price": 9.80,  "fx-rate": 1.0,    "account-id": "SA" },
        { "asset-code": "LU0127657111", "asset-code-scheme": "ISIN", "asset-currency": "USD", "value": 145000, "price": 15.20, "fx-rate": 1.0,    "account-id": "SA" },
        { "asset-code": "CNE1000001Z5", "asset-code-scheme": "ISIN", "asset-currency": "HKD", "value": 140000, "price": 8.50,  "fx-rate": 0.1282, "account-id": "SA" },
        { "asset-code": "LU0099575291", "asset-code-scheme": "ISIN", "asset-currency": "EUR", "value": 70000,  "price": 12.30, "fx-rate": 1.08,   "account-id": "SA" },
        { "asset-code": "LU0511406216", "asset-code-scheme": "ISIN", "asset-currency": "AUD", "value": 50000,  "price": 11.00, "fx-rate": 0.65,   "account-id": "SA" },
        { "asset-code": "LU0293313325", "asset-code-scheme": "ISIN", "asset-currency": "EUR", "value": 50000,  "price": 9.50,  "fx-rate": 1.08,   "account-id": "SA" },
        { "asset-code": "JP3218900003", "asset-code-scheme": "ISIN", "asset-currency": "JPY", "value": 40000,  "price": 5200,  "fx-rate": 0.0067, "account-id": "SA" },
        { "asset-code": "TW000T3605B8", "asset-code-scheme": "ISIN", "asset-currency": "USD", "value": 40000,  "price": 22.50, "fx-rate": 1.0,    "account-id": "SA" },
        { "asset-code": "JP3778630008", "asset-code-scheme": "ISIN", "asset-currency": "JPY", "value": 20000,  "price": 3100,  "fx-rate": 0.0067, "account-id": "SA" },
        { "asset-code": "IE0031386414", "asset-code-scheme": "ISIN", "asset-currency": "USD", "value": 20000,  "price": 18.75, "fx-rate": 1.0,    "account-id": "SA" },
        { "asset-code": "KYG017191142", "asset-code-scheme": "ISIN", "asset-currency": "HKD", "value": 15000,  "price": 14.20, "fx-rate": 0.1282, "account-id": "SA" },
        { "asset-code": "US29452E1010", "asset-code-scheme": "ISIN", "asset-currency": "USD", "value": 10000,  "price": 45.00, "fx-rate": 1.0,    "account-id": "SA" }
      ]
    }
  ],
  "parameters": {
    "max-nb-assets-in-portfolio": 13,
    "allow-cash-in-proposed": false,
    "rebalancing": true,
    "backtest-rebalance-interval": 12,
    "rounding": true,
    "minimum-allocation": 0.02,
    "deterministic": true
  },
  "session": {
    "id": "PriveInvestor",
    "currency": "USD"
  }
}
```

> **Note:** `price` and `fx-rate` values in the example above are illustrative placeholders. In production, always fetch live prices via `GET /assets/1/{tenant}/assets/price` and live FX rates via `POST /assets/1/{tenant}/currencies/{pair}`.

### Response

Identical structure to the REST optimiser response — see `proposed-goals[0]` and `current-goals[0]` fields documented under that endpoint. The response also includes per-factor fitness scores:

```json
"fitness": [
  { "function": "SHARPE", "score": 0.82 },
  { "function": "PDF",    "score": 0.74 }
]
```

These are available on both `proposed-goals[0].fitness` and `current-goals[0].fitness`.

---
