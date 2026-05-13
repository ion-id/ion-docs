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

The model is structured in **three distinct planes** that connect at two boundaries.

The **business and policy compliance plane** covers everything about who a participant is and what they are permitted to do. It places every organisation into a sector and category, establishes their role on the network, and ties their participation to Indonesian regulatory requirements. This plane is governed by the laws of Indonesia and ION governance policy. Compliance here is verified at onboarding and periodically re-verified.

The **catalogue plane** covers what a participant sells or offers on the network — the item classification layer. It bridges the business plane (what business are you) and the protocol plane (how do you transact). A seller's catalogue is structured around Resource Categories — ION-owned item classifications that enforce completeness and correctness of what is published, and signal to buyer apps how to present and discover items. This plane is governed by the ION Sector Working Group for each sector.

The **protocol and technical compliance plane** covers everything about how a participant must build. It provides sector-native transaction blueprints (patterns), named flow variations (variants), and a structured test suite that must be passed before going live. This plane is governed by Beckn Protocol v2.0, ION's extensions, and ION's conformance test suites. Compliance here is verified pre-go-live through test execution.

The three planes are deliberately separated. A change in business category does not change the protocol. A new CRC does not require a regulatory re-classification. Each plane has its own rules, its own governance, and its own verification process.

A shared path notation ties the whole model together. Any point in the hierarchy can be expressed as:

```
sector / category-code / resource-category-code / pattern / variant
```

For example: `trade / TRD-01 / TRC-food-bev / storefront / cash-on-delivery`

This means: within the Trade sector, under the Grocery & FMCG category (`TRD-01`), selling a Food & Beverage resource (`TRC-food-bev`), using the storefront transaction pattern, with the cash-on-delivery variant applied.

**A worked example — a pharmacy joining ION as a seller:**

1. The pharmacy asks "where do I fit?" — they sell medicines and health products, so they are in **Trade**, under category **`TRD-04` Pharmacy & Health Retail**
2. They are a merchant, not a platform — their role is **Seller**. Their profile is `seller / trade`
3. Their regulatory obligations are now clear — KBLI codes `47730`, `47740`, `47750` apply; they verify their NIB accordingly
4. They publish their catalogue — medicines are classified as **`TRC-health-beauty`** (Health & Beauty CRC). This triggers the pharmacy attribute schema — dosage form, strength, prescription flag, BPOM registration number — all become mandatory fields on the resource
5. They decide to implement the `storefront` pattern — the standard catalogue-browse-and-purchase flow
6. They add the `delivery-time-kyc` variant — medicines require identity verification at delivery
7. Their full reference path is `trade / TRD-04 / TRC-health-beauty / storefront / delivery-time-kyc`
8. The ION governance team's validation framework provides the Test Set for this exact path — the pharmacy runs it and passes before going live

From "I sell medicines" to a fully specified compliance and technical build path, in eight steps.

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
  CATALOGUE PLANE                    (what is being sold or offered)
  Governed by ION Sector Working Groups
─────────────────────────────────────────────────────────────────────

                      └── CRC   [Catalogue Resource Category — item classification; enforces catalogue completeness]
                            └── Resource / Item   [the specific item published in the catalogue]

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

| Sector | Core activity | Principle | `context.domain` |
|---|---|---|---|
| **Trade** | Buying & selling goods | Buyer ends up owning a thing | `ion:trade` |
| **Hospitality** | Experiences & lifestyle services | Time-bounded reservation of capacity | `ion:hospitality` |
| **Logistics** | Moving & storing goods | Movement and storage of goods | `ion:logistics` |
| **Mobility** | Moving people | Movement of people | `ion:mobility` |
| **Finance** | Financial products & services | Money and financial instruments | `ion:finance` |
| **Services** | Professional, tech & operational capability | Human labour and expertise | `ion:services` |

The sector principle is the classification test. When an item could belong to multiple sectors, ask: *what does the buyer end up with?* The answer determines the sector. A physical medicine → Trade (buyer owns it). A doctor consultation → Services (buyer receives expertise). A hotel room → Hospitality (buyer reserves capacity).

Sector identity travels on every Beckn transaction in `context.domain`, routing each transaction to the correct governance rules, policy attachments, and regulatory checks.

---

### 5. Category

Categories are the **segment-level groupings within a Sector**. They sit entirely within the business and policy compliance plane and define:

- What a business is **permitted to sell or offer** within a Sector
- Which **Indonesian regulations and licences** apply to that activity
- Which **KBLI codes** are associated with the activity

Categories are ION-owned and governed by the ION governance team, mapped to KBLI 2025 codes for regulatory routing.

Categories do **not** mandate which Patterns a participant must implement. A business in `trade / TRD-01` (Grocery & FMCG) may choose to build `storefront`, `live-commerce`, `subscription`, or any combination — Pattern selection is a technical build decision in the protocol compliance plane.

Each Category carries a short reference code: Sector prefix + sequential number (e.g., `TRD-01`, `HSP-03`, `MOB-02`). See Annexure B for the complete code reference.

---

## — First Compliance Boundary —

> Everything above — Organisation, Role, Profile, Sector, Category — is the **business and policy compliance plane**. Rules here are derived from Indonesian law, sectoral regulations (OJK, Kemenhub, Kemenkes/BPOM), and ION governance policy. Compliance is verified at onboarding and periodically re-verified.
>
> Everything below begins the **catalogue plane** — what is actually being sold or offered on the network.

---

### 6. Catalogue Resource Category (CRC)

A Catalogue Resource Category (CRC) is the **item-level classification** within a sector. It is the answer to the question every seller must answer when publishing their catalogue:

> **"What kind of thing are you selling?"**

CRCs sit in the catalogue plane — below the business classification (Sector / Category) and above the individual item (Resource). They serve two purposes and only two:

1. **Enforce catalogue completeness** — the CRC determines which attributes must be declared on a resource at `catalog/publish` time. A resource published without the required attributes for its category is rejected by the CDS.

2. **Signal to the buyer app how to present the item** — the CRC tells the BAP which attribute fields to render, which badges to display, which filters to surface, and how to structure the product page.

CRCs do **not** drive pattern selection, regulatory enforcement, or logistics routing directly. Those are driven by the attribute values that the Resource Category ensures are correctly declared.

**CRC codes** use a sector prefix + short name. For example: `TRC-fashion` (Trade Resource Category — Fashion), `HSC-accommodation` (Hospitality Service Category — Accommodation).

**GPC mapping (Trade only):** For the Trade sector, CRC names and vocabulary are aligned with Google Product Category (GPC) L0. This is an onboarding convenience — sellers with existing GPC codes map directly at onboarding without translation. The GPC mapping is a crosswalk reference only; it is never transmitted on the wire. Non-Trade sectors have no GPC equivalent and ION defines those categories independently.

**Status tags:**
- `ACTIVE` — attribute schema defined, validation enforced at `catalog/publish`
- `INACTIVE` — category recognised, no attribute schema enforcement yet; sellers may use but validation is not applied
- `GOVERNED` — blocked pending ION Council policy decision; resources cannot be published under this category

---

### 7. Resource / Item

A Resource is the **atomic unit of value** in the ION catalogue — a specific item, service slot, entitlement, or capacity unit that a seller publishes and a buyer can select and purchase.

In Beckn Protocol v2.0, the Resource maps directly to the `Resource` object in `Catalog.resources[]`. All ION-specific item attributes attach to `Resource.resourceAttributes` — the JSON-LD extension bag governed by the sector attribute pack.

Every Resource has:
- A **CRC** (Catalogue Resource Category) — which enforces the attribute schema
- A **Transaction Type** — the structural nature of what is being transacted (Physical Good, Prepared Good, Digital Entitlement, Slot, Capacity, Financial Instrument, Professional Service)
- **Attributes** — the specific fields declared for this item, driven by its CRC

The Transaction Type is not a separate declared field — it is derived from the resource's attributes (`resourceTangibility`, `resourceStructure`, sector membership) and determines which patterns are structurally eligible for this item.

---

### 7a. Transaction Types

Every Resource on ION has a **Transaction Type** — the structural DNA of what is being transacted. The Transaction Type is not a declared field; it is derived from the resource's attributes (`resourceTangibility`, `resourceStructure`, and sector membership). But it is the most fundamental classification in the system because it determines the shape of the entire transaction machinery.

**Why Transaction Types matter:**

The same sector can have resources of different transaction types. A grocery store (TRD-01) sells packaged goods (Physical Good) but also mobile top-up (Digital Entitlement). A hospitality venue (HSP-04) serves dine-in meals (Prepared Good) and takes table bookings (Slot). Without the transaction type, ION cannot determine which fulfilment model applies, which patterns are structurally eligible, or how post-order flows (returns, cancellations, delivery) should work.

Transaction Types are **universal across countries** — a Physical Good is a Physical Good in Indonesia, Malaysia, or India. The sector and CRC carry the jurisdiction-specific meaning. The transaction type carries the structural truth.

**The Seven Transaction Types:**

---

#### 1. PHYSICAL_GOOD

A tangible item that requires physical logistics to deliver.

```
Logistics required          →  yes — carrier must be engaged
Has inventory               →  yes — availability signal required
Can expire or be damaged    →  yes — perishability and condition matter
Return flow                 →  physical return with QC inspection
Cancellation window         →  closes at dispatch or per policy
COD eligible                →  yes (with restrictions per category)
Fulfilment                  →  delivery to address or self-pickup
Age verification            →  possible at delivery (restricted goods)
```

**Examples:** Packaged food, clothing, electronics, medicines, furniture, automotive parts, books.

**Derived from:** `resourceTangibility = PHYSICAL`

---

#### 2. PREPARED_GOOD

An item that does not exist until the buyer orders it — made or assembled after confirmation.

```
Logistics required          →  yes (delivery or self-pickup) or dine-in
Has inventory               →  no — made on demand
Preparation time            →  mandatory attribute; drives SLA
Return                      →  generally not possible once prepared
Cancellation window         →  closes when preparation begins
COD eligible                →  yes
Fulfilment                  →  delivery, self-pickup, or dine-in
```

**Examples:** Restaurant meals, custom cakes, bespoke tailoring, made-to-order furniture, custom-printed merchandise.

**Derived from:** `resourceTangibility = PHYSICAL` + `preparationTime` declared, or sector = Hospitality + dine-in / delivery pattern.

---

#### 3. DIGITAL_ENTITLEMENT

A value or access right delivered electronically — no physical logistics involved.

```
Logistics required          →  no
Has inventory               →  operator/issuer dependent — target validation required
Return                      →  generally impossible — value already consumed
Cancellation window         →  closes at operator processing start
COD                         →  not permitted
Fulfilment                  →  code push to target, account credit, or QR voucher
Operator validation         →  required before confirm (phone number, meter, game ID)
```

**Examples:** Mobile top-up (pulsa), PLN token, game credits, digital vouchers, gift cards, e-books, software licences.

**Derived from:** `resourceTangibility = DIGITAL_VOUCHER | DIGITAL_TOP_UP | DIGITAL_SUBSCRIPTION`

---

#### 4. SLOT

A time-bounded unit of capacity or access. Time is the product.

```
Logistics required          →  no
Has inventory               →  capacity-based — not stock-based
Datetime                    →  mandatory attribute; defines the slot
Cancellation                →  primary post-order flow; window is critical
Rescheduling                →  natural variant
Return                      →  not applicable — replaced by cancellation
COD                         →  generally not applicable
Fulfilment                  →  attendance, connection, or access grant
```

**Examples:** Hotel room booking, restaurant table reservation, doctor appointment, ride-hailing journey, concert ticket, gym class.

**Derived from:** Sector = Hospitality or Mobility, or `resourceType = slot` in Services.

---

#### 5. CAPACITY

Space or movement is the product. Logistics IS the fulfilment — not the delivery mechanism.

```
Logistics required          →  logistics is the product itself
Weight and volume           →  primary attributes
Route                       →  mandatory
Dynamic pricing             →  common — freight rate shopping
Damage and loss liability   →  primary post-order concern
Fulfilment                  →  transportation event with checkpoints
Documentation               →  waybill, customs declaration (cross-border)
```

**Examples:** Parcel delivery service, freight booking, warehousing capacity, sea freight slot, air cargo booking.

**Derived from:** Sector = Logistics.

---

#### 6. FINANCIAL_INSTRUMENT

Money or a financial commitment is the product.

```
Logistics required          →  no
KYC of buyer                →  mandatory before confirm
Rate and tenure             →  primary attributes
Regulatory gating           →  OJK / Bank Indonesia licence required
Post-order flow             →  repayment, servicing, claims, redemption
Return                      →  not applicable — replaced by early settlement or cancellation
COD                         →  not applicable
Fulfilment                  →  disbursement, policy activation, account opening
```

**Examples:** Personal loan, insurance policy, investment fund unit, e-wallet top-up, BNPL instalment.

**Derived from:** Sector = Finance.

---

#### 7. PROFESSIONAL_SERVICE

Human skill or expertise is the product. Time-bound or outcome-bound.

```
Logistics required          →  no (unless physical site visit)
Practitioner credentials    →  primary compliance check
Scope of work               →  defines the contract
Deliverable                 →  work product, advice, or treatment
Post-order flow             →  scope change, milestone completion, referral
Return                      →  not applicable — replaced by dispute or scope adjustment
Cancellation window         →  depends on work-in-progress state
IP ownership                →  may be relevant post-delivery
```

**Examples:** Legal consultation, medical appointment, IT project, physiotherapy session, tutoring, design brief.

**Derived from:** Sector = Services, or `resourceType = service` with practitioner credentials.

---

**Transaction Type → Pattern Eligibility:**

The transaction type determines which patterns are structurally possible. Sector and Category then narrow which patterns are relevant for a specific business. Resource attributes then determine if any variants are mandatory.

| Transaction Type | Structurally eligible patterns |
|---|---|
| PHYSICAL_GOOD | `storefront`, `marketplace-listed`, `marketplace-inhouse`, `live-commerce`, `made-to-order`, `subscription`, `business-procurement`, `forward-auction`, `reverse-auction`, `cross-border`, `government` |
| PREPARED_GOOD | `made-to-order`, `dine-in-order`, `delivery-order`, `storefront` (pre-packaged variant) |
| DIGITAL_ENTITLEMENT | `digital-goods`, `subscription` |
| SLOT | `reservation`, `experience-booking`, `dine-in-order`, `on-demand-ride`, `scheduled-journey`, `charter`, `pass`, `engagement-booking` |
| CAPACITY | `parcel`, `hyperlocal`, `freight`, `warehouse`, `roro`, `cross-border` |
| FINANCIAL_INSTRUMENT | `loan-application`, `policy-issuance`, `investment-order`, `payment`, `account-opening`, `service-activation` |
| PROFESSIONAL_SERVICE | `engagement-booking`, `retainer`, `project-order`, `subscription`, `marketplace-listed` |

---

## — Second Compliance Boundary —

> Everything above — including CRC and Resource — is the **catalogue plane**. Rules here are governed by the ION Sector Working Group. Compliance is verified at `catalog/publish` time by the CDS.
>
> Everything below is the **protocol and technical compliance plane**. Rules here are derived from Beckn v2.0, ION's extensions, and ION's conformance test suites. Compliance is verified **pre-go-live** through test execution.

---

### 8. Pattern

Patterns are **sector-native transaction blueprints** describing how a complete Beckn transaction flow (search → select → init → confirm → fulfil → settle) operates for a specific commerce scenario.

Patterns are **guidance-driven, not protocol-mandating**. They represent ION's recommended model implementations — the way ION explains to participants how to build correctly for a given scenario. They do not require changes to the underlying Beckn protocol. New Patterns can be added at any time as commerce scenarios evolve. The Pattern library is open for contribution and updates — participants, Network Participants, and the ION governance team can all propose new Patterns as real-world needs emerge.

Every Pattern is named in the language of its sector. A Mobility pattern talks about journeys. A Finance pattern talks about applications and disbursements. A Hospitality pattern talks about reservations and orders. This is intentional — the terminology a builder encounters in the spec should match the terminology of the domain they are building for.

**Mapping rules:**
- Every Pattern is **mapped to a Sector** — this is the firm constraint
- Patterns have **no mandatory direct link to a Category** — the same Pattern can serve multiple Categories within a Sector
- Patterns **may carry an advisory Category hint** where a strong natural association exists
- One Category may be served by multiple Patterns; one Pattern may serve multiple Categories

---

### 9. Variant

A Variant is a **named modification of a Pattern** that alters specific flows, fields, or rules without warranting a wholly new Pattern.

**Every Pattern has exactly one default Variant: the happy flow** — the baseline success path with no complications, edge cases, or alternative fulfilment scenarios. All other Variants are named departures from this baseline.

A transaction can invoke **multiple Variants simultaneously** on a single Pattern. They are additive. Mutual-exclusion rules, where two Variants cannot coexist on the same Pattern, are defined per Pattern as the library matures.

`mid-transaction-changes` is an umbrella Variant that covers buyer-initiated modifications after confirmation — item, quantity, address, or time. It is intentionally broad at this stage. Pattern-specific test cases define precisely what "mid-transaction" means in each context.

---

### 10. Test Set

A Test Set is a **named collection of Test Cases** scoped to a specific Pattern + Variant combination (or a Pattern at its default happy flow). Test Sets are built and maintained by the ION governance team under a dedicated validation framework and test runner.

Test Sets are the unit of conformance certification. A Seller App or Buyer App seeking ION go-live approval must pass the required Test Sets for every Pattern + Variant combination they declare support for.

---

### 11. Test Case

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
| Catalogue plane | Added as a third plane between the business/policy plane and the protocol plane. Contains Segment→CRC mapping. Governed by Sector Working Groups. Verified at catalog/publish time. |
| CRC governance | ION-owned codes. GPC L0 vocabulary used for Trade as onboarding crosswalk only — never on wire. Non-Trade sectors defined independently. |
| CRC → Pattern | CRC does not drive Pattern selection. Transaction Type (derived from resource attributes) drives Pattern eligibility. Resource Category enforces attribute completeness only. |
| TRD-06 rename | Renamed from "B2B Trade & Wholesale" to "Wholesale & Supply Trade" to avoid confusion with `business-procurement` and `business-credit` pattern names. |
| KBLI enforcement | ION maintains the reference mapping. Enforcement follows Indonesian regulation. ION does not enforce. |
| `advance-ride-booking` | Variant of `on-demand-ride`, not a standalone Pattern. |
| `business-credit` | Variant of `business-procurement`, not a standalone Pattern. |
| `self-pickup` vs `sender-drop-off` | Two distinct Variants. `self-pickup` = buyer/receiver collects (Trade, Hospitality). `sender-drop-off` = sender brings parcel to a hub (Logistics). |
| `mid-transaction-changes` | Kept as a single umbrella Variant for now. Pattern-specific test cases define what "mid-transaction" means in each context. |
| Segment → CRC mapping | No formal mapping exists. ION does not mandate which CRCs are valid under which Segments — that would be regulatory overreach. The Annexure C table is illustrative/guidance only. ION enforces attribute completeness per CRC, not CRC eligibility per Segment. |
| CRC naming convention | "Catalogue Resource Category" (CRC) is the correct term. "Resource Category" and "Item Category" are retired. Prefix codes: TRC (Trade), HSC (Hospitality), LGC (Logistics), MOC (Mobility), FNC (Finance), SVRC (Services). |
| CRC purposes | CRC serves exactly two purposes: (1) enforce catalogue completeness at `catalog/publish` time, (2) signal to BAP how to present and discover the item. Everything else (patterns, variants, logistics, regulations) flows from the attribute values the CRC enforces — not from the CRC code itself. |
| Seven Transaction Types | PHYSICAL_GOOD · PREPARED_GOOD · DIGITAL_ENTITLEMENT · SLOT · CAPACITY · FINANCIAL_INSTRUMENT · PROFESSIONAL_SERVICE. Not a declared field — derived from resource attributes and sector. Defined in Section 7a with structural constraints per type. |
| Accessibility / Purple Economy | Not a separate CRC. Handled via `resourceAttributes.accessibilityFeatures[]` flag on existing CRCs. Discovery layer at BAP/CDS level. Revisit as separate CRC only if regulatory treatment diverges at scale. |
| B2C vs B2B same item | Same CRC. Different Segment + Pattern + Offer. The item doesn't change — the transaction terms do. |
| CRC → GPC vocabulary (Trade) | Trade CRCs use GPC L0 vocabulary as the naming reference. ION governs the codes. GPC is an onboarding crosswalk convenience — never on the wire. Non-Trade sectors define their own CRC vocabulary independently. |
| Cross-country portability | ION category codes (Sector + Segment + CRC) are universal — they travel on the wire unchanged regardless of country. Jurisdiction-specific meaning (BPOM, KBLI, PPN) lives in a country overlay that never touches the wire. Indonesia = first deployment of a universal protocol. |
| Taxonomy decision | ION owns its category codes. No external taxonomy (GPC, KBLI, ONDC, Shopify) is adopted wholesale. External taxonomies serve as crosswalk inputs for seller onboarding only. The reason: no existing taxonomy satisfies all five CRC purposes (attribute validation, regulatory routing, pattern eligibility, logistics handling, discovery) across all six ION sectors. |
| CRC classification test | A proposed category is a distinct CRC if and only if it requires different mandatory attributes, different regulatory checks, or different transaction flows from every other existing CRC. Otherwise merge. |
| F&B sector placement | F&B (Food & Beverage) / restaurants are in Hospitality sector (HSP-04, HSP-05), not Trade. Packaged food sold in grocery/pharmacy is Trade (`TRC-food-bev`). The test: does the buyer end up owning a physical thing? If yes → Trade. If it's a time-bounded prepared meal → Hospitality. |
| Healthcare sector | No Healthcare sector on ION. Healthcare products (medicines, devices) → Trade. Healthcare services (consultations, diagnostics) → Services (SVC-05). Healthcare capacity (hospital beds) → Hospitality. |
| Protein powder CRC | `TRC-food-bev` not `TRC-health-beauty`. BPOM classifies protein powder as Pangan Olahan (processed food) with ML registration. Regulatory classification of the item — not where it is sold — determines the CRC. |
| Extension zones | Four zones inside any Attributes bag: (1) Beckn Defined — @context, @type, Beckn core fields; (2) ION Mandated — ION core + sector + conditional fields; (3) Participant Extensions — NP-declared under own IRI namespace, size-capped, never validated by ION Central; (4) Experimental — SWG pilot fields, auto-expire, x-ion-experimental: true tag. |

---

## Annexure A — Suggested Patterns & Variants by Sector / Category

> ION suggests the Patterns below as appropriate starting points for each Category. This mapping is advisory — businesses may implement differently. The default Variant for every Pattern is the **happy flow** and is not listed separately. Variants shown are named departures from that baseline. The Pattern and Variant libraries grow over time without requiring protocol changes.
>
> Reference path: `sector / category-code / pattern / variant`

---

## Annexure A — Suggested Patterns & Variants by Sector / Category

> ION suggests the Patterns below as appropriate starting points for each Category. This mapping is advisory — businesses may implement differently. The default Variant for every Pattern is the **happy flow** and is not listed separately. Variants shown are named departures from that baseline. The Pattern and Variant libraries grow over time without requiring protocol changes.
>
> Reference path: `sector / category-code / resource-category-code / pattern / variant`

---

### Segment × CRC × Pattern Mapping

This table shows which CRCs typically appear under each Segment, the Transaction Type each CRC carries, and which patterns are commonly applicable. Where a CRC appears under multiple Segments, it is listed in each.

> Advisory — not a hard constraint. A seller may publish any CRC that is appropriate for their business. The mapping below reflects common practice.

#### Trade

| Segment | CRC | Transaction Type | Typical Patterns |
|---|---|---|---|
| TRD-01 Grocery, FMCG & Quick Commerce | `TRC-food-bev` | PHYSICAL_GOOD | `storefront`, `subscription`, `live-commerce` |
| TRD-01 Grocery, FMCG & Quick Commerce | `TRC-health-beauty` | PHYSICAL_GOOD | `storefront`, `subscription` |
| TRD-01 Grocery, FMCG & Quick Commerce | `TRC-digital` | DIGITAL_ENTITLEMENT | `digital-goods` |
| TRD-02 E-commerce & Digital Marketplaces | `TRC-fashion` | PHYSICAL_GOOD | `storefront`, `marketplace-listed`, `marketplace-inhouse`, `live-commerce` |
| TRD-02 E-commerce & Digital Marketplaces | `TRC-electronics` | PHYSICAL_GOOD | `storefront`, `marketplace-listed`, `marketplace-inhouse` |
| TRD-02 E-commerce & Digital Marketplaces | `TRC-health-beauty` | PHYSICAL_GOOD | `storefront`, `marketplace-listed`, `live-commerce` |
| TRD-02 E-commerce & Digital Marketplaces | `TRC-home-garden` | PHYSICAL_GOOD | `storefront`, `marketplace-listed` |
| TRD-02 E-commerce & Digital Marketplaces | `TRC-furniture` | PHYSICAL_GOOD | `storefront`, `marketplace-listed` |
| TRD-02 E-commerce & Digital Marketplaces | `TRC-toys-baby` | PHYSICAL_GOOD | `storefront`, `marketplace-listed` |
| TRD-02 E-commerce & Digital Marketplaces | `TRC-sports` | PHYSICAL_GOOD | `storefront`, `marketplace-listed`, `live-commerce` |
| TRD-02 E-commerce & Digital Marketplaces | `TRC-digital` | DIGITAL_ENTITLEMENT | `digital-goods`, `subscription` |
| TRD-02 E-commerce & Digital Marketplaces | `TRC-arts` | PHYSICAL_GOOD | `storefront`, `live-commerce`, `forward-auction` |
| TRD-03 Specialty Retail | `TRC-fashion` | PHYSICAL_GOOD · PREPARED_GOOD | `storefront`, `made-to-order`, `live-commerce` |
| TRD-03 Specialty Retail | `TRC-arts` | PHYSICAL_GOOD · PREPARED_GOOD | `storefront`, `made-to-order`, `live-commerce` |
| TRD-03 Specialty Retail | `TRC-furniture` | PHYSICAL_GOOD · PREPARED_GOOD | `storefront`, `made-to-order` |
| TRD-03 Specialty Retail | `TRC-electronics` | PHYSICAL_GOOD | `storefront`, `made-to-order` |
| TRD-03 Specialty Retail | `TRC-religious` | PHYSICAL_GOOD | `storefront`, `live-commerce` |
| TRD-03 Specialty Retail | `TRC-luggage` | PHYSICAL_GOOD | `storefront`, `marketplace-listed` |
| TRD-04 Pharmacy & Health Retail | `TRC-health-beauty` | PHYSICAL_GOOD | `storefront`, `subscription` |
| TRD-04 Pharmacy & Health Retail | `TRC-food-bev` | PHYSICAL_GOOD | `storefront`, `subscription` |
| TRD-05 Automotive Sales & Parts | `TRC-automotive` | PHYSICAL_GOOD · PREPARED_GOOD | `storefront`, `made-to-order`, `marketplace-listed` |
| TRD-06 Wholesale & Supply Trade | `TRC-industrial` | PHYSICAL_GOOD | `business-procurement`, `reverse-auction`, `forward-auction`, `cross-border` |
| TRD-06 Wholesale & Supply Trade | `TRC-food-bev` | PHYSICAL_GOOD | `business-procurement`, `cross-border` |
| TRD-06 Wholesale & Supply Trade | `TRC-automotive` | PHYSICAL_GOOD | `business-procurement` |
| TRD-07 Energy & Commodity Trade | `TRC-industrial` | PHYSICAL_GOOD · CAPACITY | `business-procurement`, `forward-auction`, `reverse-auction` |

#### Hospitality

| Segment | CRC | Transaction Type | Typical Patterns |
|---|---|---|---|
| HSP-01 Hotels & Accommodation | `HSC-accommodation` | SLOT | `reservation` |
| HSP-02 Short-Stay & Alternative Accommodation | `HSC-accommodation` | SLOT | `reservation`, `marketplace-listed` |
| HSP-03 Travel Platforms & OTAs | `HSC-accommodation` | SLOT | `reservation`, `marketplace-listed` |
| HSP-03 Travel Platforms & OTAs | `HSC-events` | SLOT | `experience-booking`, `marketplace-listed` |
| HSP-04 F&B — Restaurants & Dining | `HSC-restaurant` | SLOT | `reservation`, `dine-in-order` |
| HSP-04 F&B — Restaurants & Dining | `HSC-fnb-delivery` | PREPARED_GOOD | `delivery-order` |
| HSP-05 Food Delivery & Online Ordering | `HSC-fnb-delivery` | PREPARED_GOOD | `delivery-order`, `marketplace-listed` |
| HSP-06 Events, Catering & MICE | `HSC-events` | SLOT · PREPARED_GOOD | `event-commission`, `experience-booking` |
| HSP-07 Recreation, Attractions & Wellness | `HSC-wellness` | SLOT | `experience-booking`, `pass` |

#### Logistics

| Segment | CRC | Transaction Type | Typical Patterns |
|---|---|---|---|
| LOG-01 Last-Mile & Courier Delivery | `LGC-lastmile` | CAPACITY | `parcel`, `hyperlocal` |
| LOG-02 Freight Transport | `LGC-freight` | CAPACITY | `freight` |
| LOG-03 Warehousing & Fulfilment | `LGC-warehouse` | CAPACITY | `warehouse` |
| LOG-04 Sea & Air Freight | `LGC-international` | CAPACITY | `freight`, `roro`, `cross-border` |
| LOG-05 Freight Support & Forwarding | `LGC-forwarding` | PROFESSIONAL_SERVICE | `freight`, `cross-border` |

#### Mobility

| Segment | CRC | Transaction Type | Typical Patterns |
|---|---|---|---|
| MOB-01 Ride-Hailing & On-Demand Transport | `MOC-ride` | SLOT | `on-demand-ride` |
| MOB-02 Mass Transit & Scheduled Transport | `MOC-scheduled` | SLOT | `scheduled-journey`, `pass` |
| MOB-03 Aviation — Passenger | `MOC-scheduled` | SLOT | `scheduled-journey`, `charter` |
| MOB-04 Future Mobility | `MOC-ride` · `MOC-scheduled` | SLOT | `on-demand-ride`, `scheduled-journey` |

#### Finance

| Segment | CRC | Transaction Type | Typical Patterns |
|---|---|---|---|
| FIN-01 Banking | `FNC-banking` · `FNC-payments` | FINANCIAL_INSTRUMENT | `account-opening`, `payment`, `service-activation` |
| FIN-02 Lending & Consumer Credit | `FNC-lending` | FINANCIAL_INSTRUMENT | `loan-application` |
| FIN-03 Payments & E-money | `FNC-payments` | FINANCIAL_INSTRUMENT | `payment` |
| FIN-04 Insurance & Risk | `FNC-insurance` | FINANCIAL_INSTRUMENT | `policy-issuance` |
| FIN-05 Capital Markets & Wealth | `FNC-investment` | FINANCIAL_INSTRUMENT | `investment-order`, `service-activation` |
| FIN-06 Digital Assets & Carbon Finance | `FNC-investment` | FINANCIAL_INSTRUMENT | `investment-order`, `forward-auction` |
| FIN-07 Financial Infrastructure & Support | `FNC-payments` | FINANCIAL_INSTRUMENT | `service-activation` |

#### Services

| Segment | CRC | Transaction Type | Typical Patterns |
|---|---|---|---|
| SVC-01 Technology & Digital Services | `SVRC-tech` | PROFESSIONAL_SERVICE | `subscription`, `project-order`, `engagement-booking` |
| SVC-02 Telecom & Connectivity | `SVRC-telecom` | PROFESSIONAL_SERVICE | `subscription`, `service-activation` |
| SVC-03 Professional & Advisory Services | `SVRC-professional` | PROFESSIONAL_SERVICE | `engagement-booking`, `retainer`, `project-order` |
| SVC-04 Marketing, Media & Creative | `SVRC-creative` | PROFESSIONAL_SERVICE | `project-order`, `retainer`, `subscription` |
| SVC-05 Healthcare & Wellness Services | `SVRC-healthcare` | PROFESSIONAL_SERVICE · SLOT | `engagement-booking`, `subscription` |
| SVC-06 Real Estate & Construction Services | `SVRC-professional` | PROFESSIONAL_SERVICE | `project-order`, `engagement-booking`, `marketplace-listed` |
| SVC-07 Automotive & Vehicle Services | `SVRC-auto` | PROFESSIONAL_SERVICE · SLOT | `engagement-booking`, `project-order` |
| SVC-08 Facilities, Support & Personal Services | `SVRC-home` | PROFESSIONAL_SERVICE · SLOT | `subscription`, `retainer`, `engagement-booking` |

---

### Pattern Glossary

#### Trade
| Pattern | What it means |
|---|---|
| `storefront` | A buyer browses a seller's catalogue, picks items, and completes a purchase. The standard retail flow. |
| `live-commerce` | A seller runs a live broadcast; buyers discover and purchase during the session. |
| `digital-goods` | The item delivered is purely digital — no physical fulfilment. |
| `made-to-order` | The item does not exist until the buyer commissions it. Confirmation triggers production before fulfilment. |
| `subscription` | Buyer commits to recurring delivery or access. Each cycle auto-initiates. |
| `marketplace-listed` | Seller lists on a third-party marketplace; marketplace intermediates discovery. Seller retains fulfilment. |
| `marketplace-inhouse` | Marketplace owns both listing and fulfilment. |
| `business-procurement` | A business buyer raises a structured purchase order against a supplier. |
| `forward-auction` | Seller lists an item; multiple buyers bid up. Highest bid at close wins. |
| `reverse-auction` | Buyer posts a requirement; multiple sellers bid down. Lowest qualifying bid wins. |
| `cross-border` | Transaction crosses an international border. Customs, duties, and cross-currency settlement apply. |
| `government` | Procurement under government or public sector rules. |

#### Hospitality
| Pattern | What it means |
|---|---|
| `reservation` | Buyer books a time-bound experience at a specific property or venue. |
| `experience-booking` | Buyer books a structured experience — tour, attraction, activity, event. |
| `dine-in-order` | Buyer orders food at a physical table. No prior reservation required. |
| `delivery-order` | Buyer orders food or beverage for delivery. A logistics leg is embedded. |
| `event-commission` | Buyer commissions a catering or event service. Scope negotiated before confirmation. |
| `pass` | Buyer purchases recurring or multi-use access — gym membership, museum pass. |

#### Logistics
| Pattern | What it means |
|---|---|
| `parcel` | A discrete package is picked up from a sender and delivered to a recipient. |
| `hyperlocal` | Same-day or on-demand delivery within a tight geographic radius. |
| `freight` | Large-volume or heavy cargo movement. |
| `warehouse` | A business places goods into managed storage. |
| `roro` | Roll-on/Roll-off vessel movement. |
| `cross-border` | Shipment crosses an international border. |

#### Mobility
| Pattern | What it means |
|---|---|
| `on-demand-ride` | A passenger requests a ride in real time. |
| `scheduled-journey` | A passenger books a seat on a fixed-route service. |
| `charter` | A passenger or group books exclusive use of a vehicle. |
| `pass` | A passenger purchases recurring or multi-use transit access. |

#### Finance
| Pattern | What it means |
|---|---|
| `account-opening` | A customer applies to open a financial account. |
| `loan-application` | A customer applies for credit. |
| `payment` | A payer initiates a transfer of funds to a payee. |
| `policy-issuance` | A customer applies for an insurance policy. |
| `investment-order` | A customer places an order to buy or sell a financial instrument. |
| `service-activation` | A customer activates an ongoing financial service. |

#### Services
| Pattern | What it means |
|---|---|
| `engagement-booking` | A client books a professional for a defined scope of work. |
| `retainer` | A client engages a professional on an ongoing basis. |
| `subscription` | Client subscribes to a recurring digital or operational service. |
| `project-order` | Client commissions a defined deliverable with milestones. |
| `marketplace-listed` | Service provider lists capacity on a platform. |

---

### Trade

| Category Code | Category Name | Suggested Patterns | Variants (beyond happy flow) |
|---|---|---|---|
| `TRD-01` | Grocery, FMCG & Quick Commerce | `storefront`, `subscription`, `live-commerce` | `cash-on-delivery`, `self-pickup`, `redelivery-attempts`, `mid-transaction-changes` |
| `TRD-02` | E-commerce & Digital Marketplaces | `storefront`, `marketplace-listed`, `marketplace-inhouse`, `live-commerce`, `digital-goods` | `cash-on-delivery`, `self-pickup`, `mid-transaction-changes`, `return-to-sender-handoff` |
| `TRD-03` | Specialty Retail | `storefront`, `made-to-order`, `live-commerce` | `cash-on-delivery`, `self-pickup`, `mid-transaction-changes`, `return-to-sender-handoff` |
| `TRD-04` | Pharmacy & Health Retail | `storefront`, `subscription` | `cash-on-delivery`, `self-pickup`, `delivery-time-kyc`, `redelivery-attempts` |
| `TRD-05` | Automotive Sales & Parts | `storefront`, `made-to-order`, `marketplace-listed` | `self-pickup`, `mid-transaction-changes` |
| `TRD-06` | Wholesale & Supply Trade | `business-procurement`, `made-to-order`, `reverse-auction`, `forward-auction`, `cross-border` | `business-credit`, `mid-transaction-changes` |
| `TRD-07` | Energy & Commodity Trade | `business-procurement`, `forward-auction`, `reverse-auction` | `mid-transaction-changes` |

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

### Logistics

| Category Code | Category Name | Suggested Patterns | Variants (beyond happy flow) |
|---|---|---|---|
| `LOG-01` | Last-Mile & Courier Delivery | `parcel`, `hyperlocal` | `cash-on-delivery`, `sender-drop-off`, `redelivery-attempts`, `return-to-sender-handoff`, `delivery-time-kyc` |
| `LOG-02` | Freight Transport | `freight` | `mid-transaction-changes` |
| `LOG-03` | Warehousing & Fulfilment | `warehouse` | `mid-transaction-changes` |
| `LOG-04` | Sea & Air Freight | `freight`, `roro`, `cross-border` | `mid-transaction-changes` |
| `LOG-05` | Freight Support & Forwarding | `freight`, `cross-border` | `mid-transaction-changes` |

### Mobility

| Category Code | Category Name | Suggested Patterns | Variants (beyond happy flow) |
|---|---|---|---|
| `MOB-01` | Ride-Hailing & On-Demand Transport | `on-demand-ride` | `advance-ride-booking`, `mid-journey-stop-change` |
| `MOB-02` | Mass Transit & Scheduled Transport | `scheduled-journey`, `pass` | `seat-upgrade`, `mid-transaction-changes` |
| `MOB-03` | Aviation — Passenger | `scheduled-journey`, `charter` | `seat-upgrade`, `mid-transaction-changes` |
| `MOB-04` | Future Mobility | `on-demand-ride`, `scheduled-journey` | `mid-transaction-changes` |

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
> Full reference path format: `sector / category-code / resource-category-code / pattern / variant`

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
| `TRD-06` | Wholesale & Supply Trade |
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

### Catalogue Resource Category (CRC) Codes

CRCs are ION-owned item classifications. GPC L0 mapping is a crosswalk for seller onboarding — never transmitted on wire. Status: ACTIVE = schema enforced · INACTIVE = recognised, no enforcement · GOVERNED = blocked pending policy decision.

#### Trade CRCs (`TRC`)

| Code | ION Name | GPC L0 Mapping | Status |
|---|---|---|---|
| `TRC-fashion` | Fashion & Accessories | Apparel & Accessories | ACTIVE |
| `TRC-electronics` | Electronics & Gadgets | Electronics · Cameras & Optics | ACTIVE |
| `TRC-food-bev` | Food, Beverages & Tobacco | Food, Beverages & Tobacco | ACTIVE |
| `TRC-health-beauty` | Health & Beauty | Health & Beauty | ACTIVE |
| `TRC-home-garden` | Home & Garden | Home & Garden | ACTIVE |
| `TRC-furniture` | Furniture | Furniture | ACTIVE |
| `TRC-automotive` | Vehicles & Parts | Vehicles & Parts | ACTIVE |
| `TRC-sports` | Sporting Goods | Sporting Goods | ACTIVE |
| `TRC-toys-baby` | Toys, Games & Baby | Toys & Games · Baby & Toddler | ACTIVE |
| `TRC-media` | Books & Media | Media | ACTIVE |
| `TRC-digital` | Digital Goods | Software | ACTIVE |
| `TRC-industrial` | Industrial & Wholesale | Business & Industrial | ACTIVE |
| `TRC-arts` | Arts, Crafts & Collectibles | Arts & Entertainment | ACTIVE |
| `TRC-religious` | Religious & Ceremonial | Religious & Ceremonial | ACTIVE |
| `TRC-luggage` | Luggage & Bags | Luggage & Bags | ACTIVE |
| `TRC-pets` | Animals & Pet Supplies | Animals & Pet Supplies | INACTIVE |
| `TRC-hardware` | Hardware & Tools | Hardware | INACTIVE |
| `TRC-office` | Office Supplies | Office Supplies | INACTIVE |
| `TRC-mature` | Mature | Mature | GOVERNED |

#### Hospitality CRCs (`HSC`)

| Code | ION Name | GPC L0 Mapping | Status |
|---|---|---|---|
| `HSC-accommodation` | Accommodation | — | ACTIVE |
| `HSC-restaurant` | Restaurant & Dining | — | ACTIVE |
| `HSC-fnb-delivery` | Food & Beverage Delivery | — | ACTIVE |
| `HSC-events` | Events & Experiences | — | ACTIVE |
| `HSC-wellness` | Recreation & Wellness | — | ACTIVE |

#### Logistics CRCs (`LGC`)

| Code | ION Name | GPC L0 Mapping | Status |
|---|---|---|---|
| `LGC-lastmile` | Last-Mile Delivery | — | ACTIVE |
| `LGC-freight` | Freight Transport | — | ACTIVE |
| `LGC-international` | Sea & Air Freight | — | ACTIVE |
| `LGC-warehouse` | Warehousing | — | ACTIVE |
| `LGC-forwarding` | Freight Forwarding | — | ACTIVE |

#### Mobility CRCs (`MOC`)

| Code | ION Name | GPC L0 Mapping | Status |
|---|---|---|---|
| `MOC-ride` | Ride-Hailing | — | ACTIVE |
| `MOC-scheduled` | Scheduled Transport | — | ACTIVE |
| `MOC-rental` | Vehicle Rental | — | ACTIVE |
| `MOC-charter` | Charter | — | INACTIVE |

#### Finance CRCs (`FNC`)

| Code | ION Name | GPC L0 Mapping | Status |
|---|---|---|---|
| `FNC-lending` | Lending & Credit | — | ACTIVE |
| `FNC-insurance` | Insurance | — | ACTIVE |
| `FNC-payments` | Payments & E-money | — | ACTIVE |
| `FNC-investment` | Investment | — | ACTIVE |
| `FNC-banking` | Banking Services | — | INACTIVE |

#### Services CRCs (`SVRC`)

| Code | ION Name | GPC L0 Mapping | Status |
|---|---|---|---|
| `SVRC-tech` | Technology & Digital Services | — | ACTIVE |
| `SVRC-professional` | Professional & Advisory | — | ACTIVE |
| `SVRC-healthcare` | Healthcare & Wellness Services | — | ACTIVE |
| `SVRC-telecom` | Telecom & Connectivity | — | ACTIVE |
| `SVRC-creative` | Creative & Marketing | — | ACTIVE |
| `SVRC-home` | Home & Facility Services | — | ACTIVE |
| `SVRC-education` | Education & Training | — | ACTIVE |
| `SVRC-auto` | Automotive Services | — | INACTIVE |

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

---


---

## Annexure C — Segment → CRC Relationship

> **Design decision: No formal Segment → CRC mapping exists.**
>
> ION does not mandate which CRCs are valid under which Segments. A seller in TRD-04 (Pharmacy) is not restricted to `TRC-health-beauty` — if their business legitimately stocks electronics or food products, they may publish those CRCs. What a seller sells within their licensed Segment is the seller's decision, governed by their operating licences and business registration, not by ION.
>
> ION's role is to enforce **attribute completeness per CRC** at `catalog/publish` time — not to police which CRCs a seller may use.
>
> The illustrative associations below are for **onboarding guidance only** — they show what sellers in each Segment commonly publish. They are not constraints and are not enforced at any layer.

---

### Illustrative associations (guidance only, not enforced)

#### Trade

| Segment | Segment Name | Commonly published CRCs |
|---|---|---|
| TRD-01 | Grocery, FMCG & Quick Commerce | `TRC-food-bev` · `TRC-health-beauty` · `TRC-digital` |
| TRD-02 | E-commerce & Digital Marketplaces | `TRC-fashion` · `TRC-electronics` · `TRC-health-beauty` · `TRC-home-garden` · `TRC-furniture` · `TRC-toys-baby` · `TRC-sports` · `TRC-digital` · `TRC-arts` · `TRC-luggage` · `TRC-media` |
| TRD-03 | Specialty Retail | `TRC-fashion` · `TRC-arts` · `TRC-furniture` · `TRC-electronics` · `TRC-religious` · `TRC-luggage` · `TRC-sports` · `TRC-hardware` |
| TRD-04 | Pharmacy & Health Retail | `TRC-health-beauty` · `TRC-food-bev` |
| TRD-05 | Automotive Sales & Parts | `TRC-automotive` |
| TRD-06 | Wholesale & Supply Trade | `TRC-industrial` · `TRC-food-bev` · `TRC-automotive` · `TRC-hardware` · `TRC-office` |
| TRD-07 | Energy & Commodity Trade | `TRC-industrial` |

#### Hospitality

| Segment | Segment Name | Commonly published CRCs |
|---|---|---|
| HSP-01 | Hotels & Accommodation | `HSC-accommodation` |
| HSP-02 | Short-Stay & Alternative Accommodation | `HSC-accommodation` |
| HSP-03 | Travel Platforms & OTAs | `HSC-accommodation` · `HSC-events` |
| HSP-04 | F&B — Restaurants & Dining | `HSC-restaurant` · `HSC-fnb-delivery` |
| HSP-05 | Food Delivery & Online Ordering | `HSC-fnb-delivery` |
| HSP-06 | Events, Catering & MICE | `HSC-events` |
| HSP-07 | Recreation, Attractions & Wellness | `HSC-wellness` · `HSC-events` |

#### Logistics

| Segment | Segment Name | Commonly published CRCs |
|---|---|---|
| LOG-01 | Last-Mile & Courier Delivery | `LGC-lastmile` |
| LOG-02 | Freight Transport | `LGC-freight` |
| LOG-03 | Warehousing & Fulfilment | `LGC-warehouse` |
| LOG-04 | Sea & Air Freight | `LGC-international` |
| LOG-05 | Freight Support & Forwarding | `LGC-forwarding` |

#### Mobility

| Segment | Segment Name | Commonly published CRCs |
|---|---|---|
| MOB-01 | Ride-Hailing & On-Demand Transport | `MOC-ride` |
| MOB-02 | Mass Transit & Scheduled Transport | `MOC-scheduled` |
| MOB-03 | Aviation — Passenger | `MOC-scheduled` |
| MOB-04 | Future Mobility | `MOC-ride` · `MOC-scheduled` · `MOC-rental` |

#### Finance

| Segment | Segment Name | Commonly published CRCs |
|---|---|---|
| FIN-01 | Banking | `FNC-banking` · `FNC-payments` |
| FIN-02 | Lending & Consumer Credit | `FNC-lending` |
| FIN-03 | Payments & E-money | `FNC-payments` |
| FIN-04 | Insurance & Risk | `FNC-insurance` |
| FIN-05 | Capital Markets & Wealth | `FNC-investment` |
| FIN-06 | Digital Assets & Carbon Finance | `FNC-investment` |
| FIN-07 | Financial Infrastructure & Support | `FNC-payments` |

#### Services

| Segment | Segment Name | Commonly published CRCs |
|---|---|---|
| SVC-01 | Technology & Digital Services | `SVRC-tech` |
| SVC-02 | Telecom & Connectivity | `SVRC-telecom` |
| SVC-03 | Professional & Advisory Services | `SVRC-professional` |
| SVC-04 | Marketing, Media & Creative | `SVRC-creative` |
| SVC-05 | Healthcare & Wellness Services | `SVRC-healthcare` |
| SVC-06 | Real Estate & Construction Services | `SVRC-professional` |
| SVC-07 | Automotive & Vehicle Services | `SVRC-auto` |
| SVC-08 | Facilities, Support & Personal Services | `SVRC-home` · `SVRC-professional` |

---

### Summary Count

| Sector | Segments | CRCs |
|---|---|---|
| Trade | 7 | 19 |
| Hospitality | 7 | 5 |
| Logistics | 5 | 5 |
| Mobility | 4 | 4 |
| Finance | 7 | 5 |
| Services | 8 | 8 |
| **Total** | **38** | **46** |


*Version 0.4-draft. Changes from v0.3: Annexure C revised — Segment→CRC mapping confirmed as advisory/guidance only, not a formal constraint (ION does not mandate CRC eligibility per Segment — regulatory overreach). Decisions Log expanded with 18 additional entries covering: taxonomy design rationale, cross-country portability model, CRC purposes (two only), seven transaction types, four extension zones, F&B sector placement, healthcare classification, protein powder CRC ruling, accessibility/purple economy resolution, GPC vocabulary adoption for Trade CRCs, and B2C vs B2B same-item handling.*
