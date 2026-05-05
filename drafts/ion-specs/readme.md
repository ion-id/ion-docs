# ION — Proposed Specifications

**Indonesia Open Network**
*Open digital commerce infrastructure for Indonesia, built on Beckn Protocol v2.0.*

---

## About this repository

This repository contains the **proposed** specifications, schemas,
policies, and governance artifacts for ION — Indonesia's open digital
commerce network.

The word "proposed" matters. Documents here are working drafts. They
capture ION's current thinking, the reasoning behind it, and the
structure ION intends to operate under. Drafts evolve as ION learns
from real participants, real transactions, and real regulator
engagement.

You should read this repository if you are:

- A regulator or government counterpart trying to understand what ION is and how it is governed
- An engineer at a Buyer App, Seller App, or Technology Service Provider preparing to implement against ION
- A member of the Beckn community evaluating how ION composes with the upstream protocol
- A contributor proposing a change to ION's specifications

Each audience has a starting path further down. If you only have ten
minutes, start with [`EXECUTIVE_SUMMARY.md`](EXECUTIVE_SUMMARY.md).

---

## What ION is

ION is Indonesia's open digital commerce network. Any buyer app and any
seller app on ION can transact with each other without bilateral
integration — the way any phone can call any other phone without the
carriers needing custom deals. A merchant who joins ION once becomes
reachable by every buyer app on the network. A buyer app gets access to
every merchant. No exclusivity. No platform lock-in.

ION is operated as public infrastructure under Indonesian governance. It
is not a marketplace, not a platform, not a payment app. It is the
rules, registry, and shared infrastructure that let many independent
apps interoperate.

ION is built on **Beckn Protocol v2.0**, an open international standard
maintained at
[github.com/beckn/protocol-specifications-v2](https://github.com/beckn/protocol-specifications-v2).
ION uses Beckn unchanged at the wire level and adds Indonesian-specific
extensions through Beckn's own published extension mechanism. ION stays
interoperable with other Beckn-based networks while keeping full
Indonesian sovereignty over the registry, the rules, and the data.

---

## Foundational design commitments

These commitments shape every artifact in this repository. They were
arrived at deliberately and they are not negotiable on a per-pack basis.
Reasoning is given for each so that future contributors understand
*why* ION is the way it is, not just *what* it is.

### 1. Beckn-compliant, never forked

ION uses Beckn Protocol v2.0 unchanged at the wire level. ION extends
only through Beckn-native extension points — the `*Attributes` slots
(which we call *bags*) that Beckn itself designates for network-specific
data.

Why: forking Beckn would isolate ION from the international Beckn
ecosystem and force ION to maintain its own protocol indefinitely.
Extending only through bags lets ION carry Indonesian-specific data on
the wire while staying compliant. A Beckn implementation from another
country can process an ION payload — it will simply ignore fields it
does not understand, exactly as Beckn's design intends.

### 2. One network, six sectors

ION is one network, registered under the namespace `ion.id`. It is
segmented internally into six sectors. Sector identity travels in
Beckn's own `context.domain` field on every transaction.

Why not separate networks per sector: that approach fragments liquidity
(a logistics provider would have to register separately on each
sector's network), multiplies governance overhead, and complicates
cross-sector journeys. One network with sectors as governance segments
is structurally simpler. Sectors split *only* if a regulator
specifically mandates it — which has not happened.

### 3. Sectors cut by transactional shape, not industry

The six sectors are cut by **what is being transacted**:

| Sector | Principle |
|---|---|
| **Trade & Commerce** | Buyer ends up owning a thing |
| **Logistics** | Movement of goods |
| **Mobility** | Movement of people |
| **Hospitality** | Time-bounded reservation of capacity |
| **Finance** | Exchange of money or financial instruments |
| **Services** | Exchange of human labor or expertise |

Why this cut and not industry verticals: industry cuts (healthcare,
education, agriculture, retail-vs-services) turn out to be incoherent
because each industry crosses many transactional shapes. Healthcare
alone contains products (medicines), services (consultations),
hospitality (hospital stays), finance (insurance), and logistics
(ambulances). Cutting by transactional shape sidesteps this entirely:
each piece is governed by the rules appropriate to *what it is*, and
multi-sector journeys are modeled as linked transactions, one per
sector.

The disambiguation rule, when a transaction's sector is not obvious:

- Buyer ends up owning a thing → **Trade**
- Buyer consumes someone's time or expertise → **Services**
- Buyer gets time-bounded access to capacity → **Hospitality**
- Goods are being moved → **Logistics**
- People are being moved → **Mobility**
- Money or financial instruments are being exchanged → **Finance**

### 4. Complexity lives inside ION's central infrastructure, not on the wire

Where a design choice creates work, it should create work for ION's
central team once, not for every participant repeatedly. Participants
deal with simple, single-scheme classifications and clear field shapes.
Crosswalks to international schemes (KBLI, HS, Google, Meta) are
maintained internally by ION, not exposed as participant burden.

Why: ION has a small expert team that can absorb complexity. Thousands
of participants cannot. Pushing complexity inward makes the network
easier to join and more uniform in behavior.

### 5. Three zones inside every Beckn extension bag

Beckn provides twelve extension bags across its object model (places
where network-specific data can be attached: `Offer.offerAttributes`,
`Contract.contractAttributes`, `Provider.providerAttributes`, and so
on — the full list is in [`docs/BECKN_BAGS.md`](docs/BECKN_BAGS.md)).
Inside each bag, ION carves out three zones:

1. **Governed zone** — top-level fields published by ION as
   authoritative. Strictly validated. Whole-network consistent. ION is
   responsible for these.
2. **Participant extensions zone** — a sub-object called
   `participantExtensions`, where any registered participant may add
   their own fields. Loosely validated, size-capped, declared at
   onboarding. The participant is responsible for these.
3. **ION experimental zone** — a sub-object called `ionExperimental`,
   where Sector Working Groups pilot fields before they become part of
   the governed zone. Time-boxed; auto-expires. Either promoted to
   governed or retired.

Why three zones: a single zone (governed only) is too rigid — it kills
participant innovation. Two zones (governed + participant) leave
SWG-pilot fields homeless, polluting the governed zone every time
something is tried. Three zones give every kind of field a clear home
with a clear governance regime.

A worked example, on `Offer.offerAttributes`:

```json
"offerAttributes": {
  "@context": "https://schema.ion.id/sectors/trade/v1/context.jsonld",
  "@type": "ion:TradeOffer",

  "category": "ion:trade.fashion-apparel.womens.tops",
  "cancellationPolicy": "ion://policy/cancel/standard/free",
  "returnPolicy":       "ion://policy/return/standard/7d-sellerpays",

  "participantExtensions": {
    "@context": "https://schema.tokopedia.com/ion/v1/context.jsonld",
    "@type":    "tokopedia:OfferExtensions",
    "loyaltyTier":     "GOLD",
    "internalOfferId": "tk-99821"
  },

  "ionExperimental": {
    "@context": "https://schema.ion.id/experimental/halal/v0/context.jsonld",
    "@type":    "ion-exp:HalalAttributes",
    "halalCertNumber": "MUI-LP-POM-12345",
    "_experiment":     { "id": "halal-trial-2026", "expiresAt": "2026-12-31" }
  }
}
```

Three zones, one bag. ION enforces each zone differently. Participants
can innovate in their zone without coordinating with ION; ION can
experiment safely without contaminating the governed surface; the
governed surface stays clean and stable.

### 6. One classification scheme on the wire

Each resource carries one ION-owned category code. Internal mappings to
KBLI, HS, Google's taxonomy, Meta's taxonomy, and others are maintained
by ION's central infrastructure for regulatory routing, statistics, and
participant onboarding convenience — never exposed as participant
burden on the wire.

Why: requiring participants to declare three or more classifications
per resource is the kind of complexity that pushes onboarding cost
outward. ION owns one taxonomy (`ion:{sector}`) designed for Indonesian
commerce, and absorbs the work of crosswalking to external schemes
internally.

### 7. Governance simplicity

ION is governed by a Council and Sector Working Groups. There is no
separate technical staff layer; operational work is the responsibility
of these bodies, with whatever delegation they choose internally.

Why: layered governance creates accountability gaps and slows
decisions. A two-tier model — Council for cross-sector decisions, SWG
for sector-specific decisions — keeps responsibility clear.

### 8. Patterns are documentation, not protocol

ION publishes transaction patterns and variations to help everyone
reason about flows consistently. They are not a layer of the protocol.
A participant who implements the underlying protocol correctly is
conformant whether or not they organize their code along the pattern
library.

Why: protocol additions are governance-heavy and have wide blast
radius; documentation refinements are not. Keeping patterns labeled as
documentation lets ION refine the library as it learns more about real
transactions, without forcing protocol changes.

---

## The three approaches to extending ION

When something new needs to be carried on the wire, there are exactly
three legitimate approaches. Each has a different governance regime,
a different audience, and a different lifecycle. Anyone proposing a
change should know which approach applies.

### Approach 1 — Add a field to the governed zone

**Use this when:** the field should apply to every participant in the
sector (or across sectors) and warrants formal governance — for
example, a regulator-required disclosure, a network-wide tax field,
or a structural policy reference.

**How it works:** the relevant Sector Working Group (or the Council
for cross-sector fields) drafts the field, the schema pack is updated,
and the new field becomes part of the governed zone of the relevant
bag. Every participant in the sector then carries the field on
matching transactions.

**Example:** ION decides that every Trade resource must carry a
classification code drawn from the ION category list. This is a
governed-zone field — it applies to every Trade transaction, it is
schema-validated, and the field is part of the published Trade pack.

**Trade-off:** governed-zone fields are stable and uniform across the
network, but adding them is the heaviest-weight option. Used too
freely, the governed zone becomes bloated; used too cautiously, the
network has to coordinate fields out-of-band.

### Approach 2 — Use the participant zone

**Use this when:** a single participant needs to carry a field for
their own use and does not need ION-wide governance. This is the
right approach for proprietary product attributes, internal IDs,
loyalty tiers, app-specific metadata, and integrations with
participant-internal systems.

**How it works:** the participant declares their JSON-LD context at
onboarding (a one-time disclosure to ION). On the wire, they place
their fields inside the `participantExtensions` sub-object on the
relevant bag, with their own `@context` and `@type`. No working group
review, no ION ratification.

**Example:** Tokopedia carries `loyaltyTier`, `internalOfferId`, and
`promoCode` inside `Offer.offerAttributes.participantExtensions` with
their own context. Other participants can ignore these fields without
breaking; Tokopedia's own systems read them.

**Trade-off:** participants get freedom to innovate without ION's
involvement, but their fields are not interoperable with other
participants. Counterparty apps may simply ignore them. The size of
the participant zone is capped at the gateway to prevent abuse.

### Approach 3 — Pilot through the experimental zone

**Use this when:** a Sector Working Group is testing whether a field
should eventually be promoted to the governed zone. Pilots run for a
fixed window during which the field travels alongside production
transactions, with telemetry and evaluation.

**How it works:** the SWG publishes an experimental context
(`https://schema.ion.id/experimental/{name}/v0/...`) and a time-boxed
expiry. Participants opting into the pilot carry the field inside
`ionExperimental` on the relevant bag. After the pilot window, the
field either gets promoted to the governed zone (formal ratification)
or auto-expires.

**Example:** a halal-certification field is piloted by the Trade SWG
for six months. Participants test the data shape and integration
patterns. If the pilot succeeds, the field moves to the Trade
governed zone with formal ratification. If it does not, the field
auto-expires and disappears.

**Trade-off:** experimental fields let ION test ideas safely without
committing to them, but they cannot be relied on for production
behavior — they are explicitly time-boxed.

### How to choose

| Question | Approach |
|---|---|
| "Should every Trade BPP carry this field?" | Approach 1 — governed zone |
| "Do I just need this for my own app's internal use?" | Approach 2 — participant zone |
| "Is the SWG still figuring out whether this is a good idea?" | Approach 3 — experimental zone |
| "Is this a new endpoint, not a field?" | Not an extension — see the next section on `/raise` and `/reconcile` |
| "Do I want to add a top-level field to a Beckn object directly?" | Not allowed. Bags are the only extension surface. |

The full details of each approach — including pack structure, context
disclosure rules, size caps, and the promotion path between zones —
are described in [`docs/03-EXTENSION_MODEL.md`](docs/03-EXTENSION_MODEL.md).

---

## Beckn extensions ION introduces — new endpoints

Beyond bag attributes, ION introduces two new endpoints. These are
additions, not replacements — every Beckn endpoint behaves exactly as
upstream Beckn specifies.

### `/raise` — grievance and complaint endpoint

Beckn defines `/support` for support interactions but does not define a
formal grievance pathway. ION adds `/raise` (and its callback
`/on_raise`) for raising a grievance against any entity in a
transaction — a contract, a resource, a participant, an offer.

Example use cases:

- A buyer raises a grievance against a seller for non-delivery
- A seller raises a grievance against a logistics provider for a
  damaged shipment
- A participant raises a regulatory concern about another participant's
  behavior

Why this is an endpoint and not a `/support` extension: grievances have
distinct semantics from support — they trigger formal SLAs, escalation
paths, and dispute resolution workflows that ION's policy registry
governs. Carrying them on `/support` would conflate "I have a question"
with "I'm filing a formal complaint." A separate endpoint keeps the
boundary clean.

Companion endpoints `/raise_status`, `/on_raise_status`,
`/raise_details`, and `/on_raise_details` provide grievance lifecycle
management.

### `/reconcile` — financial true-up endpoint

Beckn carries payment information through the standard transaction
flow. ION adds `/reconcile` (and `/on_reconcile`) for financial true-up
between BAP and BPP after a transaction completes — confirming
settlement amounts, applying penalties from the policy registry,
reconciling against payment provider records.

Example use cases:

- BAP and BPP confirm the final settled amount after applying
  cancellation penalty rules
- Reconciliation against QRIS or BI-FAST settlement records at
  end-of-day
- Resolving discrepancies between BAP-recorded and BPP-recorded
  transaction values

Why this is an endpoint and not in the standard transaction flow:
reconciliation often happens *after* the transaction lifecycle is
complete (sometimes hours or days later, often in batches), against a
different cadence than real-time transactions. Carrying it on the main
flow would conflate transaction-time concerns with settlement-time
concerns.

Both endpoints follow Beckn's request/callback pattern with the same
Context, signing, and acknowledgement semantics as upstream Beckn. They
are documented as part of ION's extension surface, with full schema
specifications under [`schema/endpoints/`](schema/endpoints/).

---

## Repository structure

The repository is organized to reflect what ION owns and how that
content is governed. Each top-level folder serves a distinct purpose;
together they cover everything ION publishes.

```
ion-specs/
│
├── README.md                ← you are here
├── EXECUTIVE_SUMMARY.md     ← shorter version of this file
├── REPO_STATE.md            ← what is here today vs. what is yet to come
├── CHARTER.md               ← ION's constitutional document
├── CHANGELOG.md             ← release history
├── CONTRIBUTING.md          ← how to propose changes
│
├── docs/                    ← human-readable documentation (numbered)
│   ├── 01-OVERVIEW.md
│   ├── 02-GLOSSARY.md
│   ├── 03-EXTENSION_MODEL.md
│   ├── ...
│   ├── sectors/             ← brief overview, one per sector
│   └── patterns/            ← developer walkthroughs (see below)
├── annexes/                 ← machine-readable governance artifacts
├── schema/                  ← OpenAPI 3.1.1 schema packs
├── policies/                ← machine-enforceable policy registry
├── errors/                  ← ION error code registry
├── governance/              ← Council and SWG charters, decision log
└── tools/                   ← verifiers and registry generators
```

Each folder is explained below, with the *reason* it exists separated
from the contents.

### `docs/` — human-readable specifications

Long-form, narrative documents that explain ION's design and usage.
These are written for humans to read end-to-end, not for tools to parse.

The documents are numbered. A reader new to ION should read them in
order; readers who know Beckn can skip to `02-` or later. The
[`docs/README.md`](docs/README.md) has reading paths by audience.

```
docs/
├── 01-OVERVIEW.md             ← what ION is, in one document
├── 02-GLOSSARY.md             ← controlled vocabulary
├── 03-EXTENSION_MODEL.md      ← how ION extends Beckn (the central document)
├── 04-BECKN_BAGS.md           ← reference: the twelve bags ION may extend
├── 05-SCHEMA_STYLE_GUIDE.md   ← rules every schema pack follows
├── 06-CLASSIFICATION.md       ← how ION's category taxonomy works
├── 07-ONBOARDING.md           ← joining ION as a participant
├── 08-CONFORMANCE.md          ← proving you meet ION's rules
├── 09-DISPUTE_RESOLUTION.md   ← how grievances are filed and handled
├── sectors/                   ← brief overview, one per sector
│   ├── TRADE.md
│   ├── LOGISTICS.md
│   ├── MOBILITY.md
│   ├── HOSPITALITY.md
│   ├── FINANCE.md
│   └── SERVICES.md
└── patterns/                  ← developer walkthroughs (see below)
```

Why this folder exists: schemas and policies in machine-readable form
are not enough. Engineers, regulators, and SWG members need narrative
explanations of how the pieces fit together. Sector documents are
brief — they cover scope and entity model — not exhaustive guides.

**A note on examples.** Every pack, every endpoint, every pattern,
and every variation in ION's specifications carries worked examples.
This is a network-wide commitment, not a per-folder afterthought.
Examples live next to the thing they illustrate (in the pack's
`examples/` folder, in the pattern walkthrough document, in the policy
terms file). When something does not have an example, it is
incomplete.

#### `docs/patterns/` — developer walkthroughs

Patterns are *developer aids*: walkthroughs that show how ION's
schemas, policies, endpoints, and Beckn primitives compose into a
typical transaction. They are reading material, not protocol.

```
docs/patterns/
└── sectors/
    ├── trade/                   ← storefront, live-commerce, digital-goods,
    │                              made-to-order, subscription, business-credit,
    │                              auctions, marketplace, cross-border, etc.
    ├── logistics/               ← parcel, hyperlocal, freight, warehouse,
    │                              roro, cross-border
    ├── mobility/
    ├── hospitality/
    ├── finance/
    └── services/
```

Each sector folder contains:
- A pattern document per common transaction shape (the typical happy
  flow)
- A `variations/` sub-folder with documents for cancellation, returns,
  return-to-sender, exception flows, mid-transaction updates, etc.

A *pattern* document walks through the typical happy flow of a
transaction shape end-to-end: which Beckn endpoints are called in what
order, which policy IRIs apply, which Beckn bags carry which ION
fields (in which zone), which fields participants typically populate,
and what the payloads look like at each step — with worked examples.

A *variation* document walks through a deviation from a pattern. It
shows how the deviation grafts onto an existing pattern at the point
where it occurs, with worked examples for the changed payloads.

Why these are documents, not specifications: the same transaction
could be implemented many ways, all conformant with the protocol.
Patterns capture *common implementations* so developers do not have to
discover them from scratch. They are not requirements. A participant
who implements the underlying protocol correctly is conformant whether
or not they organize their code along the pattern library.

Patterns may be added, refined, or retired as ION learns more about
real-world transactions. Such changes do not affect protocol
conformance.

### `annexes/` — machine-readable governance artifacts

Versioned, machine-parsable artifacts that ION publishes as authoritative
references. Each annex has a human-readable companion (`.md`) and a
machine-readable form (`.json` or `.yaml`).

```
annexes/
├── annex-a-sector-registry.{md,json}            ← the six sectors
├── annex-b-classification-schemes.{md,json}     ← which schemes ION recognizes
├── annex-c-categories/
│   ├── trade.v1.json                            ← ion:trade category list
│   ├── logistics.v1.json                        ← ion:logistics category list
│   └── ...                                      ← one per sector
├── annex-d-error-codes.{md,json}                ← ION error registry
├── annex-e-policy-iri-registry.{md,json}        ← list of valid policy IRIs
├── annex-f-recognized-credentials.{md,json}     ← halal, BPOM, BPJS, etc.
└── annex-g-bag-extension-rules.{md,json}        ← three-zone rules per bag
```

Why this folder exists: ION's tooling, participant integrations, and
external auditors need machine-readable references for sector codes,
category lists, error codes, recognized credentials, and policy IRIs.
Burying these inside narrative documents makes them unparsable. Each
annex is independently versioned and fetchable from a stable URL.

### `schema/` — OpenAPI 3.1.1 schema packs

The actual schemas ION publishes — what fields ION's extensions add to
each Beckn bag, what shape they have, what is required.

```
schema/
├── core/                    ← cross-sector packs (Indonesian primitives)
│   ├── address/v1/          ← provinsi, RT/RW, postal code
│   ├── identity/v1/         ← NPWP, NIB, business identity
│   ├── payment/v1/          ← QRIS, BI-FAST, COD, BNPL
│   ├── classification/v1/   ← the categories field shape
│   ├── linkage/v1/          ← cross-sector transaction linkage
│   └── ...
├── sectors/                 ← per-sector packs
│   ├── trade/
│   │   ├── offer/v1/
│   │   ├── resource/v1/
│   │   ├── contract/v1/
│   │   └── ...
│   ├── logistics/
│   ├── mobility/
│   ├── hospitality/
│   ├── finance/
│   └── services/
└── endpoints/               ← ION-introduced endpoints
    ├── raise/v1/
    └── reconcile/v1/
```

Each pack folder contains `attributes.yaml` (the OpenAPI/JSON Schema
definitions), `context.jsonld` (the JSON-LD context for the pack's
fields), `vocab.jsonld` (the linked-data vocabulary), `examples/`, and
a `README.md`.

Why two layers: a cross-sector layer (`core/`) and a sector-specific
layer (`sectors/`). Indonesian primitives like NPWP and QRIS apply
everywhere; trade-specific fields like return policy or warranty apply
only in Trade. Splitting them keeps each pack focused.

### `policies/` — machine-enforceable policy registry

Sellers do not author policy prose. They declare a *policy IRI* on each
offer (e.g., `ion://policy/return/standard/7d-sellerpays`) which
resolves to a structured terms document in this folder. The terms
document defines exactly what the policy means and how it is enforced.

```
policies/
├── cross-sector/            ← policies used across sectors
│   ├── dispute/v1/
│   ├── grievance-sla/v1/
│   ├── payment-terms/v1/
│   └── cancellation/v1/
├── sectors/
│   ├── trade/
│   │   ├── return/v1/
│   │   ├── warranty/v1/
│   │   └── penalty/v1/
│   └── logistics/
│       ├── evidence/v1/
│       ├── insurance/v1/
│       ├── sla/v1/
│       ├── re-attempt/v1/
│       └── ...
└── registry.json            ← generated aggregate of all policy IRIs
```

Why this folder exists: a single network with thousands of merchants
cannot tolerate per-seller policy prose. Disputes become unparsable.
Policies-as-IRIs let merchants pick from a published menu, ION's
infrastructure enforce uniformly, and disputes resolve against
predictable rules.

A note on implementation: how each policy is actually enforced —
whether through Rego rules, JSON Schema, declarative YAML, or
operational policy — is a downstream concern. The structure here
defines *what* a policy says; how it is enforced may differ across
ION's infrastructure components and may evolve.

### `errors/` — ION error code registry

Errors are organized by category, each owning a numeric range:

| Range | Category |
|---|---|
| `ION-1xxx` | Transport |
| `ION-2xxx` | Catalog |
| `ION-3xxx` | Transaction |
| `ION-4xxx` | Fulfillment |
| `ION-5xxx` | Post-order |
| `ION-6xxx` | Settlement |
| `ION-7xxx` | Network |
| `ION-8xxx` | Schema |
| `ION-9xxx` | System |

A developer receiving error `ION-5xxx` knows immediately it is a
post-order error without looking it up. The numbering is part of the
contract.

Each error entry carries its code, HTTP status, title (Bahasa
Indonesia and English), description, the affected field and APIs, the
schema or pattern it relates to, and a resolution hint. A generated
`registry.json` aggregates all entries; tooling under `tools/` builds
it from the per-category YAML files.

Why this folder exists: a network of thousands of participants cannot
tolerate ad-hoc error strings. Two participants reporting the same
problem must use the same code; two pieces of tooling must agree on
what each code means. The registry makes that possible.

### `governance/` — Council and SWG charters, decision log

```
governance/
├── council-charter.md
├── working-groups/
│   ├── trade-swg.md
│   ├── logistics-swg.md
│   ├── mobility-swg.md
│   ├── hospitality-swg.md
│   ├── finance-swg.md
│   └── services-swg.md
└── decision-log/
    ├── 2026-Q1-001-six-sector-launch.md
    ├── 2026-Q1-002-sectors-cut-by-transactional-shape.md
    ├── 2026-Q1-003-three-zone-bag-design.md
    └── ...
```

Why this folder exists: the *reasoning* behind ION's decisions is
itself a public artifact. Years from now, when someone asks "why are
sectors cut by transactional shape and not by industry?", the answer is
in the decision log. Without it, ION's institutional memory walks out
the door whenever a member rotates.

### `tools/` — verifiers and registry generators

```
tools/
├── verify_schema.py         ← checks schemas against the style guide
├── verify_beckn_alignment.py ← checks ION schemas against current Beckn
├── verify_inventory.py      ← checks pack-level files agree with composed registry
├── generate_registry.py     ← builds policies/registry.json
└── generate_categories.py   ← builds annex-c category JSONs from sector inputs
```

Why this folder exists: ION's correctness depends on schemas, policies,
and registries staying in sync. Tooling automates the consistency
checks; without it, drift is inevitable.

---

## Where to start, by reader

**If you are new to ION (or to Beckn):**
1. [`EXECUTIVE_SUMMARY.md`](EXECUTIVE_SUMMARY.md)
2. [`docs/01-OVERVIEW.md`](docs/01-OVERVIEW.md)
3. [`docs/02-GLOSSARY.md`](docs/02-GLOSSARY.md)
4. [`docs/03-EXTENSION_MODEL.md`](docs/03-EXTENSION_MODEL.md)

**If you know Beckn and want to understand ION's specific additions:**
1. [`docs/03-EXTENSION_MODEL.md`](docs/03-EXTENSION_MODEL.md)
2. [`docs/04-BECKN_BAGS.md`](docs/04-BECKN_BAGS.md)
3. [`docs/05-SCHEMA_STYLE_GUIDE.md`](docs/05-SCHEMA_STYLE_GUIDE.md)
4. [`schema/endpoints/raise/v1/`](schema/endpoints/raise/v1/) and
   [`schema/endpoints/reconcile/v1/`](schema/endpoints/reconcile/v1/)

**If you are building a Buyer App, Seller App, or Technology Service Provider:**
1. [`EXECUTIVE_SUMMARY.md`](EXECUTIVE_SUMMARY.md)
2. [`docs/07-ONBOARDING.md`](docs/07-ONBOARDING.md)
3. The relevant sector document under [`docs/sectors/`](docs/sectors/)
4. [`docs/08-CONFORMANCE.md`](docs/08-CONFORMANCE.md)
5. The relevant pattern walkthroughs under [`docs/patterns/`](docs/patterns/)

**If you are a regulator or government counterpart:**
1. [`EXECUTIVE_SUMMARY.md`](EXECUTIVE_SUMMARY.md)
2. [`CHARTER.md`](CHARTER.md)
3. [`docs/09-DISPUTE_RESOLUTION.md`](docs/09-DISPUTE_RESOLUTION.md)
4. [`governance/`](governance/)

**If you are contributing to a specification:**
1. [`CONTRIBUTING.md`](CONTRIBUTING.md)
2. [`docs/03-EXTENSION_MODEL.md`](docs/03-EXTENSION_MODEL.md) — to know which of the three approaches your change uses
3. [`docs/05-SCHEMA_STYLE_GUIDE.md`](docs/05-SCHEMA_STYLE_GUIDE.md)
4. [`governance/decision-log/`](governance/decision-log/)

---

## Versioning

Each artifact in this repository is independently versioned. A
specification's version is part of its path
(`schema/sectors/trade/offer/v1/`). Breaking changes increment the
version with a deprecation window for the previous version.

Rule: ION runs a single canonical version at any time. There is no
per-participant version pinning. When a new version is published,
participants are given advance notice (typically 90 days) before the
change takes effect.

---

## Relationship to Beckn upstream

ION does not fork Beckn. ION's contributions to Beckn (proposed
extension points, error code patterns, linkage primitives) are
submitted upstream through Beckn's own governance process at
[github.com/beckn/protocol-specifications-v2](https://github.com/beckn/protocol-specifications-v2).

When Beckn is updated upstream, ION re-validates its profile against
the new release and publishes any compatibility notes. Tooling under
[`tools/`](tools/) includes a verifier that checks ION schemas against
the current Beckn specification.

---

## How ION is governed

Two bodies govern ION: the **ION Council** and **Sector Working
Groups**. There is no separate technical staff layer; operational work
is the responsibility of these bodies.

The Council handles cross-sector and architectural matters: the sector
list, the extension model, the three-zone bag design, ION-wide
policies, error code conventions, and the conformance regime.

Sector Working Groups handle sector-specific matters: the
ion:{sector} category list, sector-specific schema packs, sector
policies, sector patterns and variations, and sector conformance
annexes.

Reasoning behind decisions is preserved in
[`governance/decision-log/`](governance/decision-log/) so that ION's
institutional memory is a public artifact, not a person-dependent
asset.

---

## License

Specifications and documentation are released under
**[CC-BY-NC-SA 4.0](LICENSE)** to align with Beckn's licensing.

Tooling and reference code are released under **Apache 2.0**, noted
per file.

---

## Status and contact

This repository is **draft**. All artifacts are subject to change as
ION's specifications evolve. Implementations against draft artifacts
are at the implementer's risk.

- General questions: open a Discussion on this repository
- Specification proposals: see [`CONTRIBUTING.md`](CONTRIBUTING.md)
- Press, regulatory, or partnership enquiries: *(contact details
  to be added)*

---

*Indonesia Open Network — proposed specifications, draft.*
