# LYRA V2 — ENGINEERING CONSTITUTION

## 1. ROLE

You are the coding agent working on the LYRA V2 repository.

LYRA V2 is a professional financial web application.

You must follow this document and the repository architecture.

Do not invent product features.
Do not change locked product decisions without explicit approval.
Do not perform destructive repository operations unless explicitly requested.

Before significant implementation work:
1. inspect the relevant repository state,
2. understand existing architecture,
3. explain the planned change,
4. implement only the approved scope,
5. run appropriate validation.

---

## 2. CORE TECHNOLOGY

Frontend:
- React
- TypeScript

Backend:
- Node.js
- TypeScript

Database:
- PostgreSQL

API:
- REST under /api/v1

Realtime:
- authenticated WebSocket

Architecture:
- monorepo

Development environment:
- GitHub Codespaces

Package manager:
- npm

Node:
- Node.js 24 LTS compatible

Use strict TypeScript.

---

## 3. REPOSITORY PRINCIPLES

Expected architecture:

apps/
  web/
  api/

packages/
  types/
  config/
  validation/
  ui/

workers/

database/

tests/

docs/

scripts/

.github/

.devcontainer/

Keep frontend, backend, shared types, database, workers and tests logically separated.

Avoid unnecessary dependencies.

Avoid giant files.

Avoid duplicated business logic.

Do not create fake production implementations and present them as complete.

---

## 4. USER AND ADMIN ARE DISTINCT

THIS IS A CRITICAL LOCKED DECISION.

LYRA has two distinct account domains:

1. USER
2. ADMIN

ADMIN IS NOT A NORMAL USER ROLE THAT CAN BE SELECTED OR ENABLED THROUGH NORMAL USER REGISTRATION.

Normal user authentication and admin authentication must have separate application flows.

User routes are under normal user application space.

Admin routes are under /admin.

Admin must never be created through normal public/invite user registration.

A normal user must never be able to promote themselves to admin.

Frontend route separation is required.

Backend authorization is mandatory.

Never rely only on frontend route hiding.

Admin requires stronger security controls.

---

## 5. USER APPLICATION

The user application will eventually contain:

- Login
- Register
- 2FA
- Dashboard
- Crypto
- Borsa İstanbul
- ABD Borsası
- Fırsatlar
- LYRA AI
- Portföy
- Açık Pozisyonlar
- LYRA Performansı
- Bildirimler
- Ayarlar

Do not implement all of these unless explicitly instructed.

---

## 6. ADMIN APPLICATION

The admin application will eventually contain:

- Admin Login
- Admin 2FA
- Admin Dashboard / Command Center
- Users
- Invitations
- Roles & Permissions
- System Health
- Exchange Monitoring
- Opportunity Engine Status
- LYRA AI Status
- Serbest Kasa System Status
- Audit Logs
- System Events
- Admin Settings

Admin has a separate visual experience from normal users.

Do not merge admin screens into the normal user dashboard.

---

## 7. CRYPTO

Crypto is the core LYRA market.

Supported trading exchanges:
- Binance
- OKX

Only one trading exchange may be active at a time.

The active trading exchange is the only exchange allowed for execution.

Market data may eventually be collected from supported exchanges independently of the active execution exchange.

---

## 8. EXCHANGE SWITCH RULE

If LYRA has open LYRA-managed positions on the current active trading exchange:

EXCHANGE SWITCH MUST BE BLOCKED.

Example:

Binance active
+
LYRA position open
=
OKX switch blocked

Positions must be closed/ended and confirmed before switching the active trading exchange.

This rule must be enforced in backend business logic, not only in the UI.

---

## 9. EXCHANGE PERMISSIONS

LYRA may use trading permissions required for its trading functions.

LYRA must NOT use:
- withdrawal permission
- transfer permission

Never implement withdrawal or transfer functionality.

Never expose exchange API secrets to the frontend.

Never commit exchange secrets to Git.

Never print secrets in logs.

---

## 10. CRYPTO HOLDINGS

Crypto holdings must not be manually entered.

Crypto balances and positions come from the active connected exchange.

Do not create fake/manual crypto portfolio balances.

---

## 11. BIST

BIST is a supporting market.

LYRA does not act as a BIST broker.

BIST transactions are manually recorded based on the user's real executed transaction data.

BIST source currency:
TRY / TL

User-entered real execution price and quantity must be preserved.

Do not replace the user's actual execution price with a market-source price.

Support future portfolio treatment of:
- dividends
- bonus shares
- splits
- relevant corporate actions

---

## 12. USA EQUITIES

USA equities are a supporting market.

LYRA does not execute broker orders for USA equities.

Transactions are manually recorded based on real executed transaction data.

USA source currency:
USD

Preserve the user's real execution price and quantity.

Support future portfolio treatment of:
- dividends
- bonus shares
- splits
- relevant corporate actions

---

## 13. FUTURES

Futures belongs inside the Crypto area.

Do not create Futures as a separate top-level market.

Supported directions:
- Long
- Short

The product UI may expose leverage selection up to 100x.

Actual execution may NEVER exceed:
- user limit
- exchange limit
- instrument limit
- system limit

All applicable limits must be respected.

---

## 14. FUTURES SAFETY

Initial automated futures architecture should use:

- isolated margin
- one-way / net position mode
- reduce-only exits

Do not introduce hedge-mode complexity unless explicitly approved.

Reverse should follow:

close current position
-> confirm close
-> revalidate new opportunity
-> open opposite direction

Do not blindly reverse without confirmation and revalidation.

---

## 15. OPPORTUNITY ENGINE

The Opportunity Engine detects potential trading opportunities.

It must eventually consider multiple relevant inputs and may include:
- market structure
- momentum
- volatility
- volume/liquidity
- support/resistance
- multi-timeframe context
- futures-related market information when applicable
- entry/stop geometry
- risk/reward

No single indicator should automatically equal a trade signal.

Do not invent the final strategy algorithm unless explicitly approved.

---

## 16. OPPORTUNITY RULES

An opportunity should contain structured information such as:

- symbol
- exchange
- direction
- entry zone
- stop loss
- target/scenario
- leverage
- risk
- R/R
- rationale
- invalidation
- timestamps
- lifecycle state

Opportunity and actual execution are separate concepts.

Approval and execution are separate concepts.

---

## 17. MINIMUM R/R

An opportunity must satisfy the configured minimum R/R requirement before execution.

The exact final numerical threshold must not be invented if it has not been explicitly defined.

Never hardcode an unapproved minimum R/R value.

---

## 18. USER APPROVAL

Normal opportunity mode supports:

- Approve
- Reject

User approval does not override system safety rules.

Before execution after approval, the system must re-check:
- current market conditions
- risk conditions
- R/R
- user limits
- account state
- position conflicts
- exchange state
- permissions

An old opportunity must never be blindly executed after conditions have materially changed.

---

## 19. RISK ENGINE

The Risk Engine is higher priority than AI suggestions.

System rules and risk rules override AI decisions.

Risk logic must eventually consider:
- entry
- stop
- position size
- leverage
- allocated capital
- exposure
- exchange limits
- user limits
- current positions
- current losses
- drawdown
- R/R

Never invent risk values that are not explicitly approved.

---

## 20. SERBEST KASA

Serbest Kasa is OFF by default.

Serbest Kasa is not a separate real wallet.

It represents an allocation/control layer over the active exchange account.

It may include:
- allocation limit
- risk settings
- leverage limit
- Spot permission
- Futures permission
- automation status

It must never access more authority than explicitly allowed.

---

## 21. MASTER KILL SWITCH

LYRA has a Master Kill Switch.

When active:
new automated entries must be blocked.

Do not treat the kill switch as database deletion or as automatic closure of every position.

Existing position management must be handled separately and safely.

---

## 22. CONNECTION LOSS

If the active exchange connection is lost:

NEW ENTRIES MUST STOP.

Do not trade blindly on stale exchange/account information.

Existing positions must be handled using the safest available mechanisms.

When the connection returns:
perform resynchronization with the real exchange state.

The exchange state is authoritative for actual account and position state.

---

## 23. ORDER SAFETY

Never assume:
"order request sent = order filled"

Support order lifecycle states.

Important states may include:
- CREATED
- SUBMITTED
- ACCEPTED
- PARTIALLY_FILLED
- FILLED
- CANCEL_REQUESTED
- CANCELLED
- REJECTED
- EXPIRED
- UNKNOWN

Timeouts and unknown results must be reconciled with the exchange before retrying.

Prevent duplicate orders.

Use a unique LYRA client order identifier where supported.

---

## 24. PROTECTIVE STOP

For automated futures positions, protective stop logic is a critical safety requirement.

If a position is opened but protective risk protection cannot be established:
the system must not treat the position as safely managed.

Use the safest available reduce-only closure path when appropriate.

Do not silently continue as though protection exists.

---

## 25. AI

LYRA AI is an intelligence/orchestration layer.

AI may analyze:
- market data
- opportunities
- risk data
- positions
- portfolio data
- system state

AI does not override:
- system rules
- risk rules
- user permissions
- exchange limits
- kill switch
- active exchange rule

AI must not directly bypass the Execution Engine.

AI decisions should be structured and validated before execution.

AI provider implementation should remain replaceable.

---

## 26. DEMO VS REAL

Demo and real environments/data must be isolated.

Demo:
- demo positions
- demo orders
- demo performance
- demo history

Real:
- real positions
- real orders
- real performance
- real history

A demo trade must NEVER appear as a real performance result.

---

## 27. ENVIRONMENTS

Maintain distinct concepts for:

Development
Staging
Production

Production credentials must never be used for ordinary development.

Real trading must remain disabled until explicitly enabled.

Use a feature flag such as:

LIVE_TRADING_ENABLED=false

until all required safety checks have passed.

---

## 28. TESTNET / DEMO BEFORE REAL TRADING

Real trading must not be the first execution environment.

Use appropriate exchange test/demo environments where possible before production execution.

Do not store real exchange API credentials in development environments.

---

## 29. FINANCIAL NUMBERS

Do not use JavaScript floating-point arithmetic for money-sensitive logic when exact decimal arithmetic is required.

Use database numeric/decimal types and a suitable decimal arithmetic strategy.

Be careful with:
- prices
- quantities
- fees
- P/L
- stops
- targets
- leverage calculations

---

## 30. TIME

Store timestamps in UTC.

Convert to user timezone for display.

Do not mix local time storage with UTC storage.

---

## 31. UTF-8 / TURKISH

UTF-8 is mandatory across:
- source files
- JSON
- API
- database
- documentation
- UI

Never create mojibake such as:
- TÃ¼rkÃ§e
- Ã‡
- Ä±
- Ã¶
- ÅŸ

Turkish strings such as:
- Kontrol Paneli
- Fırsatlar
- Açık Pozisyonlar
- Borsa İstanbul
- Güvenlik
- İşlem
- Şifre
- Çıkış

must remain correctly encoded.

Add an encoding validation test/script.

---

## 32. INTERNATIONALIZATION

User-facing strings should be structured so the application can support centralized Turkish translations.

Do not scatter important user-facing text unnecessarily throughout business logic.

Code identifiers may remain in English.

User-visible language is Turkish.

---

## 33. SECURITY

Never expose:
- database passwords
- session secrets
- JWT secrets
- AI keys
- exchange secrets

to the browser.

Never commit .env files with secrets.

Use .env.example only for placeholders.

Sensitive secrets must not appear in logs.

Admin credentials and exchange secrets must not be visible in plaintext to other users.

---

## 34. ADMIN SECURITY

Admin authentication is separate from normal user authentication.

Admin must have stronger security controls.

Admin 2FA is required for the production admin environment.

Do not allow user-side privilege escalation.

Backend authorization is mandatory.

---

## 35. DATABASE

PostgreSQL is the source of persistent application data.

Use migration-based schema management.

Keep database migrations versioned.

Keep development and production databases separate.

Use UTC timestamps.

Use exact numeric types for financial values.

---

## 36. AUDIT

Important security and trading/system events should be auditable.

Examples:
- login
- authentication events
- admin actions
- user invitations
- exchange connection changes
- opportunity approval
- opportunity rejection
- order lifecycle
- position changes
- automation changes
- kill switch actions
- system failures

Audit records should not be casually deleted or rewritten from the normal UI.

---

## 37. CI QUALITY GATES

The project should use automated validation.

At minimum:
- lint
- typecheck
- unit tests
- integration tests where appropriate
- build

Encoding checks should also be included.

Do not ignore failing validation.

---

## 38. DEVELOPMENT RULE

Never make a broad repository change when the requested task is narrow.

Do not refactor unrelated areas without explicit approval.

Before changing architecture, explain why.

Before adding a dependency, verify whether the current stack already solves the requirement.

Prefer simple, maintainable solutions.

---

## 39. BYCHAT PREVIEW RULE

The Bychat preview is a VISUAL/UX reference.

Do not blindly copy its generated code into the production architecture.

The preview will be reviewed separately.

Before integrating any preview component:
- inspect it
- evaluate it
- adapt it to LYRA V2 architecture
- preserve the approved visual intent

Do not let the preview dictate the backend architecture.

---

## 40. NO UNSANCTIONED FEATURES

Do NOT add:
- social media
- community/chat
- NFT features
- metaverse features
- gambling
- unrelated financial products
- staking systems
- extra broker integrations
- BIST automated trading
- USA automated broker trading
- unexplained dashboards
- unrelated subscription features

Only implement the approved LYRA scope.

---

## 41. FOUNDATION-FIRST RULE

The current foundation phase must focus on:
- repository architecture
- configuration
- shared types
- validation
- authentication boundaries
- admin/user separation
- database foundation
- testing foundation
- CI
- UTF-8
- security baseline
- documentation

Do not implement the full product yet.

Do not connect real exchanges yet.

Do not execute trades yet.

---

## 42. CHANGE SAFETY

Before significant changes:
- inspect git status
- understand affected files
- keep changes scoped
- run validation afterward

Never:
- force push
- reset repository history
- delete unrelated files
- expose secrets

unless explicitly instructed.

---

## 43. REPORTING

After a task, report:
1. what changed
2. files created/modified
3. commands run
4. tests
5. typecheck
6. lint
7. build result
8. remaining issues

Do not claim success without verification.

---

## 44. CURRENT TASK BOUNDARY

The current task is FOUNDATION ONLY.

Do not start:
- full UI
- live market data
- exchange execution
- Opportunity Engine implementation
- real AI trading
- Serbest Kasa execution
- production deployment

until explicitly instructed.

END OF LYRA V2 ENGINEERING CONSTITUTION
