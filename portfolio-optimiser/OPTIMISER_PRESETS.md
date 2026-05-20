# Optimiser Presets Configuration

This document is the **single source of truth** for the optimisation strategy presets used in the Portfolio Optimiser. Business users can edit this file to change the number of presets, their names, descriptions, and the underlying fitness functions.

> **Note to AI Agent:** When this file is updated, ensure you synchronise the changes to:
> 1. `frontend/src/config/presets.js`
> 2. `backend/src/services/presetEnrichment.js`

---

## Global Settings
- **Risk Cap**: 5 (The default risk cap applied to all fitness functions in the backend)

---

## Available Fitness Functions (Prive API)

Descriptions sourced from official Prive documentation. Weights across the fitness array must sum to exactly `1.0`. The "Used in Presets" column references preset IDs defined below.

> **Note:** SHARPE, REAI, MRVI, and FXM are the confirmed correct codes for this tenant. Alternate codes visible in some Prive documentation (SRF, REAF, MRVF, FXMF) are not used here.

| Code | Full Name | Description | Extra Field Required | Used in Presets |
| :--- | :--- | :--- | :--- | :--- |
| SHARPE | Sharpe Ratio Factor | Maximises the Sharpe Ratio — the highest possible return per unit of risk. | — | 1, 2 |
| PDF | Product Diversification Factor | Measures diversification across different products. Portfolio will have as many products as possible with approximately equal weight. Adjustment factors for product types can be applied. | — | 1, 2, 4 |
| REAI | Re-use Existing Assets Index | Measures re-use of the investor's existing holdings. Reduces turnover by retaining as many current positions as possible. | — | 1, 3, 4 |
| EWF | Equal Weight Factor | Measures the extent of equally-weighted assets. Reduces extreme allocations on the high or low end, producing a more balanced portfolio. | — | 2 |
| MRVI | Min Return-gap Volatility Index | Minimises the difference between the portfolio's return and the client's target return, subject to volatility not exceeding the risk limit. | — | 3 |
| MPF_20176 | Maximum Performance Factor (#20176) | Maximises return by targeting and exceeding the expected annualised return. Model ID 20176 specifies the underlying model configuration. | — | 3 |
| VCPF | Volatility Cap Performance Factor | Targets a volatility ceiling. Portfolio will try to reach but not breach the target volatility. | `risk-cap` (decimal, e.g. `0.15` = 15% vol cap) | 4 |
| FXM | FX Minimisation Factor | Minimises FX transactions by keeping as much of each currency's allocation from the existing portfolio as possible. Reduces currency conversion costs and FX risk. | — | 4 |
| ACAF | Asset Class Allocation Factor | Measures how closely the portfolio follows a target allocation across asset classes. | `asset-class-allocation` (array of class + weight pairs) | — |
| PAF | Preferred Assets Factor | Maximises use of preferred assets (e.g. premier funds). Primarily used to identify assets for retention/sale. | — | — |
| PCF | Preferred Currency Factor | Maximises portfolio value invested in assets denominated in the investor's preferred currencies. | — | — |
| PPF | Portfolio Performance Factor | Maximises portfolio return relative to the model portfolio. More aggressive than MPF. | — | — |
| PVF | Portfolio Volatility Factor | Minimises portfolio volatility relative to the model portfolio. | — | — |
| MPF | Maximum Performance Factor (base) | Maximises return by targeting and exceeding the expected annualised return. Base version without a specific model ID. | — | — |
| ETFF | ETF Allocation Factor | Steers the portfolio toward a target ETF allocation percentage. | `ETF-allocation` (decimal) | — |
| REGF | Regional Allocation Factor | Measures how closely the portfolio follows a target regional exposure breakdown. | `region-allocation` (array of region + weight pairs) | — |

---

## Presets

### Preset 1: Markowitz / Efficient Frontier
- **ID**: 1
- **Badge**: Risk-Adjusted Return
- **Description**: Maximises the Sharpe Ratio — return per unit of risk — using mean-variance optimisation.
- **Tooltip**: Finds the portfolio with the best return-to-risk ratio by balancing expected return against volatility. Best for clients seeking an efficient, data-driven allocation.
- **Fitness Functions**:
| Function | Weight | Rationale |
| :--- | :--- | :--- |
| SHARPE | 0.70 | Primary objective. At 70% it dominates the optimisation — this is a Markowitz strategy so Sharpe must drive the result. |
| REAI | 0.20 | Minimises unnecessary turnover. Without this, the optimiser might achieve the same Sharpe by reshuffling holdings in ways that cost the client in transaction fees and tax events. |
| PDF | 0.10 | Prevents the optimiser from collapsing to a 1–2 asset portfolio to hit Sharpe. A light diversification floor keeps the result practically deployable. |

### Preset 2: Diversification & Regional Preferences
- **ID**: 2
- **Badge**: Diversification
- **Description**: Spreads holdings across products, asset classes, regions, and sectors. Aligns to client's regional preferences.
- **Tooltip**: Reduces concentration risk by spreading holdings across products and issuers. Best for clients with diversification mandates.
- **Fitness Functions**:
| Function | Weight | Rationale |
| :--- | :--- | :--- |
| PDF | 0.60 | Primary objective. Drives spreading across products and issuers — this is the core mandate for diversification-focused clients. |
| SHARPE | 0.20 | Economic guard rail. Diversification alone could produce a well-spread but low-return portfolio. SHARPE at 20% ensures the result still makes financial sense. |
| EWF | 0.20 | Complements PDF by penalising extreme position sizes. PDF counts how many different products are held; EWF ensures no single position dominates the allocation, preventing PDF being satisfied by 10 holdings where one is 80%. |

### Preset 3: Return & Yield Maximisation
- **ID**: 3
- **Badge**: Maximum Return
- **Description**: Pushes toward the highest achievable return within a defined risk ceiling.
- **Tooltip**: Targets the highest achievable return within a defined risk ceiling. Best for growth-oriented clients comfortable with higher volatility.
- **Fitness Functions**:
| Function | Weight | Rationale |
| :--- | :--- | :--- |
| MRVI | 0.40 | Primary objective. Targets the client's expected return while keeping volatility within the risk limit — this is the disciplined version of return maximisation that doesn't ignore risk entirely. |
| MPF_20176 | 0.30 | Adds a model-portfolio-anchored return push. Where MRVI is constrained by the risk limit, MPF_20176 provides additional directional pressure toward high-return assets using a specific pre-calibrated model configuration. |
| REAI | 0.30 | Turnover control. Growth-oriented strategies can churn aggressively; REAI at 30% ensures the optimised result doesn't force costly full-portfolio liquidations to achieve return targets. |

### Preset 4: Capital Preservation / Low-Volatility
- **ID**: 4
- **Badge**: Capital Preservation
- **Description**: Minimises volatility for conservative clients or capital protection mandates.
- **Tooltip**: Minimises portfolio volatility to protect capital. Best for conservative clients or mandates where preserving principal is the primary objective.
- **Fitness Functions**:
| Function | Weight | Rationale |
| :--- | :--- | :--- |
| VCPF | 0.50 | Primary objective. The hard volatility ceiling is the defining constraint for a capital preservation mandate — it must dominate the fitness score. |
| FXM | 0.20 | For conservative clients, unhedged currency exposure is a significant source of unexpected losses. FX minimisation keeps the portfolio close to the base currency, reducing a major volatility contributor that VCPF alone does not specifically target. |
| REAI | 0.20 | Conservative clients often have low tolerance for transaction activity. REAI ensures the optimised portfolio retains existing positions wherever possible, reducing realisation events and their associated costs and tax implications. |
| PDF | 0.10 | Light diversification safeguard. Prevents the optimiser from concentrating into a single "low-vol" sector or issuer, which would carry idiosyncratic risk not captured by VCPF. |

### Preset 5: Custom Configuration
- **ID**: 5
- **Badge**: Admin
- **Description**: Manually configure your own fitness factors and weights for a completely custom optimisation strategy.
- **Tooltip**: Access requires an admin password. Allows granular control over all available optimisation factors.
- **Fitness Functions**: Dynamically defined by the user via the custom builder. No fixed fitness array.

> **Note:** This preset is admin-only and sends whatever the user constructs in the UI directly to the backend. It is not recognised by `presetEnrichment.js` (which only handles presets 1–4); the fitness array is passed through as-is from the frontend.
