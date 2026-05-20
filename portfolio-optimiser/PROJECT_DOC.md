# Executive Summary

## What This Is
The Portfolio Optimiser is a sophisticated web platform designed to modernise and streamline the investment advisory process. It acts as an intelligent bridge between raw portfolio data and advanced financial engineering, providing advisors with a professional workspace to refine, test, and explain investment strategies. Rather than just a calculator, it is a comprehensive decision-support system that transforms complex quantitative analysis—such as risk-adjusted rebalancing and stress testing—into clear, actionable narratives that are easy to communicate to end clients.

## The Problem It Solves
Traditionally, portfolio rebalancing and risk analysis are fragmented, manual processes that can take hours or even days to complete. Advisors often rely on static reports that lack the flexibility to test "what-if" scenarios or provide deep explanations for suggested changes. Furthermore, existing tools frequently fail to bridge the gap between technical metrics (like "fitness scores" or volatility) and the intuitive stories clients need to hear. This leaves advisors struggling to justify algorithmic recommendations in a way that builds trust and meets increasing regulatory demands for transparency and "Explainable AI."

## What It Does
The application automates the entire rebalancing lifecycle, from the initial upload of a client's holdings to the generation of a refined, forward-looking investment plan. It uses advanced mathematical models to suggest optimal asset mixes based on specific goals—such as protecting capital during a downturn or maximizing growth—while simultaneously "stress testing" these portfolios against historical crises and future market shocks. Crucially, the platform integrates artificial intelligence to translate complex data into plain-English explanations. It can pinpoint exactly why certain assets were added or removed and use simulated "market narratives" to project how a portfolio might perform over the next twelve months under different economic conditions.

## Who It Is For
This tool is built for wealth managers, investment advisors, and advisory firms who require a professional-grade interface for portfolio construction and client reporting. It empowers advisory teams to provide more personalised and responsive service by quickly generating sophisticated proposals. Additionally, risk and compliance officers benefit from the platform’s focus on transparency, ensuring that every algorithmic recommendation is auditable and clearly justified.

## Current Status
The project is currently a fully functional working prototype, having successfully transitioned from an initial proof-of-concept to a robust, containerised application. It features a stable integration with core financial data providers and a polished user interface built with modern design standards. The system has been validated for secure, scalable deployment in cloud environments, with reliable session handling and automated data validation. While the core engine is feature-complete, future development is targeted at expanding the library of market scenarios and further deepening the customisation of the artificial intelligence narratives.

## Key Outcomes
*   **Significant Time Savings:** Reduces the turnaround time for generating complex, optimised portfolio proposals from hours to minutes through a streamlined digital workflow.
*   **Enhanced Client Trust:** Delivers "Explainable AI" summaries that translate technical rebalancing decisions into clear, narrative-driven justifications that clients can easily understand.
*   **Forward-Looking Confidence:** Replaces static historical charts with dynamic, forward-looking simulations (including probabilistic "Fan Charts") that help clients visualise potential future outcomes and risks.
*   **Standardised Strategy Execution:** Ensures consistency across an advisory firm by providing pre-set strategy templates that align with firm-wide investment views while allowing for individual client constraints.
---

# 1. Business Context & Motivation

## 1.1 The Platform Gap
The Portfolio Optimiser was developed to address a critical limitation in the existing Privé Profiler journey, which currently provides only static, non-configurable optimization based on "HDI defaults." From a user perspective, the core optimization engine remains a "black box," offering no transparency into how recommended allocations are derived. Furthermore, the official Privé Optimizer API documentation is incomplete, failing to specify mandatory parameters, dependency requirements, or consistent field names (e.g., `latestPrice` vs `close-px`). This lack of transparency and technical documentation makes it difficult for advisors to trust, validate, or customize the advice given to clients.

## 1.2 Business and Product Motivation
The primary motivation for this project is to fulfill specific requirements from partner prospects for "knock-out sufficient frontier analysis." In the competitive wealth management landscape, providing a standard "one-size-fits-all" portfolio is no longer sufficient. Advisors require the ability to run multi-factor optimisations and provide Explainable AI (XAI) to their clients. 

The business impact of this gap is twofold:
- **Operational Inefficiency:** Manual portfolio rebalancing is a time-intensive process that can take hours. This tool aims to reduce that turnaround to minutes.
- **Compliance & Trust Risk:** Regulatory frameworks such as MiFID II and COBS increasingly require that algorithmic recommendations be explainable to clients and auditable by regulators. Without tools like Shapley-based attribution, firms face potential compliance exposure and a lack of client trust.

## 1.3 Stakeholders
The project serves several key stakeholder groups:
- **Relationship Managers (RMs) and Advisors:** Who need to generate insights via CIO views and LLM-powered narratives to explain portfolio changes to clients.
- **Wealth Management & Advisory Firms:** Who require automated, scalable rebalancing pipelines to serve more clients efficiently.
- **Partner Prospects:** Large institutional clients who require sophisticated "efficient frontier" analysis as a prerequisite for adoption.
- **High-Net-Worth Individuals (HNWIs):** The end clients who benefit from more tailored, transparent, and proactive investment advice.

## 1.4 Prior Attempts or Workarounds
Before this project, portfolio rebalancing was handled through "manual, time-intensive processes" or by relying on the default, non-configurable Privé Profiler journey. These workarounds were costly in terms of accuracy and time. To mitigate the "black box" nature of the API and its incomplete documentation, the development team has had to maintain a private "source of truth" reference ([[`API_REFERENCE.md`](../api/API_REFERENCE.md)](../api/API_REFERENCE.md)) and implement defensive fallback logic within the application code to handle inconsistent API responses.

## 1.5 Why Now
The timing of this project was triggered by two primary factors:
1. **Strategic Client Requirements:** Explicit demands from partner prospects for advanced frontier analysis and XAI capabilities.
2. **Technological Opportunity:** The integration of Large Language Models (LLMs) like Gemini AI and advanced attribution methods (Shapley Values) has reached a maturity level where it can be used to translate complex quantitative metrics into plain-English "CIO narratives," allowing RMs to provide higher-value insights than were previously possible.
---

# 2. Current State

## 2.1 What the Existing System Does
The existing baseline for portfolio optimization is centered around the Prive Profiler and Comparison journeys. These tools provide a standardized workflow for wealth managers to rebalance client portfolios using genetic-algorithm-based optimization engines. Users can input an existing set of holdings and receive a suggested target allocation designed to meet specific risk/return objectives.

The system currently relies on a set of hardcoded HDI (High-Density Investment) presets. It processes portfolio data by validating identifiers (ISINs and Tickers) against the Prive universe, computing historical backtests to illustrate past performance, and generating a proposed set of weightings. While the core quantitative engine is robust, the output is primarily a "take-it-or-leave-it" recommendation without granular justification or modularity.

## 2.2 Where It Falls Short
The current system suffers from three primary functional gaps and one significant technical deficiency:

1.  **Lack of Configurability:** The hardcoded HDI presets are rigid. They do not allow advisory firms or distribution partners to define their own house views or custom optimization constraints, making it difficult to adapt the tool for different market segments.
2.  **The "Black Box" Problem:** The genetic algorithm provides the "what" (the new allocation) but never the "why." There is no mechanism to explain which specific assets or factors drove the change in recommendation, leaving advisors unable to defend the proposal to sophisticated clients.
3.  **Absence of Forward-Looking Analysis:** The existing journeys are essentially backward-looking. They use historical data to "illustrate" performance but lack the capability to model forward-looking market narratives (e.g., "AI Boom" or "Rising Interest Rates") or perform Monte Carlo simulations to project future outcomes.
4.  **Fragmented API Documentation:** The underlying Prive API documentation is incomplete. It frequently omits mandatory fields, fails to define enum codes, and presents inconsistent field names (e.g., `latestPrice` vs. `close-px`), forcing developers into a cycle of manual trial-and-error to maintain integration stability.

## 2.3 Impact of the Gap
The impact of these shortfalls is felt across both the sales cycle and the regulatory compliance framework:

*   **Sales Stagnation:** The inability to demonstrate custom, narrative-driven scenarios makes it difficult to convert prospects who demand a platform that reflects their specific investment philosophy. The "hardcoded" nature of the tool limits its appeal to a narrow subset of users.
*   **Compliance and Audit Risk:** Regulatory frameworks such as MiFID II and COBS increasingly mandate that algorithmic recommendations be explainable and auditable. Without an attribution layer (XAI), firms risk non-compliance as they cannot prove the rationale behind automated advice.
*   **Operational Inefficiency:** Wealth managers spend excessive time manually "sanity-checking" algorithmic outputs. Because the system doesn't explain its choices, advisors must perform their own secondary analysis to ensure the recommendation aligns with the client's needs, defeating the purpose of an automated optimizer.
*   **Development Friction:** The documentation gaps result in significant "technical debt by discovery." Engineers spend days debugging silent failures caused by zero-price assets or undocumented field requirements, slowing down the delivery of new features.

## 2.4 Known Constraints of the Current Approach
Several technical and methodological constraints prevented the simple "patching" of the existing system:

*   **API Protocol Limitations:** The standard Prive REST endpoints are optimized for fixed-universe rebalancing. Enabling an "Open Universe" (where the optimizer can suggest assets outside the current holdings) required a fundamental shift to a GraphQL-based architecture that was not supported by the original implementation.
*   **Data Integrity Challenges:** The lack of a "source of truth" for Prive enum codes and identifier schemes (ISIN vs. Ticker) meant that any new layer had to implement a heavy defensive "normalization" logic to handle inconsistent API responses.
*   **Attribution Methodology:** Standard attribution methods like "Leave-One-Out" (LOO) are technically insufficient for financial portfolios because they ignore the high correlation between assets. A more sophisticated, computationally expensive approach (like Shapley Values) was required to provide a mathematically sound explanation of asset contribution.
*   **Infrastructure Statelessness:** The initial deployment of the optimizer to modern serverless environments (Google Cloud Run) was constrained by legacy session management that broke under horizontal scaling, requiring a pivot to a distributed Firestore-based session store.
---

# 3. Design Intent & Decisions

## 3.1 What This Project Sets Out to Achieve
The project sets out to transform the standard portfolio optimization workflow from a "black box" quantitative exercise into a transparent, narrative-driven advisory experience. The goal was to build a tool that not only suggests optimal asset weights but also provides a clear, plain-English explanation of *why* those changes are recommended and how they align with specific market views. By the time this project works correctly, a Relationship Manager (RM) should be able to input a client's portfolio, apply a complex market narrative (e.g., "AI sector boom continues despite rising rates"), and receive a validated, optimized portfolio recommendation accompanied by forward-looking Monte Carlo projections and AI-generated rationales.

## 3.2 Core Design Philosophy
The overarching philosophy is defined by the principle of **"Not a Black Box."** The design prioritizes **explainability** and **configurability** over raw performance or algorithmic complexity.
- **Explainability:** Quantitative outputs (like Shapley values) are translated into narratives using LLMs to ensure the "why" is always accessible.
- **User-Centric Configuration:** The tool provides robust validation and configurable presets to ensure the optimizer remains within realistic, tenant-specific boundaries.
- **Production-Ready Scalability:** From the start, the design accounted for deployment on Google Cloud Run, necessitating a shift from local session state to distributed persistence.
- **Narrative-Driven Analysis:** The project consciously shifted from reactive, historical benchmarking to proactive, forward-looking scenario analysis.

## 3.3 Key Design Decisions

### 3.3.1 Server-Side Session State via Firestore
- **Decision:** Utilized server-side sessions stored in Firestore instead of stateless JWTs or in-memory storage.
- **Rationale:** To support horizontal scaling on Google Cloud Run. In-memory sessions caused immediate logouts as requests hit different stateless instances. Firestore provides a distributed, persistent store that survives container restarts and scaling events.
- **Trade-off:** Adds a dependency on a external database and introduces slight latency for session lookups; requires explicit session save wrapping to prevent race conditions.
- **Source:** Git history (`c1f3d04`) and [[`_pdoc_logic.md`](../internal/pdoc/_pdoc_logic.md)](../internal/pdoc/_pdoc_logic.md).

### 3.3.2 Narrative-to-Quantitative Translation (LLM Integration)
- **Decision:** Integrated Gemini 2.0 Flash to translate qualitative market narratives into structured numerical shocks.
- **Rationale:** Manual mapping of "market views" to specific sector/region shocks is error-prone and tedious for users. An LLM allows the application to act as an "intelligent bridge" between human language and quantitative inputs.
- **Trade-off:** Reliability depends on LLM parsing; introduces a dependency on external AI API availability and potential non-deterministic outputs.
- **Source:** Q&A / [[`_pdoc_logic.md`](../internal/pdoc/_pdoc_logic.md)](../internal/pdoc/_pdoc_logic.md).

### 3.3.3 Synchronous High-Latency Processing (10m Timeouts)
- **Decision:** Increased Nginx and frontend proxy timeouts to 10 minutes (600s) to handle the Shapley attribution logic.
- **Rationale:** Calculating Shapley values requires orchestrating multiple iterative calls to the Prive API. Instead of the architectural complexity of a background task queue (e.g., Pub/Sub + WebSockets), the developer chose a simpler synchronous path to keep the MVP journey streamlined.
- **Trade-off:** Long-running requests tie up server resources and provide a less responsive "loading" experience than an asynchronous notification system.
- **Source:** Git history (`8ad1e81`) and [[`_pdoc_logic.md`](../internal/pdoc/_pdoc_logic.md)](../internal/pdoc/_pdoc_logic.md).

### 3.3.4 Transition to GraphQL for "Open Universe" Discovery
- **Decision:** Added support for GraphQL optimization endpoints alongside the original REST implementation.
- **Rationale:** The initial REST-based optimizer was restricted to reweighting existing assets ("Closed Universe"). Switching to GraphQL allowed the engine to discover and include new assets from a broader universe bounded by risk level and currency.
- **Trade-off:** Increased complexity in query construction and data normalization for the response.
- **Source:** Git history (`090393f`).

### 3.3.5 Normalized Portfolio Value ($1,000,000 Base)
- **Decision:** Standardized all optimization and stress-testing requests on a fixed $1M base value.
- **Rationale:** Prive APIs require absolute monetary values rather than percentage weights. Standardizing the base simplifies the mathematical transformations and avoids precision scaling issues with small fractional weights.
- **Trade-off:** Users see standardized values in debug logs that don't match their actual portfolio size, though frontend mapping hides this.
- **Source:** [[`_pdoc_logic.md`](../internal/pdoc/_pdoc_logic.md)](../internal/pdoc/_pdoc_logic.md).

## 3.4 Alternatives Considered and Rejected

### 3.4.1 Frontend State Management vs. Server-Side Integration Layer
- **Alternative:** Performing data normalization and Prive API orchestration directly in the React frontend.
- **Rejection:** Rejected in favor of a robust Express backend integration layer. This ensures sensitive bearer tokens stay server-side, simplifies tenant-based routing, and allows for centralized error mapping across different Prive domains.
- **Source:** Inferred from [[`_pdoc_logic.md`](../internal/pdoc/_pdoc_logic.md)](../internal/pdoc/_pdoc_logic.md) and Q&A.

### 3.4.2 In-Memory Session Storage
- **Alternative:** Standard Express-session in-memory storage.
- **Rejection:** Rejected after deployment to Cloud Run revealed "session hopping" issues where users were logged out randomly.
- **Source:** Git history (`c1f3d04`).

## 3.5 Intentional Simplifications

### 3.5.1 Fixed 12-Month Projection Horizon
The scenario projection logic is strictly limited to a 12-month horizon. This was done to simplify the UI presentation and ensure the Monte Carlo simulations remained performant without needing complex multi-year drift parameters.

### 3.5.2 Normal Distribution Assumption in Monte Carlo
The `runMonteCarlo` algorithm assumes a normal distribution of returns (using `boxMullerTransform`). While financial markets often exhibit "fat tails," this simplification was accepted for the MVP to provide a clear confidence interval visualization without the computational cost of more complex distribution models.

### 3.5.3 Exact Identifier Matches for Asset Validation
The validation service requires exact matches for asset codes. Fuzzy matching or partial search was left out of scope to prioritize the accuracy of the optimization universe.

## 3.6 Open Questions at Time of Writing
- **Native Shapley Support:** The current backend-orchestrated Shapley logic is a "workaround." It is flagged for replacement once a native Prive endpoint becomes available.
- **Ticker Scheme Consistency:** There is an outstanding note in `assets.routes.js` to confirm the specific ticker schemes required for certain Prive tenants.
- **Currency Fallbacks:** The FX rate logic currently defaults to 1 if a fetch fails; the long-term reliability of this fallback in high-volatility scenarios remains a point of review.

---
---

# 4. Architecture & Data Flow

## 4.1 System Overview
The Portfolio Optimiser is a multi-tier web application designed as a secure orchestration and proxy layer for the Prive Managers APIs. It consists of a React-based frontend (Vite/MUI) and a Node.js/Express backend, deployed as containerised services on Google Cloud Run. 

The system's primary boundary involves ingesting portfolio asset data from users, validating it against financial benchmarks, and orchestrating complex optimisation and stress-testing requests through upstream Prive services. It maintains stateful user sessions using Firestore in production and returns structured financial analytics, including XAI (Explainable AI) attribution and risk metrics, to the client-side visualisation engine (Highcharts).

## 4.2 Module Inventory

| Module | Language | Responsibility | Key Exports |
|---|---|---|---|
| `backend/src/index.js` | JavaScript | Main server entry point and route registration. | `app` |
| `backend/src/config/env.js` | JavaScript | Environment-specific API base URLs and configurations. | `BASE_URL_AUTH_PROD`, `BASE_URL_API_UAT` |
| `backend/src/config/session.js` | JavaScript | Production Firestore session configuration. | `sessionConfig` |
| `backend/src/services/priveClient.js` | JavaScript | Low-level HTTP client for upstream Prive API calls. | `priveClient` |
| `backend/src/services/optimiseService.js` | JavaScript | Logic for handling portfolio optimisation requests. | `optimise` |
| `backend/src/services/stressService.js` | JavaScript | Orchestrates stress testing and scenario analysis. | `runStressTests` |
| `backend/src/services/assetsService.js` | JavaScript | Handles asset validation and enrichment. | `processAssets` |
| `backend/src/services/authService.js` | JavaScript | Manages Prive bearer token lifecycle. | `login`, `refreshToken` |
| `backend/src/services/firestoreSessionStore.js` | JavaScript | Firestore-backed session persistence logic. | `FirestoreSessionStore` |
| `backend/src/routes/auth.routes.js` | JavaScript | Endpoints for user login and logout. | `router` |
| `backend/src/routes/assets.routes.js` | JavaScript | Endpoints for asset upload and retrieval. | `router` |
| `backend/src/routes/optimise.routes.js` | JavaScript | Endpoints for triggering portfolio optimisations. | `router` |
| `backend/src/routes/stress.routes.js` | JavaScript | Endpoints for stress-testing execution. | `router` |
| `backend/src/middleware/requireSession.js` | JavaScript | Authentication guard for protected routes. | `requireSession` |
| `backend/src/middleware/errorHandler.js` | JavaScript | Centralised error mapping and response formatting. | `errorHandler` |
| `frontend/src/main.jsx` | JavaScript (React) | Frontend entry point and React root mounting. | N/A |
| `frontend/src/App.jsx` | JavaScript (React) | Application root, routing, and theme provider. | `App` |
| `frontend/src/api/client.js` | JavaScript | Axios instance for backend communication. | `apiClient` |
| `frontend/src/pages/InputPage.jsx` | JavaScript (React) | Portfolio ingestion and asset management UI. | `InputPage` |
| `frontend/src/pages/ConfigPage.jsx` | JavaScript (React) | Optimisation strategy and preset selection UI. | `ConfigPage` |
| `frontend/src/pages/ResultsPage.jsx` | JavaScript (React) | Analytics visualisation and XAI attribution UI. | `ResultsPage` |

## 4.3 Entry Points
- **Backend Express Server:** `backend/src/index.js` listens on port 8080 (Cloud Run) or 3001 (local), initializing middleware and mounting routes under the `/api` prefix.
- **Frontend React App:** `frontend/src/main.jsx` serves as the SPA entry point, initializing the DOM and starting the React lifecycle.
- **Docker Orchestration:** 
  - `backend/Dockerfile` executes `node src/index.js`.
  - `frontend/Dockerfile` starts Nginx to serve static assets and proxy API requests.
- **Upstream API Communication:** All outbound requests to Prive are channelled through `priveClient.js`.

## 4.4 Data Flow
The system follows a sequential multi-stage pipeline:

1.  **Authentication & Ingestion:**
    - User logs in via `LoginPage.jsx` -> `authApi.js` -> `auth.routes.js`.
    - `authService.js` retrieves a Prive bearer token and stores it in the Firestore-backed `req.session.accessToken`.
2.  **Asset Upload & Validation:**
    - User uploads assets in `InputPage.jsx` -> `assetsApi.js` -> `assets.routes.js`.
    - `assetsService.js` validates the asset structure and ensures compliance with Prive requirements before temporary storage or session-binding.
3.  **Optimisation Configuration:**
    - User selects presets or custom strategies in `ConfigPage.jsx`.
    - Configuration state is passed to the backend via `optimiseApi.js` -> `optimise.routes.js`.
4.  **Orchestrated Execution:**
    - `optimiseService.js` constructs the payload for Prive, attaching the `accessToken` from the session.
    - **Timeout Management:** The request is handled with a 3-minute timeout to accommodate complex upstream calculations.
    - Upstream results are received and optionally enriched by `presetEnrichment.js`.
5.  **Attribution & Results:**
    - Detailed results are visualised in `ResultsPage.jsx`.
    - If XAI attribution is requested, `shapleyAttributionService.js` calculates contribution scores for individual assets.
6.  **Response Delivery:**
    - Final JSON payloads are returned to the frontend, where `Highcharts` renders performance and risk charts.

## 4.5 Module Dependency Graph
```
frontend/App.jsx → uses → frontend/pages/*
frontend/pages/* → uses → frontend/api/*
frontend/api/* → calls → backend/routes/*

backend/index.js → mounts → backend/routes/*
backend/routes/* → uses → backend/middleware/requireSession
backend/routes/* → calls → backend/services/*
backend/services/optimiseService → uses → backend/services/priveClient
backend/services/authService → uses → backend/services/priveClient
backend/services/priveClient → reads → backend/config/env
backend/config/session → uses → backend/services/firestoreSessionStore
```

## 4.6 Output Artefacts

| Output | Format | Contents | Produced by |
|---|---|---|---|
| **Optimisation Results** | JSON | Portfolio weights, expected returns, and risk metrics. | `optimiseService.js` |
| **XAI Attribution** | JSON | Asset-level contribution scores (Shapley values). | `shapleyAttributionService.js` |
| **Session State** | Firestore Doc | Encrypted Prive access tokens and user session metadata. | `firestoreSessionStore.js` |
| **Audit Logs** | Text/JSON | HTTP request logs for debugging and security audits. | `requestLogger.js` |
| **UI Visualisations** | SVG/Canvas | Rendered charts and graphs in the browser. | `Highcharts` (Frontend) |

## 4.7 External Dependencies

| Library | Role in this project |
|---|---|
| `express` | Core backend framework for API routing and middleware. |
| `express-session` | Session management with Firestore persistence support. |
| `react` | Frontend component framework for building the UI. |
| `@mui/material` | Design system and UI component library. |
| `highcharts` | High-performance charting engine for financial data. |
| `@google-cloud/firestore` | Database client for session and configuration storage. |
| `axios` | HTTP client for both frontend and backend API calls. |
| `helmet` | Security middleware for protecting the Express app. |
| `cors` | Middleware to enable Cross-Origin Resource Sharing. |

## 4.8 Structural Notes
- **Session Branching:** The system uses `backend/src/config/session.local.js` for development (MemoryStore) and `backend/src/config/session.js` for production (Firestore), ensuring low latency in dev while maintaining scalability in prod.
- **Proxy Trust:** The backend is configured with `app.set('trust proxy', 1)`, which is critical for correctly handling HTTPS sessions behind the Google Cloud Load Balancer.
- **Nginx Injection:** The frontend Docker image uses a custom script to inject the `BACKEND_ORIGIN` environment variable into the Nginx configuration at runtime, allowing the same build to work across multiple environments.
- **Long-Running Requests:** Optimisation routes are specifically configured to handle timeouts up to 3 minutes, reflecting the computational intensity of the underlying financial models.

---
---

# 5. Technical Implementation

## 5.1 Core Algorithms

### Asset Validation & Normalization
**What it does:** Validates user-provided asset codes against the Privé universe and extracts standardized identifiers, names, and prices.
**Implementation:** `validateAssets` (`assetsService.js`) batches requests to Privé's validation endpoint, building an `O(1)` lookup map of returned assets by identifier. It uses fallback chains (`resolveAssetName` and `extractNumericValue`) to normalize inconsistent data structures.
**Design rationale:** Privé does not use a single consistent field name across all asset types or price objects; this approach handles structural inconsistencies gracefully.
**Limitations:** Requires exact identifier matches; non-matches are simply marked invalid. FX rates default to 1 if the fetch fails.

### Portfolio Optimization (Closed Universe)
**What it does:** Generates optimal asset weights given a fixed universe of user-provided assets and pre-configured risk/fitness factors.
**Implementation:** `runOptimisation` (`optimiseService.js`) separates cash from the asset pool to enforce 100% investment. It maps current asset weights to a standard $1,000,000 portfolio value and requests a rebalanced portfolio from the Privé `OPTIMISE_V2_CODES` endpoint using factors enriched from `presetEnrichment.js`.
**Design rationale:** Ensures the optimizer allocates strictly across the selected assets ("Approach 2" where the input portfolio defines the universe).
**Limitations:** Cash handling is strictly separated and excluded from the active optimization universe.

### Portfolio Optimization (Open Universe / GraphQL)
**What it does:** Discovers and optimizes a portfolio from a broader universe bounded by risk level and asset types.
**Implementation:** `runGraphqlOptimisation` (`optimiseGraphqlService.js`) sends a structured GraphQL query filter (bounding by `risk-level`, currency, and fixed `INCLUDED_ASSET_TYPES`) to the Privé `OPTIMISE_V2_GRAPHQL` endpoint, limiting results to a maximum of 18 assets (`GRAPHQL_MAX_ASSETS`).
**Design rationale:** Allows optimization beyond user-provided assets, dynamically searching based on top-down risk constraints.
**Limitations:** Hard limits on asset count (18 max) and fixed asset types (Mutual Funds, ETFs, Equities, Bonds).

### Scenario Projection (Linear & Monte Carlo)
**What it does:** Projects future portfolio returns based on custom shocks or standard crisis scenarios.
**Implementation:** `projectScenario` (`scenarioService.js`) coordinates parallel calls to `getClassification` and `runStressTest`. `calculateFactorImpacts` maps shock categories to portfolio exposures. For linear projection, it computes a simple path using `calculateLinearPath`. For Monte Carlo, it uses `runMonteCarlo` applying Geometric Brownian Motion (`boxMullerTransform`) over a 12-month horizon to generate confidence intervals.
**Design rationale:** Combines historic baseline returns with forward-looking shocks (either preset or derived by LLM) to provide a localized, explainable projection.
**Limitations:** Monte Carlo assumes a normal distribution of returns; both models use a fixed 12-month projection horizon.

### Narrative to Quantitative Translation
**What it does:** Converts plain-text qualitative market views into structured numerical shocks.
**Implementation:** `translateNarrativeToShocks` (`geminiService.js`) sends the text and available Privé classification categories to Gemini 2.0 Flash with a prompt demanding a structured JSON response mapping categories to percentage decimal shocks.
**Design rationale:** Automates the mapping of complex, subjective narratives into the specific numerical inputs required by the scenario projection service.
**Limitations:** Relies entirely on LLM parsing reliability; subject to a hard-coded 15-second network timeout.

## 5.2 Business Rules

| Rule | Location (file:function) | Condition | Behaviour when met | Behaviour when not met |
|---|---|---|---|---|
| Session Expiry Enforcement | `middleware/requireSession.js` | `req.session.accessToken` is absent | Rejects request with `401 SESSION_EXPIRED` | Proceeds to next middleware |
| Tenant API Routing | `routes/auth.routes.js`, `priveClient.js` | Successful login | Writes `tenant` to session; injected into all API endpoints | N/A |
| Cash Exclusion in Optimization | `optimiseService.js` | Asset type is 'CASH' or identifier is 'CASH' | Filters asset out of optimization universe universe array | Asset remains in optimization pool |
| Preset Fitness Enforcement | `presetEnrichment.js` | Preset ID is recognized | Maps to static array of fitness factors/weights capped at RISK_CAP=5 | Throws `400 INVALID_REQUEST` |

## 5.3 Data Transformations

*   **Asset Object Normalization:** `assetsService.js` transforms varied Privé price fields (`close-px`, `price`, `value`, nested objects) and name fields (`name`, `assetName`, localized `en` objects) into standard `{ assetName, price, fxRate, currency }` objects.
*   **Classification Mapping:** `classificationService.js` translates frontend-requested breakdown strings (e.g., `regional-assetclass-breakdown`) into strict Privé enum equivalents (e.g., `Region-AssetClassAdjusted`) using the `BREAKDOWN_MAPPING` dictionary.
*   **Weight to Value Normalization:** `optimiseService.js` and `optimiseGraphqlService.js` transform percentage weights (0-100) into actual monetary values against a fixed `NORMALIZED_PORTFOLIO_VALUE` of $1,000,000 to satisfy Privé API requirements.
*   **Prive Response Weight Mapping:** Privé returns portfolio weights as decimals (0-1). The optimization services multiply these by 100 before returning them to the frontend.
*   **Stress Portfolio Mapping:** `stressService.js` transforms holdings into Privé's rigid schema, explicitly mapping `type === 'CASH'` to specific `asset-code-scheme: cash` layouts and injecting fallback currencies.
*   **GraphQL Universe Currencies:** `optimiseGraphqlService.js` extracts unique currencies from the user's current portfolio and merges them with a hardcoded list of `DEFAULT_CURRENCIES` to form the GraphQL search universe constraints.

## 5.4 Quantitative Methods

### Portfolio Value Normalisation
**Formula:** $Value_i = \frac{w_i}{\sum w_j} \times V_{normalized}$
**Implementation:** `scorePortfolio` (`attributionService.js`), `scoreCoalition` (`shapleyAttributionService.js`), `runOptimisation` (`optimiseService.js`)
**Inputs:** Percentage weights $w_i$ and a constant `NORMALIZED_PORTFOLIO_VALUE` ($1,000,000$).
**Output:** Absolute currency values used to standardise API payloads for fitness scoring and optimisation.
**Assumptions and limitations:** Uses a fixed $1M base to simplify math and avoid precision scaling issues with small fractional weights.

### Leave-One-Out (LOO) Attribution
**Formula:** $\Delta_{LOO} = Fitness(S_{proposed}) - Fitness(S_{proposed} \setminus \{i\})$
**Implementation:** `computeDeltas` in `attributionService.js`
**Inputs:** Fitness score of the proposed portfolio and the score of a portfolio where one change (add/remove/reweight) is reverted.
**Output:** The delta contribution of a specific asset change to the overall portfolio fitness improvement.
**Assumptions and limitations:** Measures individual contribution by observing the impact of removal; assumes linear independence for simple delta calculation.

### Approximate Shapley Value
**Formula:** $\phi_i = \frac{1}{K} \sum_{k=1}^{K} (Fitness(C_k \cup \{i\}) - Fitness(C_k))$
**Implementation:** `runShapleyAttribution` in `shapleyAttributionService.js`
**Inputs:** $K$ random permutations of changes (default $K=5$).
**Output:** The average marginal contribution of change $i$ across all steps, ensuring fair attribution in the presence of non-linear factor interactions.
**Assumptions and limitations:** With $K=5$, it is a rough approximation and results may fluctuate slightly between executions.

### Linear Factor Impact
**Formula:** $\Delta R = \sum (Shock_k \times Exposure_k)$
**Implementation:** `calculateFactorImpacts` in `scenarioService.js`
**Inputs:** Shock value decimal (e.g., -0.10 for -10%) and factor exposure (asset class or sector weight).
**Output:** Estimated return delta based on external events.
**Assumptions and limitations:** Ignores correlations between factors and second-order return effects (first-order linear approximation).

### Geometric Brownian Motion (GBM)
**Formula:** $dS_t = S_t(\mu dt + \sigma Z \sqrt{dt})$
**Implementation:** `runMonteCarlo` in `scenarioService.js`
**Inputs:** Adjusted mean return $\mu$, volatility $\sigma$, random normal $Z$, and time step $dt=1/12$.
**Output:** Simulated price paths for future portfolio value trajectories.
**Assumptions and limitations:** Assumes mean return and volatility remain constant over 12 months; ignores volatility regimes or mean-reverting behavior.

### Box-Muller Transform
**Formula:** $Z = \sqrt{-2 \ln U_1} \cos(2\pi U_2)$
**Implementation:** `boxMullerTransform` in `scenarioService.js`
**Inputs:** Two uniform random numbers $U_1, U_2 \in [0, 1]$.
**Output:** $Z \sim N(0, 1)$, a normally distributed shock.
**Assumptions and limitations:** Relies on the quality of the underlying uniform random number generator.

## 5.5 Parameters and Configuration

| Name | Type | Default | Source | Description | Sensitivity |
|---|---|---|---|---|---|
| `MAX_ASSETS` | number | 11 | `backend/src/constants/optimiserDefaults.js` | Default max assets for optimisation | HIGH |
| `MIN_ALLOCATION` | number | 0.02 | `backend/src/constants/optimiserDefaults.js` | Default min allocation | HIGH |
| `ROUNDING` | boolean | true | `backend/src/constants/optimiserDefaults.js` | Rounding enabled | HIGH |
| `VCPF_RISK_CAP` | number | 0.15 | `backend/src/constants/optimiserDefaults.js` | VCPF risk cap | HIGH |
| `MRVF_MULTIPLIER` | number | 2.75 | `backend/src/constants/optimiserDefaults.js` | MRVF multiplier | HIGH |
| `ACCOUNT_ID` | string | 'SA' | `backend/src/constants/optimiserDefaults.js` | Account ID for Prive | HIGH |
| `PRESETS` | array | [...] | `frontend/src/config/presets.js` | Optimisation preset definitions | HIGH |
| `Risk Cap` | number | 5 | [[`OPTIMISER_PRESETS.md`](../design/OPTIMISER_PRESETS.md)](../design/OPTIMISER_PRESETS.md) | Default risk cap for fitness functions | HIGH |
| `ISIN_REGEX` | regex | `/^[A-Z]{2}[A-Z0-9]{9}[0-9]$/` | `frontend/src/constants/validation.js` | ISIN validation regex | HIGH |
| `WEIGHT_TOTAL_TARGET` | number | 100 | `frontend/src/constants/validation.js` | Target total portfolio weight | HIGH |
| `SESSION_SECRET` | string | - | Environment Variable | Cryptographic secret for signing session cookies | HIGH |
| `GEMINI_API_KEY` | string | - | Environment Variable | API key for generating narrative insights | HIGH |
| `NORMALIZED_PORTFOLIO_VALUE` | number | 1,000,000 | [[`_pdoc_quant.md`](../internal/pdoc/_pdoc_quant.md)](../internal/pdoc/_pdoc_quant.md) Section 6 | Nominal base for currency conversions | LOW |
| `DEFAULT_SAMPLE_COUNT` | number | 5 | [[`_pdoc_quant.md`](../internal/pdoc/_pdoc_quant.md)](../internal/pdoc/_pdoc_quant.md) Section 6 | Permutations for Shapley approximation | MED |
| `SP500_ISIN` | string | 'US78378X1072' | `backend/src/constants/benchmarks.js` | Benchmark ISIN for comparison | MED |

### HIGH Sensitivity Parameters

**Optimiser Constraints (MAX_ASSETS, MIN_ALLOCATION, VCPF_RISK_CAP):** Modifying these hardcoded constants affects the underlying constraint models sent to Prive. If they are changed out of step with what the Prive API allows, it will trigger immediate request validation failures.

**PRESETS & Risk Cap:** The optimisation presets (fitness functions and their weights) directly dictate the engine's behaviour. If these are incorrectly configured, the Prive API will return sub-optimal, irrelevant, or `INFEASIBLE` results. There is a risk of configuration drift between `presets.js` and [[`OPTIMISER_PRESETS.md`](../design/OPTIMISER_PRESETS.md)](../design/OPTIMISER_PRESETS.md).

**SESSION_SECRET & GEMINI_API_KEY:** These environment variables are critical for security and functionality. Failure to provide them will crash backend services, while exposure compromises session integrity or incurs unexpected billing via unauthorized LLM API usage.

**ISIN_REGEX:** This regex is the primary validation gate for asset input. Incorrect modification will block users from entering valid assets, rendering the application unusable.

## 5.6 Edge Cases and Error Handling

*   **Infeasible Portfolios:** Explicitly caught in `errorMapper.js` (detecting HTTP 409 or the word "infeasible" in the response body) and mapped to a user-friendly `422 INFEASIBLE` error.
*   **Network Timeouts:** Axios `ECONNABORTED` is caught in `errorMapper.js` and mapped to `504 TIMEOUT`. The frontend `client.js` implements an `AbortController` timeout, converting `AbortError` to a `TIMEOUT` code.
*   **LLM Parsing Failures:** `geminiService.js` wraps `JSON.parse(text)` in a try-catch block, throwing a structured error if the LLM fails to return valid JSON for the narrative shocks.
*   **Session Race Conditions:** `auth.routes.js` explicitly wraps `req.session.save()` in a Promise to wait for Firestore writes to complete before returning 200 OK, preventing immediate subsequent requests from finding an empty session store.
*   **Missing Timeframes:** `scenarioService.js:pickBestStats` searches through a prioritized array (`TIMEFRAME_PRIORITY`) to find the longest available historical performance window, gracefully degrading if longer timeframes are missing.

## 5.7 Design Decisions in Code

*   **Session-Based State over JWTs:** The application uses server-side sessions (`req.session`) to store the Privé `accessToken` and `tenant`. This keeps sensitive tokens out of the browser and simplifies tenant injection across backend API calls.
*   **Centralized Client Factories:** `priveClient.js` ensures consistent header injection, bearer tokens, timeouts, and base URLs based on the user's `environment` without repeating boilerplate in every service.
*   **Normalized Portfolio Value ($1M):** Privé APIs require absolute values rather than percentages; standardizing on a $1M base simplifies math and avoids precision scaling issues with small fractional weights.
*   **Client-Side Thin Wrapper:** `frontend/src/api/client.js` minimizes third-party bundle dependencies while providing centralized 401 interception, timeout handling, and offline detection.
*   **Error Mapping Separation:** `errorMapper.js` isolates Privé error translation from the route logic, standardizing error contracts sent to the frontend UI.
---

# 6. Outcomes & Appendix

## 6.1 What This Project Produces
The Portfolio Optimiser delivers a functional prototype deployed on Google Cloud Run, providing a full-stack solution for investment professionals. Key outputs include:
*   **Web Application:** A responsive React-based interface allowing users to upload portfolios, configure strategies, and visualize outcomes in both light and dark modes.
*   **XAI Attribution Layer:** A sophisticated explanation engine that decomposes portfolio improvements into specific asset contributions using Shapley values and narrative insights.
*   **Forward-Looking Scenario Tool:** A module that replaces static historical charts with dynamic projections, generating both linear impact "waterfalls" and Monte Carlo distribution "fan charts."
*   **Standardized API Documentation:** An improved [[`API_REFERENCE.md`](../api/API_REFERENCE.md)](../api/API_REFERENCE.md) that bridges the gap between Privé's core endpoints and the specific data structures required for this application.
*   **Structured Analytical Data:** Normalised JSON payloads representing validated asset universes, fitness scores, and statistical attribution results, ready for downstream reporting or further analysis.

## 6.2 Success Criteria
The prototype's success is measured against the following technical and functional benchmarks:
*   **Asset Validation:** 100% of assets uploaded must be correctly mapped to the Privé tenant universe or flagged with clear error messaging.
*   **Performance:** A full optimisation run, including statistical attribution and narrative generation, must complete within a 10-minute timeout window (typically averaging under 3 minutes).
*   **Quantitative Accuracy:** Fitness scores and attribution deltas must match the underlying Privé engine results precisely.
*   **Qualitative Alignment:** Narrative insights generated by Gemini AI must be consistent with professional CIO views and logically follow from the quantitative shocks applied.
*   **Infrastructure Stability:** Successful handling of stateless session management across multiple Cloud Run instances via Firestore.

## 6.3 Validation Approach
The application's logic and outputs have been validated through a multi-stage process:
*   **Tenant Validation:** Assets were validated against live Privé tenant data to ensure ISIN/Ticker mapping accuracy.
*   **Cross-Reference Testing:** Optimization results and fitness scores were compared side-by-side with raw Privé API responses to verify normalization logic.
*   **Permutation Stability:** Shapley attribution was tested with varying sample counts (K=5 to K=10) to balance variance against latency.
*   **Narrative Review:** LLM-generated market shocks and explanations were manually reviewed for financial logic and consistency with the "Forward-Looking Scenario" intent.
*   **Infrastructure Stress Test:** The deployment was validated on Cloud Run to ensure Firestore session persistence and Nginx proxy stability under long-running requests.

## 6.4 Known Limitations
The following limitations are present in the current prototype:
*   **Deliberate Simplifications:**
    *   **Linear Shocks:** The Waterfall view assumes a first-order linear impact, ignoring cross-factor correlations and non-linear second-order effects.
    *   **Normal Distribution:** Monte Carlo simulations assume a standard normal distribution (GBM), which may not account for "fat-tail" events or volatility clustering.
*   **Not-Yet-Implemented Features:**
    *   **Native Attribution:** Shapley logic is currently a backend workaround involving multiple iterative API calls; a native Privé endpoint is preferred for production.
    *   **Dynamic Preset Sync:** [[`OPTIMISER_PRESETS.md`](../design/OPTIMISER_PRESETS.md)](../design/OPTIMISER_PRESETS.md) requires manual synchronization with the code, posing a risk of configuration drift.
*   **Known Constraints:**
    *   **Exact Matching:** The asset validator requires exact identifier matches; it lacks "fuzzy" search or alias resolution.
    *   **Fixed Horizon:** Projections are strictly limited to a 12-month forward-looking window.
    *   **Cash Handling:** The optimizer excludes cash from the active universe, forcing 100% investment.

## 6.5 Recommended Next Steps
To move this prototype toward production readiness, the following steps are recommended:
*   **Scenario Expansion:** Add more preset forward-looking scenarios covering geopolitical conflicts, specific economic regimes (e.g., stagflation), and thematic shifts.
*   **Prompt Engineering:** Refine Gemini system prompts to improve the precision of narrative-to-shock translations and ensure consistent JSON parsing.
*   **Data Cleanup:** Perform a comprehensive audit and cleanup of the asset universe in the UAT environment to resolve unconfirmed identifiers (e.g., `SP500_ISIN`).
*   **API Transition:** Replace the iterative Shapley workaround with a native Privé attribution endpoint once available to reduce latency and infrastructure load.
*   **Schema Confirmation:** Finalize and confirm the `TICKER` scheme implementation with Privé to ensure robust validation across all asset classes.

---

## Appendix A: Full Parameter Reference

| name | type | default value | source | description | sensitivity |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `SP500_ISIN` | string | 'US78378X1072' | hardcoded (`backend/src/constants/benchmarks.js`) | Benchmark ISIN for comparison | MED |
| `SESSION_EXPIRED` | string | 'SESSION_EXPIRED' | hardcoded (`backend/src/constants/errorCodes.js`, `frontend/src/constants/errorCodes.js`) | Error code string | LOW |
| `TIMEOUT` | string | 'TIMEOUT' | hardcoded (`backend/src/constants/errorCodes.js`, `frontend/src/constants/errorCodes.js`) | Error code string | LOW |
| `INFEASIBLE` | string | 'INFEASIBLE' | hardcoded (`backend/src/constants/errorCodes.js`, `frontend/src/constants/errorCodes.js`) | Error code string | LOW |
| `INVALID_REQUEST` | string | 'INVALID_REQUEST' | hardcoded (`backend/src/constants/errorCodes.js`, `frontend/src/constants/errorCodes.js`) | Error code string | LOW |
| `UPSTREAM_ERROR` | string | 'UPSTREAM_ERROR' | hardcoded (`backend/src/constants/errorCodes.js`, `frontend/src/constants/errorCodes.js`) | Error code string | LOW |
| `OFFLINE` | string | 'OFFLINE' | hardcoded (`backend/src/constants/errorCodes.js`, `frontend/src/constants/errorCodes.js`) | Error code string | LOW |
| `STRESS_ERROR` | string | 'STRESS_ERROR' | hardcoded (`backend/src/constants/errorCodes.js`) | Error code string | LOW |
| `CLASSIFICATION_ERROR` | string | 'CLASSIFICATION_ERROR' | hardcoded (`backend/src/constants/errorCodes.js`) | Error code string | LOW |
| `SCENARIO_ERROR` | string | 'SCENARIO_ERROR' | hardcoded (`backend/src/constants/errorCodes.js`) | Error code string | LOW |
| `MAX_ASSETS` | number | 11 | hardcoded (`backend/src/constants/optimiserDefaults.js`) | Default max assets cap | HIGH |
| `MIN_ALLOCATION` | number | 0.02 | hardcoded (`backend/src/constants/optimiserDefaults.js`) | Default min allocation | HIGH |
| `MAX_CONCENTRATION` | number | 0.30 | hardcoded (`backend/src/constants/optimiserDefaults.js`) | Maximum allowed concentration | HIGH |
| `ROUNDING` | boolean | true | hardcoded (`backend/src/constants/optimiserDefaults.js`) | Rounding enabled | HIGH |
| `VCPF_RISK_CAP` | number | 0.15 | hardcoded (`backend/src/constants/optimiserDefaults.js`) | VCPF risk cap | HIGH |
| `MRVF_MULTIPLIER` | number | 2.75 | hardcoded (`backend/src/constants/optimiserDefaults.js`) | MRVF multiplier | HIGH |
| `ACCOUNT_ID` | string | 'SA' | hardcoded (`backend/src/constants/optimiserDefaults.js`) | Account ID for Prive | HIGH |
| `PRESET_SCENARIOS` | object | {...} | hardcoded (`backend/src/constants/presetScenarios.js`) | Pre-set forward-looking scenarios with market shocks | HIGH |
| `OPTIMISE_ONLY` | function | (tenant) => `...` | hardcoded (`backend/src/constants/priveEndpoints.js`) | Prive API endpoint path | HIGH |
| `OPTIMISE_V2_CODES` | function | (tenant) => `...` | hardcoded (`backend/src/constants/priveEndpoints.js`) | Prive API endpoint path | HIGH |
| `OPTIMISE_V2_GRAPHQL` | function | (tenant) => `...` | hardcoded (`backend/src/constants/priveEndpoints.js`) | Prive API endpoint path | HIGH |
| `VALIDATE_CODES` | function | (tenant) => `...` | hardcoded (`backend/src/constants/priveEndpoints.js`) | Prive API endpoint path | HIGH |
| `FITNESS_SCORES` | function | (tenant) => `...` | hardcoded (`backend/src/constants/priveEndpoints.js`) | Prive API endpoint path | HIGH |
| `FX_RATE` | function | (tenant, pair) => `...` | hardcoded (`backend/src/constants/priveEndpoints.js`) | Prive API endpoint path | HIGH |
| `ASSET_PRICE_BATCH` | function | (tenant) => `...` | hardcoded (`backend/src/constants/priveEndpoints.js`) | Prive API endpoint path | HIGH |
| `STRESS_SCENARIO` | function | (tenant) => `...` | hardcoded (`backend/src/constants/priveEndpoints.js`) | Prive API endpoint path | HIGH |
| `PORTFOLIO_CLASSIFICATION` | function | (tenant) => `...` | hardcoded (`backend/src/constants/priveEndpoints.js`) | Prive API endpoint path | HIGH |
| `DEFAULT_TIMEOUT_MS` | number | 30000 | hardcoded (`backend/src/constants/timeouts.js`, `frontend/src/constants/timeouts.js`) | Standard API call timeout | MED |
| `OPTIMISER_TIMEOUT_MS` | number | 600000 | hardcoded (`backend/src/constants/timeouts.js`, `frontend/src/constants/timeouts.js`) | Optimiser run timeout | MED |
| `IDLE_TIMEOUT_MS` | number | 1800000 | hardcoded (`backend/src/constants/timeouts.js`, `frontend/src/constants/timeouts.js`) | Session idle window timeout | MED |
| `CURRENT_TEAL` | string | '#00557e' | hardcoded (`frontend/src/constants/chartColors.js`) | Chart line color | LOW |
| `OPTIMISED_GREEN` | string | '#60c094' | hardcoded (`frontend/src/constants/chartColors.js`) | Chart line color | LOW |
| `SP500_GREY` | string | '#9CA3AF' | hardcoded (`frontend/src/constants/chartColors.js`) | Chart line color | LOW |
| `HIGHER_IS_BETTER` | string | 'higher' | hardcoded (`frontend/src/constants/metrics.js`) | Metric direction indicator | LOW |
| `LOWER_IS_BETTER` | string | 'lower' | hardcoded (`frontend/src/constants/metrics.js`) | Metric direction indicator | LOW |
| `ISIN_REGEX` | regex | `/^[A-Z]{2}[A-Z0-9]{9}[0-9]$/` | hardcoded (`frontend/src/constants/validation.js`) | ISIN validation regex | HIGH |
| `WEIGHT_TOTAL_TARGET` | number | 100 | hardcoded (`frontend/src/constants/validation.js`) | Target total portfolio weight | HIGH |
| `PALETTE` | object | {...} | hardcoded (`frontend/src/config/designTokens.js`) | Core design color palette | LOW |
| `LIGHT_PALETTE` | object | {...} | hardcoded (`frontend/src/config/designTokens.js`) | Light theme specific palette | LOW |
| `DARK_PALETTE` | object | {...} | hardcoded (`frontend/src/config/designTokens.js`) | Dark theme specific palette | LOW |
| `CHART_PALETTE` | array | [...] | hardcoded (`frontend/src/config/designTokens.js`) | Array of fallback chart colors | LOW |
| `createAppTheme` | function | - | hardcoded (`frontend/src/config/muiTheme.js`) | MUI theme generator | LOW |
| `PRESETS` | array | [...] | hardcoded (`frontend/src/config/presets.js`) | Optimisation preset definitions | HIGH |
| `ROUTES` | object | {...} | hardcoded (`frontend/src/config/routes.js`) | Application route paths | MED |
| `Risk Cap` | number | 5 | hardcoded ([[`OPTIMISER_PRESETS.md`](../design/OPTIMISER_PRESETS.md)](../design/OPTIMISER_PRESETS.md)) | Default risk cap for fitness functions | HIGH |

## Appendix B: Environment Variables

| variable name | used in (file/function) | purpose | required or optional | example value if available |
| :--- | :--- | :--- | :--- | :--- |
| `PORT` | `backend/src/index.js` | Defines the server listening port | optional | 3001 |
| `SESSION_SECRET` | `backend/src/config/session.js`, `backend/src/config/session.local.js` | Cryptographic secret for signing session cookies | required | replace-with-a-long-random-string |
| `FRONTEND_ORIGIN` | `backend/src/index.js` | Specifies the allowed CORS origin | optional (required in prod) | http://localhost:5173 |
| `NODE_ENV` | `backend/src/index.js`, `backend/src/config/session.js` | Sets execution environment (development/production) | optional | development |
| `FIRESTORE_EMULATOR_HOST` | `backend/src/services/firestoreSessionStore.js` (implicitly via SDK) | Points the Firebase SDK to a local emulator | optional | localhost:8081 |
| `GOOGLE_CLOUD_PROJECT` | `backend/src/services/firestoreSessionStore.js` | Sets the GCP Project ID for Firestore | optional | portfolio-optimiser-local |
| `FIRESTORE_DATABASE_ID` | `backend/src/services/firestoreSessionStore.js` | Specifies the named Firestore database instance | optional | portfolio-optimiser |
| `GEMINI_API_KEY` | `backend/src/services/geminiService.js` | API key for generating narrative insights | required | your-gemini-api-key-here |
| `VITE_API_BASE_URL` | `frontend/src/api/client.js` | Overrides the relative API path for production builds | optional | https://your-backend-run-url |

## Appendix C: External Dependencies

| Library | Role in this project |
|---|---|
| `express` | Backend web framework |
| `react` | Frontend UI library |
| `dotenv` | Environment variable management |
| `axios` | HTTP client for backend Privé API calls |
| `express-session` | Server-side session management |
| `connect-firestore` | Firestore-backed session storage |
| `firebase-admin` | Google Cloud Firestore integration |
| `@google/generative-ai` | Gemini 2.0 Flash integration for narrative generation |
| `material-ui` | Core UI component library |
| `nginx` | Reverse proxy and static file server |

## Appendix D: Formula Reference

| Formula | LaTeX | Implemented in | See section |
|---|---|---|---|
| Portfolio Value Normalisation | $Value_i = \frac{w_i}{\sum w_j} \times V_{normalized}$ | `optimiseService.js` | 5.4 |
| Leave-One-Out (LOO) Attribution | $\Delta_{LOO} = Fitness(S_{proposed}) - Fitness(S_{proposed} \setminus \{i\})$ | `attributionService.js` | 5.4 |
| Approximate Shapley Value | $\phi_i = \frac{1}{K} \sum_{k=1}^{K} (Fitness(C_k \cup \{i\}) - Fitness(C_k))$ | `shapleyAttributionService.js` | 5.4 |
| Linear Factor Impact | $\Delta R = \sum (Shock_k \times Exposure_k)$ | `scenarioService.js` | 5.4 |
| Geometric Brownian Motion (GBM) | $dS_t = S_t(\mu dt + \sigma Z \sqrt{dt})$ | `scenarioService.js` | 5.4 |
| Box-Muller Transform | $Z = \sqrt{-2 \ln U_1} \cos(2\pi U_2)$ | `scenarioService.js` | 5.4 |

## Appendix E: Outstanding TODOs

| Item | Source (file or commit) | Priority |
|---|---|---|
| confirm TICKER scheme with Prive | `backend/src/routes/assets.routes.js` | MED |
| Transition to native Prive attribution endpoint | [[`XAI-ATTRIBUTION-BRIEF.md`](../design/XAI-ATTRIBUTION-BRIEF.md)](../design/XAI-ATTRIBUTION-BRIEF.md) / [[`_pdoc_git.md`](../internal/pdoc/_pdoc_git.md)](../internal/pdoc/_pdoc_git.md) | HIGH |
| Confirm SP500_ISIN against Prive universe | [[`_pdoc_config.md`](../internal/pdoc/_pdoc_config.md)](../internal/pdoc/_pdoc_config.md) | MED |
| Automate preset sync between MD and code | [[`_pdoc_config.md`](../internal/pdoc/_pdoc_config.md)](../internal/pdoc/_pdoc_config.md) | LOW |
| Add more geopolitical preset scenarios | [USER_PROMPT_SUMMARY] | MED |
| Refine Gemini narrative-to-shock prompts | [USER_PROMPT_SUMMARY] | HIGH |
| Asset universe cleanup in UAT | [USER_PROMPT_SUMMARY] | HIGH |
