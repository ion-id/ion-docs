# ION Entity Hierarchy Model

---

## What This Document Is

ION is Indonesia's open digital commerce network. Its founding promise is simple: any seller app and any buyer app on the network can transact with each other without bilateral integration. A merchant joins once and becomes reachable by every buyer app. A buyer app connects once and gets access to every merchant. No exclusivity, no platform lock-in.

For that promise to hold, every participant needs to be able to answer a deceptively simple question the moment they consider joining:

**"I sell X — or I do Y — where do I fit on ION, and what does that mean for me?"**

A pharmacy chain, a freight forwarder, a fintech lending startup, a warung owner — none of them think in terms of protocols or network sectors when they first encounter ION. They think in terms of what they sell, what they do, and what they need to do to operate. The ION Entity Hierarchy Model is the answer to that question. It is, first and foremost, the **onboarding map**: start with what your business does, find your place in the hierarchy, and everything that follows — your regulatory obligations, your participation profile, your technical build path, your conformance requirements before go-live — is derived from that single classification decision.

This document defines that model.

---

## What Problems This Hierarchy Solves

The hierarchy exists because an open network without shared structure compounds problems rapidly. Four problems in particular motivated this model.

**The "where do I fit" problem.** When a business decides to join ION, they need to locate themselves — what they can do on the network, what role they play, which sector and category they belong to. Without a structured map, this becomes a guessing exercise. Businesses self-classify inconsistently, end up in the wrong sector, and discover the mismatch only when compliance or technical issues surface downstream. The hierarchy gives every business a clear, deterministic path from "what I do" to "where I am on ION."

**The classification problem.** Indonesia has six distinct commerce sectors, 38 business categories, and hundreds of KBLI codes. Without a clear mapping between these layers, regulatory routing breaks down. ION cannot verify that a participant is authorised to operate in the sector they claim, and participants cannot easily understand which regulations apply to their specific activity. The hierarchy maps sectors to categories to KBLI codes in a single reference model.

**The implementation problem.** Beckn Protocol v2.0 is powerful but general. It does not tell a ride-hailing platform how a journey booking should flow, or tell a bank how a loan application should be structured on the wire. Without sector-native implementation guidance, every builder interprets the protocol differently, interoperability breaks down, and conformance cannot be tested consistently. The hierarchy provides that guidance through patterns and variants — sector-specific transaction blueprints that sit above the protocol without changing it.

**The language problem.** A governance team member talking about "the grocery category," a developer talking about "the storefront pattern," and a regulator asking about "KBLI 47110" may all be referring to aspects of the same transaction — but without a shared reference model, they cannot verify that. Errors accumulate silently. Misalignments between business intent, regulatory classification, and technical implementation only surface at the worst possible moments. The hierarchy gives everyone — merchants, developers, regulators, governance team — a single shared language and reference notation.

---

## How the Hierarchy Works

The model is structured in two distinct planes that meet at the boundary between Category and Pattern.

The **business and policy compliance plane** covers everything about who a participant is and what they are permitted to do. It places every organisation into a sector and category, establishes their role on the network, and ties their participation to Indonesian regulatory requirements. This plane is governed by the laws of Indonesia and ION governance policy. Compliance here is verified at onboarding and periodically re-verified.

The **protocol and technical compliance plane** covers everything about how a participant must build. It provides sector-native transaction blueprints (patterns), named flow variations (variants), and a structured test suite that must be passed before going live. This plane is governed by Beckn Protocol v2.0, ION's extensions, and ION's conformance test suites. Compliance here is verified pre-go-live through test execution.

The two planes are deliberately separated. A change in business category does not change the protocol. A new transaction pattern does not require a regulatory re-classification. Each plane has its own rules, its own governance, and its own verification process.

A shared path notation ties the whole model together. Any point in the hierarchy can be expressed as:

```
sector / category-code / pattern / variant
```

For example: `trade / TRD-01 / storefront / cash-on-delivery`

This means: within the Trade sector, under the Grocery & FMCG category (`TRD-01`), using the storefront transaction pattern, with the cash-on-delivery variant applied. A regulator, a developer, and a governance team member reading that reference are all pointing at exactly the same thing.

**A worked example — a pharmacy joining ION as a seller:**

1. The pharmacy asks "where do I fit?" — they sell medicines and health products, so they are in **Trade**, under category **`TRD-04` Pharmacy & Health Retail**
2. They are a merchant, not a platform — their role is **Seller**. Their profile is `seller / trade`
3. Their regulatory obligations are now clear — KBLI codes `47730`, `47740`, `47750` apply; they verify their NIB accordingly
4. They decide to implement the `storefront` pattern — the standard catalogue-browse-and-purchase flow
5. They add the `delivery-time-kyc` variant — medicines require identity verification at delivery
6. Their full reference path is `trade / TRD-04 / storefront / delivery-time-kyc`
7. The ION governance team's validation framework provides the Test Set for this exact path — the pharmacy runs it and passes before going live

From "I sell medicines" to a fully specified compliance and technical build path, in seven steps.

---

## The Hierarchy

```
─────────────────────────────────────────────────────────────────────
  BUSINESS / POLICY COMPLIANCE PLANE            (laws of the land)
  Governed by Indonesian regulation and ION governance policy
─────────────────────────────────────────────────────────────────────

  Organisation
    └── Role(s)            [Seller | Seller App | Buyer App | TSP]
          └── Profile      [one Role + one Sector = one Profile]
                └── Sector [Trade | Hospitality | Logistics | Mobility | Finance | Services]
                      └── Category   [segment grouping; drives business/policy rules]

─────────────────────────────────────────────────────────────────────
  PROTOCOL / TECHNICAL COMPLIANCE PLANE         (pre-go-live checks)
  Governed by Beckn v2.0 spec, ION extensions, and conformance test suites
─────────────────────────────────────────────────────────────────────

                      └── Pattern    [sector-native transaction blueprint]
                            └── Variant         [named flow modification; default = happy flow]
                                  └── Test Set  [collection of Test Cases for a Pattern + Variant]
                                        └── Test Case   [stateful or stateless; atomic executable scenario]
```

---

## Layer-by-Layer Breakdown

### 1. Organisation

The top-level legal or operational entity registered on ION — a company, cooperative, or sole proprietorship with a valid NIB (Business Identification Number).

An organisation **can hold multiple Roles**. The same legal entity may participate in ION in more than one capacity. A technology company might simultaneously operate as a Seller App (running a merchant-facing platform) and as a TSP (providing technical infrastructure to other apps). A large conglomerate might operate as a Buyer App in one sector and a Seller App in another.

**KBLI is mapped at Organisation level, scoped to each Sector.** ION maintains the reference mapping of which KBLI codes apply to each Sector and Category. Organisations are expected to hold the relevant KBLI codes for the Sectors they operate in, as required by Indonesian regulation. Compliance with KBLI obligations is governed and enforced by Indonesian regulatory authorities. ION maintains the mapping as a reference — it does not enforce.

---

### 2. Role

The four recognised participation roles on ION:

| Role | What they do |
|---|---|
| **Seller** | The merchant — owns the catalogue, inventory, and fulfilment obligation |
| **Seller App** | The platform connecting Sellers to the ION network; hosts the Provider Platform (PP) |
| **Buyer App** | The consumer-facing application; hosts the Consumer Platform (CP) |
| **TSP** | Technology Service Provider — provides technical infrastructure *to* a Seller App or Buyer App; does not transact directly on the network |

A TSP is always attached to a Seller App, a Buyer App, or both. It does not hold a standalone transacting role. An organisation can hold more than one Role simultaneously.

---

### 3. Profile

A Profile is the **unit of authorisation and governance** on ION:

> **one Role + one Sector = one Profile**

A single organisation can hold multiple Profiles. Each Profile is independently authorised, independently governed, and independently mapped to KBLI codes for its Sector. Activating one Profile does not activate another — each is a separate authorisation lane.

A Profile is expressed in code as `role / sector`. For example:

| Profile Code | Meaning |
|---|---|
| `SA / trade` | Seller App operating in the Trade sector |
| `BA / mobility` | Buyer App operating in the Mobility sector |
| `TSP / logistics` | TSP providing infrastructure in the Logistics sector |
| `seller / hospitality` | Seller (merchant) operating in the Hospitality sector |

**Example — Gojek:**
- `BA / mobility` (GoRide, GoCar) — Kemenhub permit + KBLI `49230`
- `SA / hospitality` (GoFood platform) — KBLI `56400`
- `SA / finance` (GoPay) — OJK licence + KBLI `64950`

A TSP can hold a profile in multiple sectors — one `TSP / sector` profile per sector it serves, following the same model as all other roles.

---

### 4. Sector

Six sectors, each governed by the ION governance team:

| Sector | Core activity | `context.domain` |
|---|---|---|
| **Trade** | Buying & selling goods | `ion:trade` |
| **Hospitality** | Experiences & lifestyle services | `ion:hospitality` |
| **Logistics** | Moving & storing goods | `ion:logistics` |
| **Mobility** | Moving people | `ion:mobility` |
| **Finance** | Financial products & services | `ion:finance` |
| **Services** | Professional, tech & operational capability | `ion:services` |

Sector identity travels on every Beckn transaction in `context.domain`, routing each transaction to the correct governance rules, policy attachments, and regulatory checks.

---

### 5. Category

Categories are the **segment-level groupings within a Sector**. They sit entirely within the business and policy compliance plane and define:

- What a business is **permitted to sell or offer** within a Sector
- Which **Indonesian regulations and licences** apply to that activity
- The basis for **catalogue structure** — addressed separately, out of scope for this document

Categories are ION-owned and governed by the ION governance team, mapped to KBLI 2025 codes for regulatory routing.

Categories do **not** mandate which Patterns a participant must implement. A business in `trade / TRD-01` (Grocery & FMCG) may choose to build `storefront`, `live-commerce`, `subscription`, or any combination — Pattern selection is a technical build decision in the protocol compliance plane.

Each Category carries a short reference code: Sector prefix + sequential number (e.g., `TRD-01`, `HSP-03`, `MOB-02`). See Annexure B for the complete code reference.

---

## — The Compliance Boundary —

> Everything above — Organisation, Role, Profile, Sector, Category — is the **business and policy compliance plane**. Rules here are derived from Indonesian law, sectoral regulations (OJK, Kemenhub, Kemenkes/BPOM), and ION governance policy. Compliance is verified at onboarding and periodically re-verified.
>
> Everything below — Pattern, Variant, Test Set, Test Case — is the **protocol and technical compliance plane**. Rules here are derived from Beckn v2.0, ION's extensions, and ION's conformance test suites. Compliance is verified **pre-go-live** through test execution.

---

### 6. Pattern

Patterns are **sector-native transaction blueprints** describing how a complete Beckn transaction flow (search → select → init → confirm → fulfil → settle) operates for a specific commerce scenario.

Patterns are **guidance-driven, not protocol-mandating**. They represent ION's recommended model implementations — the way ION explains to participants how to build correctly for a given scenario. They do not require changes to the underlying Beckn protocol. New Patterns can be added at any time as commerce scenarios evolve. The Pattern library is open for contribution and updates — participants, Network Participants, and the ION governance team can all propose new Patterns as real-world needs emerge.

Every Pattern is named in the language of its sector. A Mobility pattern talks about journeys. A Finance pattern talks about applications and disbursements. A Hospitality pattern talks about reservations and orders. This is intentional — the terminology a builder encounters in the spec should match the terminology of the domain they are building for.

**Mapping rules:**
- Every Pattern is **mapped to a Sector** — this is the firm constraint
- Patterns have **no mandatory direct link to a Category** — the same Pattern can serve multiple Categories within a Sector
- Patterns **may carry an advisory Category hint** where a strong natural association exists
- One Category may be served by multiple Patterns; one Pattern may serve multiple Categories

---

### 7. Variant

A Variant is a **named modification of a Pattern** that alters specific flows, fields, or rules without warranting a wholly new Pattern.

**Every Pattern has exactly one default Variant: the happy flow** — the baseline success path with no complications, edge cases, or alternative fulfilment scenarios. All other Variants are named departures from this baseline.

A transaction can invoke **multiple Variants simultaneously** on a single Pattern. They are additive. Mutual-exclusion rules, where two Variants cannot coexist on the same Pattern, are defined per Pattern as the library matures.

`mid-transaction-changes` is an umbrella Variant that covers buyer-initiated modifications after confirmation — item, quantity, address, or time. It is intentionally broad at this stage. Pattern-specific test cases define precisely what "mid-transaction" means in each context.

---

### 8. Test Set

A Test Set is a **named collection of Test Cases** scoped to a specific Pattern + Variant combination (or a Pattern at its default happy flow). Test Sets are built and maintained by the ION governance team under a dedicated validation framework and test runner.

Test Sets are the unit of conformance certification. A Seller App or Buyer App seeking ION go-live approval must pass the required Test Sets for every Pattern + Variant combination they declare support for.

---

### 9. Test Case

A Test Case is an **atomic executable scenario** within a Test Set. Test Cases are of two types:

| Type | Description |
|---|---|
| **Stateless** | Self-contained; no dependency on prior API calls. Tests a single request-response pair or schema validation in isolation. |
| **Stateful** | Depends on prior state; tests a multi-step sequence where the output of one API call is the input to the next (e.g., search → select → init → confirm). |

Each Test Case specifies pre-conditions, the sequence of Beckn API calls with request payloads, expected responses, and pass/fail criteria. Every Test Case is traceable to a specific schema field or ION policy rule — the lowest-level link between the written specification and verifiable technical compliance.

---

## The Regulatory Layer — KBLI in Practice

KBLI (Klasifikasi Baku Lapangan Usaha Indonesia) is Indonesia's standard business classification system. ION maintains the mapping between its Sectors and Categories and the relevant KBLI 2025 codes (BPS Reg. 7/2025). This mapping is a reference artefact — enforcement of KBLI compliance follows Indonesian regulation and the relevant authorities. ION does not enforce.

Key points for participants:

- The KBLI 2020 to 2025 migration deadline is **18 June 2026**, as set by Indonesian regulation
- Finance profiles require an **OJK licence** in addition to NIB/KBLI
- Mobility profiles require a **Kemenhub permit** in addition to NIB/KBLI
- Health-related Services profiles require **Kemenkes/BPOM** authorisation where applicable
- Organisations holding multiple KBLI codes must identify the specific code that applies to each ION Profile's activity

New KBLI 2025 codes most relevant to ION's digital-native profiles: `47915` (social/live-stream commerce), `49230` (ride-hailing), `53300` (digital platform last-mile), `55400` (OTA platforms), `56400` (food delivery platforms), `63130` (digital marketplace intermediation), `64130` (digital banking), `64950` (payment system operators), `66140 / 66150 / 66180` (P2P lending, crowdfunding, BNPL).

---

## Decisions Log

| Topic | Decision |
|---|---|
| TSP profile scope | A TSP can hold one profile per Sector it serves — same model as all other roles |
| Pattern→Category mapping | Advisory and suggested. ION suggests Patterns per Category as guidance. Businesses may implement differently. Mandate may follow in a subsequent phase. |
| Multiple Variants per transaction | Yes — a transaction can invoke multiple Variants on a single Pattern simultaneously |
| Test Set ownership | ION governance team, under a dedicated validation framework and test runner |
| Catalogue structure | Out of scope for this document. Addressed separately. |
| KBLI enforcement | ION maintains the reference mapping. Enforcement follows Indonesian regulation. ION does not enforce. |
| `advance-ride-booking` | Variant of `on-demand-ride`, not a standalone Pattern. The core journey flow is the same — only the timing differs. |
| `business-credit` | Variant of `business-procurement`, not a standalone Pattern. It modifies how an existing procurement settles, not the procurement flow itself. |
| `self-pickup` vs `sender-drop-off` | Two distinct Variants, not one. `self-pickup` = buyer/receiver collects (Trade, Hospitality). `sender-drop-off` = sender brings parcel to a hub (Logistics). Same actor name, different actors doing different things. |
| `mid-transaction-changes` | Kept as a single umbrella Variant for now. Pattern-specific test cases define what "mid-transaction" means in each context. |

---

## Annexure A — Suggested Patterns & Variants by Sector / Category

> ION suggests the Patterns below as appropriate starting points for each Category. This mapping is advisory — businesses may implement differently. The default Variant for every Pattern is the **happy flow** and is not listed separately. Variants shown are named departures from that baseline. The Pattern and Variant libraries grow over time without requiring protocol changes.
>
> Reference path: `sector / category-code / pattern / variant`

---

### Pattern Glossary

#### Trade
| Pattern | What it means |
|---|---|
| `storefront` | A buyer browses a seller's catalogue, picks items, and completes a purchase. The standard retail flow — online or physical equivalent. |
| `live-commerce` | A seller runs a live broadcast; buyers discover and purchase during the session. Order is tied to a live event context. |
| `digital-goods` | The item delivered is purely digital — no physical fulfilment. A download, licence key, or access token is the deliverable. |
| `made-to-order` | The item does not exist until the buyer commissions it. Confirmation triggers production or customisation before fulfilment. |
| `subscription` | Buyer commits to recurring delivery or access. Each cycle auto-initiates without a new search-select flow. |
| `marketplace-listed` | Seller lists on a third-party marketplace; the marketplace intermediates discovery and may handle payment. Seller retains fulfilment. |
| `marketplace-inhouse` | Marketplace owns both listing and fulfilment — seller ships to marketplace, marketplace ships to buyer. |
| `business-procurement` | A business buyer raises a structured purchase order against a supplier. Approval workflows and bulk pricing may apply. |
| `forward-auction` | Seller lists an item; multiple buyers bid up. Highest bid at close wins. |
| `reverse-auction` | Buyer posts a requirement; multiple sellers bid down. Lowest qualifying bid wins. |
| `cross-border` | Transaction crosses an international border. Customs, duties, and cross-currency settlement apply. |
| `government` | Procurement under government or public sector rules — may involve tender processes, compliance declarations, and special payment rails. |

#### Hospitality
| Pattern | What it means |
|---|---|
| `reservation` | Buyer books a time-bound experience at a specific property or venue — hotel room, restaurant table, spa slot. Inventory is held against the booking. |
| `experience-booking` | Buyer books a structured experience — tour, attraction, activity, event. Has a defined start time and participant count. |
| `dine-in-order` | Buyer orders food at a physical table. Flow begins at seating and ends at settlement; no prior reservation required. |
| `delivery-order` | Buyer orders food or beverage for delivery to a location. A logistics leg is embedded within the hospitality transaction. |
| `event-commission` | Buyer commissions a catering or event service. Scope, menu, and logistics are negotiated before confirmation. Not a catalogue pick. |
| `pass` | Buyer purchases recurring or multi-use access — gym membership, museum annual pass. Defined by a validity period rather than a single event. |

#### Logistics
| Pattern | What it means |
|---|---|
| `parcel` | A discrete package is picked up from a sender and delivered to a recipient. Standard courier flow. |
| `hyperlocal` | Same-day or on-demand delivery within a tight geographic radius. Typically under two hours; rider dispatched in real time. |
| `freight` | Large-volume or heavy cargo movement — trucking, rail, sea, or air freight. Involves booking, documentation, and handoff checkpoints. |
| `warehouse` | A business places goods into managed storage. Flow covers inbound, storage period, and outbound. May include fulfilment operations. |
| `roro` | Roll-on/Roll-off vessel movement — vehicles or wheeled cargo loaded directly onto a ship. Specific to sea freight of wheeled cargo. |
| `cross-border` | Shipment crosses an international border. Customs declarations, duties, and cross-jurisdiction handoffs are part of the flow. |

#### Mobility
| Pattern | What it means |
|---|---|
| `on-demand-ride` | A passenger requests a ride in real time. Driver is matched and dispatched immediately. No pre-booking; journey starts near-instantly. |
| `scheduled-journey` | A passenger books a seat on a fixed-route service departing at a defined time — train, bus, ferry, or flight. Seat allocation and ticketing are core. |
| `charter` | A passenger or group books exclusive use of a vehicle for a defined route or duration. Full capacity reserved; not a shared service. |
| `pass` | A passenger purchases recurring or multi-use transit access — monthly rail pass, prepaid ride credits, multi-trip bus card. |

#### Finance
| Pattern | What it means |
|---|---|
| `account-opening` | A customer applies to open a financial account — bank account, e-wallet, investment account. Involves KYC, eligibility checks, and activation. |
| `loan-application` | A customer applies for credit — personal loan, business loan, BNPL, P2P lending. Flow covers application, scoring, offer, acceptance, and disbursement. |
| `payment` | A payer initiates a transfer of funds to a payee. Covers wallet top-up, bill payment, transfer, and payment gateway flows. |
| `policy-issuance` | A customer applies for an insurance policy. Flow covers product selection, risk declaration, premium calculation, and policy activation. |
| `investment-order` | A customer places an order to buy or sell a financial instrument — mutual fund, stock, bond, or digital asset. |
| `service-activation` | A customer activates an ongoing financial service — standing order, auto-debit, credit facility. Not a one-time transaction. |

#### Services
| Pattern | What it means |
|---|---|
| `engagement-booking` | A client books a professional for a defined scope of work — legal consultation, medical appointment, IT project, design brief. Scope agreed before confirmation. |
| `retainer` | A client engages a professional on an ongoing basis — monthly advisory, managed service contract. Recurring commitment with periodic billing. |
| `subscription` | Client subscribes to a recurring digital or operational service — SaaS, connectivity plan, maintenance contract. Auto-renews on a defined cycle. |
| `project-order` | Client commissions a defined deliverable — construction work, software build, content production. Milestones, payments, and sign-off points are structured into the flow. |
| `marketplace-listed` | Service provider lists capacity on a platform — freelance marketplace, contractor directory, staffing platform. Client discovers and books through the intermediary. |

---

### Trade

| Category Code | Category Name | Suggested Patterns | Variants (beyond happy flow) |
|---|---|---|---|
| `TRD-01` | Grocery, FMCG & Quick Commerce | `storefront`, `subscription`, `live-commerce` | `cash-on-delivery`, `self-pickup`, `redelivery-attempts`, `mid-transaction-changes` |
| `TRD-02` | E-commerce & Digital Marketplaces | `storefront`, `marketplace-listed`, `marketplace-inhouse`, `live-commerce`, `digital-goods` | `cash-on-delivery`, `self-pickup`, `mid-transaction-changes`, `return-to-sender-handoff` |
| `TRD-03` | Specialty Retail | `storefront`, `made-to-order`, `live-commerce` | `cash-on-delivery`, `self-pickup`, `mid-transaction-changes`, `return-to-sender-handoff` |
| `TRD-04` | Pharmacy & Health Retail | `storefront`, `subscription` | `cash-on-delivery`, `self-pickup`, `delivery-time-kyc`, `redelivery-attempts` |
| `TRD-05` | Automotive Sales & Parts | `storefront`, `made-to-order`, `marketplace-listed` | `self-pickup`, `mid-transaction-changes` |
| `TRD-06` | B2B Trade & Wholesale | `business-procurement`, `made-to-order`, `reverse-auction`, `forward-auction`, `cross-border` | `business-credit`, `mid-transaction-changes` |
| `TRD-07` | Energy & Commodity Trade | `business-procurement`, `forward-auction`, `reverse-auction` | `mid-transaction-changes` |

---

### Hospitality

| Category Code | Category Name | Suggested Patterns | Variants (beyond happy flow) |
|---|---|---|---|
| `HSP-01` | Hotels & Accommodation | `reservation` | `early-checkin`, `late-checkout`, `mid-transaction-changes` |
| `HSP-02` | Short-Stay & Alternative Accommodation | `reservation`, `marketplace-listed` | `early-checkin`, `late-checkout`, `mid-transaction-changes` |
| `HSP-03` | Travel Platforms & OTAs | `reservation`, `experience-booking`, `marketplace-listed` | `mid-transaction-changes` |
| `HSP-04` | F&B — Restaurants & Dining | `reservation`, `dine-in-order` | `mid-transaction-changes` |
| `HSP-05` | Food Delivery & Online Ordering | `delivery-order`, `marketplace-listed` | `cash-on-delivery`, `self-pickup`, `mid-transaction-changes` |
| `HSP-06` | Events, Catering & MICE | `event-commission`, `experience-booking` | `scope-change`, `mid-transaction-changes` |
| `HSP-07` | Recreation, Attractions & Wellness | `experience-booking`, `pass` | `mid-transaction-changes` |

---

### Logistics

| Category Code | Category Name | Suggested Patterns | Variants (beyond happy flow) |
|---|---|---|---|
| `LOG-01` | Last-Mile & Courier Delivery | `parcel`, `hyperlocal` | `cash-on-delivery`, `sender-drop-off`, `redelivery-attempts`, `return-to-sender-handoff`, `delivery-time-kyc` |
| `LOG-02` | Freight Transport | `freight` | `mid-transaction-changes` |
| `LOG-03` | Warehousing & Fulfilment | `warehouse` | `mid-transaction-changes` |
| `LOG-04` | Sea & Air Freight | `freight`, `roro`, `cross-border` | `mid-transaction-changes` |
| `LOG-05` | Freight Support & Forwarding | `freight`, `cross-border` | `mid-transaction-changes` |

---

### Mobility

| Category Code | Category Name | Suggested Patterns | Variants (beyond happy flow) |
|---|---|---|---|
| `MOB-01` | Ride-Hailing & On-Demand Transport | `on-demand-ride` | `advance-ride-booking`, `mid-journey-stop-change` |
| `MOB-02` | Mass Transit & Scheduled Transport | `scheduled-journey`, `pass` | `seat-upgrade`, `mid-transaction-changes` |
| `MOB-03` | Aviation — Passenger | `scheduled-journey`, `charter` | `seat-upgrade`, `mid-transaction-changes` |
| `MOB-04` | Future Mobility | `on-demand-ride`, `scheduled-journey` | `mid-transaction-changes` |

---

### Finance

| Category Code | Category Name | Suggested Patterns | Variants (beyond happy flow) |
|---|---|---|---|
| `FIN-01` | Banking | `account-opening`, `payment`, `service-activation` | `joint-account`, `mid-transaction-changes` |
| `FIN-02` | Lending & Consumer Credit | `loan-application` | `partial-disbursement`, `top-up-loan`, `early-settlement`, `mid-transaction-changes` |
| `FIN-03` | Payments & E-money | `payment` | `split-payment`, `mid-transaction-changes` |
| `FIN-04` | Insurance & Risk | `policy-issuance` | `mid-term-endorsement`, `mid-transaction-changes` |
| `FIN-05` | Capital Markets & Wealth | `investment-order`, `service-activation` | `mid-transaction-changes` |
| `FIN-06` | Digital Assets & Carbon Finance | `investment-order`, `forward-auction` | `mid-transaction-changes` |
| `FIN-07` | Financial Infrastructure & Support | `service-activation` | `mid-transaction-changes` |

---

### Services

| Category Code | Category Name | Suggested Patterns | Variants (beyond happy flow) |
|---|---|---|---|
| `SVC-01` | Technology & Digital Services | `subscription`, `project-order`, `engagement-booking` | `scope-change`, `mid-transaction-changes` |
| `SVC-02` | Telecom & Connectivity | `subscription`, `service-activation` | `plan-change`, `mid-transaction-changes` |
| `SVC-03` | Professional & Advisory Services | `engagement-booking`, `retainer`, `project-order` | `scope-change`, `mid-transaction-changes` |
| `SVC-04` | Marketing, Media & Creative | `project-order`, `retainer`, `subscription` | `scope-change`, `mid-transaction-changes` |
| `SVC-05` | Healthcare & Wellness Services | `engagement-booking`, `subscription` | `delivery-time-kyc`, `referral-handoff`, `mid-transaction-changes` |
| `SVC-06` | Real Estate & Construction Services | `project-order`, `engagement-booking`, `marketplace-listed` | `scope-change`, `mid-transaction-changes` |
| `SVC-07` | Automotive & Vehicle Services | `engagement-booking`, `project-order` | `mid-transaction-changes` |
| `SVC-08` | Facilities, Support & Personal Services | `subscription`, `retainer`, `engagement-booking` | `mid-transaction-changes` |

---

## Annexure B — Reference Codes

> Quick-reference index of all codes in the ION hierarchy.
> Full reference path format: `sector / category-code / pattern / variant`

---

### Profile Code Format

`role-shortcode / sector`

| Role | Shortcode |
|---|---|
| Seller | `seller` |
| Seller App | `SA` |
| Buyer App | `BA` |
| Technology Service Provider | `TSP` |

Examples: `SA / trade` · `BA / mobility` · `TSP / logistics` · `seller / hospitality`

---

### Category Codes

#### Trade (`TRD`)
| Code | Category |
|---|---|
| `TRD-01` | Grocery, FMCG & Quick Commerce |
| `TRD-02` | E-commerce & Digital Marketplaces |
| `TRD-03` | Specialty Retail |
| `TRD-04` | Pharmacy & Health Retail |
| `TRD-05` | Automotive Sales & Parts |
| `TRD-06` | B2B Trade & Wholesale |
| `TRD-07` | Energy & Commodity Trade |

#### Hospitality (`HSP`)
| Code | Category |
|---|---|
| `HSP-01` | Hotels & Accommodation |
| `HSP-02` | Short-Stay & Alternative Accommodation |
| `HSP-03` | Travel Platforms & OTAs |
| `HSP-04` | F&B — Restaurants & Dining |
| `HSP-05` | Food Delivery & Online Ordering |
| `HSP-06` | Events, Catering & MICE |
| `HSP-07` | Recreation, Attractions & Wellness |

#### Logistics (`LOG`)
| Code | Category |
|---|---|
| `LOG-01` | Last-Mile & Courier Delivery |
| `LOG-02` | Freight Transport |
| `LOG-03` | Warehousing & Fulfilment |
| `LOG-04` | Sea & Air Freight |
| `LOG-05` | Freight Support & Forwarding |

#### Mobility (`MOB`)
| Code | Category |
|---|---|
| `MOB-01` | Ride-Hailing & On-Demand Transport |
| `MOB-02` | Mass Transit & Scheduled Transport |
| `MOB-03` | Aviation — Passenger |
| `MOB-04` | Future Mobility |

#### Finance (`FIN`)
| Code | Category |
|---|---|
| `FIN-01` | Banking |
| `FIN-02` | Lending & Consumer Credit |
| `FIN-03` | Payments & E-money |
| `FIN-04` | Insurance & Risk |
| `FIN-05` | Capital Markets & Wealth |
| `FIN-06` | Digital Assets & Carbon Finance |
| `FIN-07` | Financial Infrastructure & Support |

#### Services (`SVC`)
| Code | Category |
|---|---|
| `SVC-01` | Technology & Digital Services |
| `SVC-02` | Telecom & Connectivity |
| `SVC-03` | Professional & Advisory Services |
| `SVC-04` | Marketing, Media & Creative |
| `SVC-05` | Healthcare & Wellness Services |
| `SVC-06` | Real Estate & Construction Services |
| `SVC-07` | Automotive & Vehicle Services |
| `SVC-08` | Facilities, Support & Personal Services |

---

### Pattern Index by Sector

| Sector | Patterns |
|---|---|
| `trade` | `storefront` · `live-commerce` · `digital-goods` · `made-to-order` · `subscription` · `marketplace-listed` · `marketplace-inhouse` · `business-procurement` · `forward-auction` · `reverse-auction` · `cross-border` · `government` |
| `hospitality` | `reservation` · `experience-booking` · `dine-in-order` · `delivery-order` · `event-commission` · `pass` |
| `logistics` | `parcel` · `hyperlocal` · `freight` · `warehouse` · `roro` · `cross-border` |
| `mobility` | `on-demand-ride` · `scheduled-journey` · `charter` · `pass` |
| `finance` | `account-opening` · `loan-application` · `payment` · `policy-issuance` · `investment-order` · `service-activation` |
| `services` | `engagement-booking` · `retainer` · `subscription` · `project-order` · `marketplace-listed` |

---

### Variant Applicability Matrix

> ✓ = variant applies to one or more patterns in this sector. Applicable pattern(s) shown in each cell. Blank = not applicable.

| Variant | trade | hospitality | logistics | mobility | finance | services |
|---|---|---|---|---|---|---|
| `cash-on-delivery` | `storefront` | `delivery-order` | `parcel` · `hyperlocal` | | | |
| `self-pickup` | `storefront` · `made-to-order` | `delivery-order` | | | | |
| `sender-drop-off` | | | `parcel` | | | |
| `redelivery-attempts` | `storefront` | | `parcel` · `hyperlocal` | | | |
| `return-to-sender-handoff` | `storefront` · `marketplace-listed` | | `parcel` | | | |
| `delivery-time-kyc` | `storefront` | | `parcel` · `hyperlocal` | | | `engagement-booking` |
| `advance-ride-booking` | | | | `on-demand-ride` | | |
| `mid-journey-stop-change` | | | | `on-demand-ride` | | |
| `seat-upgrade` | | | | `scheduled-journey` | | |
| `early-checkin` | | `reservation` | | | | |
| `late-checkout` | | `reservation` | | | | |
| `scope-change` | | `event-commission` | | | | `project-order` · `retainer` · `engagement-booking` |
| `referral-handoff` | | | | | | `engagement-booking` |
| `plan-change` | | | | | `service-activation` | `subscription` · `service-activation` |
| `joint-account` | | | | | `account-opening` | |
| `partial-disbursement` | | | | | `loan-application` | |
| `top-up-loan` | | | | | `loan-application` | |
| `early-settlement` | | | | | `loan-application` | |
| `split-payment` | | | | | `payment` | |
| `mid-term-endorsement` | | | | | `policy-issuance` | |
| `business-credit` | `business-procurement` | | | | | |
| `mid-transaction-changes` | `storefront` · `made-to-order` · `marketplace-listed` · `business-procurement` | `reservation` · `experience-booking` · `delivery-order` | `freight` · `warehouse` | `scheduled-journey` · `charter` | `payment` · `service-activation` · `investment-order` | `engagement-booking` · `retainer` · `project-order` · `subscription` |
