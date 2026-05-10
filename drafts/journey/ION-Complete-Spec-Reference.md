# ION — Complete Specification Reference

**What this document is:** A plain-English reference for every structural concept in ION. It covers every sector, every segment, every CRC, every pattern, every variant — with examples throughout. Read it once and you understand the complete structure of the network.

**Who this is for:** Anyone joining ION — business owners, platform builders, regulators, investors, partner networks. No prior knowledge of Beckn Protocol required.

**What this is not:** A technical API specification. For API calls, request/response payloads, and JSON schemas, see the Beckn Base Spec sheet and ION Extensions sheet.

---

# PART 1 — HOW ION WORKS

## 1.1 The Network Promise

ION (Indonesia Open Network) is built on one promise: **any seller app and any buyer app can transact with each other without a bilateral integration agreement.**

A pharmacy joins ION once. It becomes immediately reachable by every health app, every general marketplace, every hyperlocal delivery app on the network — without negotiating with each one separately.

A buyer app connects to ION once. It gets access to every merchant on the network — pharmacies, grocery stores, fashion sellers, logistics providers, financial institutions — without bilateral deals.

This only works because every participant speaks the same structured language. ION defines that language. This document explains it.

---

## 1.2 The Three-Plane Model

Every participant on ION operates across three distinct planes. Think of them as three different questions that need three different kinds of answers.

```
PLANE 1 — BUSINESS / POLICY COMPLIANCE
  The question: Who are you, and what are you allowed to do?
  Governed by: Indonesian law, KBLI, sectoral licences, ION governance policy
  Verified at: Onboarding, and periodically re-verified

PLANE 2 — CATALOGUE
  The question: What are you selling or offering?
  Governed by: ION Sector Working Groups
  Verified at: catalog/publish time by the ION CDS

PLANE 3 — PROTOCOL / TECHNICAL COMPLIANCE
  The question: How have you built your system?
  Governed by: Beckn Protocol v2.0, ION extensions, conformance test suites
  Verified at: Pre-go-live through test execution
```

These three planes are deliberately separated. A change in business category does not require a rebuild. A new product category does not require regulatory re-classification. Each plane has its own rules, its own governance, and its own verification process.

---

## 1.3 The Reference Path

Any point in ION's structure can be expressed as a single reference path:

```
sector / segment-code / crc-code / pattern / variant
```

Example: `trade / TRD-04 / TRC-health-beauty / storefront / delivery-time-kyc`

This means: within the Trade sector, under the Pharmacy & Health Retail segment (TRD-04), selling a Health & Beauty item (TRC-health-beauty), using the standard storefront transaction pattern, with the delivery-time-kyc variant applied (identity verification at door).

A regulator, a developer, and a business owner reading that path are all pointing at exactly the same thing.

---

## 1.4 The Classification Test

When classifying any business, item, or transaction on ION, one question resolves most ambiguity:

**"What does the buyer end up with?"**

```
Buyer ends up owning a physical thing?     →  Trade
Buyer receives a time-bounded reservation? →  Hospitality
Buyer gets transported from A to B?        →  Mobility
Buyer receives money or a financial product? → Finance
Buyer receives human labour or expertise?  →  Services
Goods are moved or stored?                 →  Logistics
```

Examples:
- A box of Panadol → buyer owns it → **Trade**
- A restaurant meal at a table → time-bounded capacity → **Hospitality**
- A GoRide journey → movement of a person → **Mobility**
- A personal loan → financial instrument → **Finance**
- A doctor consultation → human expertise → **Services**
- A JNE parcel delivery → movement of goods → **Logistics**

---

# PART 2 — THE BUSINESS / POLICY PLANE

## 2.1 Organisation

The top-level legal entity registered on ION. This is the company, cooperative, or sole proprietorship with a valid NIB (Nomor Induk Berusaha — Business Registration Number).

**Key point:** One organisation can hold multiple roles. Gojek, for example, holds roles in Mobility (GoRide, GoCar), Hospitality (GoFood platform), and Finance (GoPay) simultaneously. Each role in each sector is independently authorised.

**KBLI (Klasifikasi Baku Lapangan Usaha Indonesia)** — Indonesia's standard business classification code — is mapped at the Organisation level, scoped to each sector the organisation operates in. ION maintains the reference mapping. Enforcement follows Indonesian regulation — ION does not enforce KBLI compliance itself.

*Important operational note: The KBLI 2020 → 2025 migration deadline is 18 June 2026 per BPS Reg. 7/2025. Organisations must update their NIB/OSS records accordingly.*

---

## 2.2 Roles

Four roles are recognised on ION:

| Role | What they do | Example |
|---|---|---|
| **Seller** | The merchant — owns the catalogue, inventory, and fulfilment obligation | Apotek Sehat Mandiri (Bu Sari's pharmacy) |
| **Seller App** | The platform connecting Sellers to ION — hosts the Provider Platform (BPP) | GoApotik, Kimia Farma App |
| **Buyer App** | The consumer-facing application — hosts the Consumer Platform (BAP) | Halodoc, Tokopedia, GrabHealth |
| **TSP** | Technology Service Provider — provides technical infrastructure *to* a Seller App or Buyer App. Does not transact directly | A payment gateway, an OMS provider |

**Critical distinction — Seller vs Seller App:** Bu Sari is the **Seller** (the merchant). The app Dinda's team builds to connect Bu Sari to ION is the **Seller App**. One Seller App can serve thousands of Sellers. A Seller can switch Seller Apps without losing their ION registration. These are two distinct roles with separate authorisation.

**TSP:** A TSP is always attached to a Seller App or Buyer App — it does not participate directly in transactions. A TSP can serve multiple Seller Apps or Buyer Apps, and can hold one `TSP / sector` profile per sector it serves.

---

## 2.3 Profile

**A Profile = one Role + one Sector.**

This is ION's unit of authorisation. Every Profile is independently authorised, independently governed, and independently mapped to KBLI codes.

```
Profile code format:  role / sector

Examples:
  seller / trade          Bu Sari's pharmacy on ION
  SA / trade              A seller app operating in Trade
  BA / mobility           A ride-hailing buyer app
  TSP / logistics         A tech provider serving logistics platforms
  seller / hospitality    A restaurant on ION
```

**One organisation, multiple profiles:**

| Organisation | Profile 1 | Profile 2 | Profile 3 |
|---|---|---|---|
| Gojek | `BA / mobility` (GoRide, GoCar) | `SA / hospitality` (GoFood platform) | `SA / finance` (GoPay) |
| A large hospital | `seller / services` (consultations) | `seller / trade` (pharmacy products) | — |
| A tech company | `SA / trade` (marketplace platform) | `TSP / trade` (fulfilment tech) | `TSP / logistics` (routing engine) |

Each profile requires separate KYC, separate KBLI verification, and separate category licences where applicable.

---

## 2.4 Sectors

Six sectors, each with a defining principle:

| Sector | Code | Principle | What belongs here |
|---|---|---|---|
| **Trade** | `ion:trade` | Buyer ends up **owning** a thing | Groceries, medicines, electronics, fashion, automotive parts, B2B wholesale |
| **Hospitality** | `ion:hospitality` | **Time-bounded reservation** of capacity | Hotel rooms, restaurant tables, food delivery, event bookings, wellness sessions |
| **Logistics** | `ion:logistics` | **Movement and storage** of goods | Courier delivery, freight, warehousing, freight forwarding |
| **Mobility** | `ion:mobility` | **Movement of people** | Ride-hailing, scheduled transport, aviation, vehicle rental |
| **Finance** | `ion:finance` | **Money and financial instruments** | Lending, insurance, payments, investment |
| **Services** | `ion:services` | **Human labour and expertise** | Professional services, healthcare consultations, education, home services |

The sector code (`ion:trade`, `ion:hospitality` etc.) travels in `context.domain` on every Beckn API call, routing each transaction to the correct governance rules, policy attachments, and regulatory checks.

---

## 2.5 Segments

Segments are the business category groupings within a Sector. They define what a business is permitted to do within its sector, which Indonesian regulations apply, and which KBLI codes are associated with that activity.

**Important:** Segments do NOT mandate which products a seller can list. That is the seller's decision, governed by their operating licences. Segments are a business classification — not a product constraint.

### Trade Segments (TRD)

| Code | Segment Name | What businesses belong here | Key KBLI codes | Licences required |
|---|---|---|---|---|
| **TRD-01** | Grocery, FMCG & Quick Commerce | Supermarkets, minimarkets, convenience stores, q-commerce platforms, fresh produce sellers | 47110, 47190, 47211, 47212, 47221 | NIB, SIUP (if applicable) |
| **TRD-02** | E-commerce & Digital Marketplaces | Online marketplaces, multi-category e-retailers, live commerce platforms, digital goods sellers | 47911, 47912, 47913, 47914, 47915 | NIB; marketplace operators need additional Kominfo registration |
| **TRD-03** | Specialty Retail | Single-category specialist stores — fashion, electronics, furniture, sports, toys, arts & crafts, books | 47510, 47540, 47591, 47710, 47720 | NIB; category-specific for regulated items |
| **TRD-04** | Pharmacy & Health Retail | Licensed pharmacies, health product retailers, medical device sellers | 47722, 47730, 47740, 47750 | PBF licence (Pedagang Besar Farmasi), SIA/SIK (apoteker) |
| **TRD-05** | Automotive Sales & Parts | Car and motorcycle dealers, auto parts retailers, tyre shops, lubricant sellers | 45111, 45113, 45191, 45311, 45319 | NIB; dealer licence for vehicle sales |
| **TRD-06** | Wholesale & Supply Trade | B2B distributors, raw material suppliers, industrial equipment sellers, bulk commodity traders | 46100–46900 series | SIUP-B (wholesale trading licence); may require industry-specific permits |
| **TRD-07** | Energy & Commodity Trade | Petroleum product distributors, coal and mineral traders, energy commodity platforms | 46610, 46620, 46900 | SIUP-B; sector-specific ESDM permits for energy |

### Hospitality Segments (HSP)

| Code | Segment Name | What businesses belong here | Key KBLI codes |
|---|---|---|---|
| **HSP-01** | Hotels & Accommodation | Star-rated hotels, boutique hotels, resorts, serviced apartments | 55110, 55120 |
| **HSP-02** | Short-Stay & Alternative Accommodation | Villas, homestays, guesthouses, glamping, Airbnb-style rentals | 55190, 55201 |
| **HSP-03** | Travel Platforms & OTAs | Online travel agents, hotel booking platforms, travel aggregators | 55400, 79110, 79120 |
| **HSP-04** | F&B — Restaurants & Dining | Full-service restaurants, cafes, food courts, street food (registered) | 56101, 56102, 56103 |
| **HSP-05** | Food Delivery & Online Ordering | Cloud kitchens, dark kitchens, food delivery platforms, GoFood/GrabFood equivalents | 56400 |
| **HSP-06** | Events, Catering & MICE | Event organisers, catering companies, MICE venues, wedding organisers | 56210, 82301, 82302 |
| **HSP-07** | Recreation, Attractions & Wellness | Theme parks, spas, gyms, fitness centres, golf courses, tourist attractions | 93110, 93121, 93199, 96040 |

### Logistics Segments (LOG)

| Code | Segment Name | What businesses belong here | Key KBLI codes |
|---|---|---|---|
| **LOG-01** | Last-Mile & Courier Delivery | Courier companies (JNE, J&T, SiCepat), same-day delivery platforms, hyperlocal delivery | 53100, 53200, 53300 |
| **LOG-02** | Freight Transport | Trucking companies, rail freight, domestic air cargo | 49410, 49420, 51210 |
| **LOG-03** | Warehousing & Fulfilment | Fulfilment centres, cold storage, bonded warehouses | 52100, 52102, 52103 |
| **LOG-04** | Sea & Air Freight | International shipping lines, Ro-Ro operators, international air cargo | 50111, 50112, 51210 |
| **LOG-05** | Freight Support & Forwarding | Customs brokers, freight forwarders, 3PL providers | 52291, 52292, 52299 |

### Mobility Segments (MOB)

| Code | Segment Name | What businesses belong here | Key KBLI codes |
|---|---|---|---|
| **MOB-01** | Ride-Hailing & On-Demand Transport | Gojek, Grab, ride-hailing platforms, ojek platforms | 49230 |
| **MOB-02** | Mass Transit & Scheduled Transport | Bus operators, train services, ferry operators | 49210, 49220, 50211 |
| **MOB-03** | Aviation — Passenger | Airlines, charter flight operators | 51101, 51102 |
| **MOB-04** | Future Mobility | Electric vehicle platforms, autonomous transport, shared micromobility | 49230, 77110 (evolving) |

### Finance Segments (FIN)

| Code | Segment Name | What businesses belong here | Key KBLI codes | OJK/BI Licence |
|---|---|---|---|---|
| **FIN-01** | Banking | Commercial banks, digital banks, rural banks (BPR) | 64110, 64120, 64130 | OJK banking licence |
| **FIN-02** | Lending & Consumer Credit | Multifinance companies, P2P lending platforms, BNPL providers | 64921, 64922, 64923 | OJK multifinance/P2P licence |
| **FIN-03** | Payments & E-money | Payment system operators, e-wallet providers, QRIS acquirers | 64950, 64960 | Bank Indonesia payment system licence |
| **FIN-04** | Insurance & Risk | Life insurers, general insurers, reinsurers, insurance brokers | 65110, 65120, 65200 | OJK insurance licence |
| **FIN-05** | Capital Markets & Wealth | Securities companies, investment managers, robo-advisors | 66110, 66120, 66130 | OJK capital markets licence |
| **FIN-06** | Digital Assets & Carbon Finance | Digital asset exchanges, carbon credit platforms, tokenised asset platforms | 66140, 66180 | OJK digital asset licence |
| **FIN-07** | Financial Infrastructure & Support | Payment gateways, switching networks, financial data providers | 66190, 66199 | BI/OJK as applicable |

### Services Segments (SVC)

| Code | Segment Name | What businesses belong here | Key KBLI codes |
|---|---|---|---|
| **SVC-01** | Technology & Digital Services | Software development, IT services, SaaS, cloud, digital marketing | 62010, 62020, 63110, 63120 |
| **SVC-02** | Telecom & Connectivity | Mobile operators, ISPs, enterprise connectivity providers | 61100, 61200, 61300 |
| **SVC-03** | Professional & Advisory Services | Law firms, accounting firms, management consultants, HR services | 69100, 69200, 70201, 78100 |
| **SVC-04** | Marketing, Media & Creative | Advertising agencies, content studios, media buying, event production | 73100, 73200, 74100, 90001 |
| **SVC-05** | Healthcare & Wellness Services | Clinics, hospitals (service side), telemedicine platforms, physiotherapy, mental health | 86101, 86102, 86103, 86904 |
| **SVC-06** | Real Estate & Construction Services | Property agents, construction contractors, architecture firms, interior design | 41010, 43210, 68100, 68200 |
| **SVC-07** | Automotive & Vehicle Services | Car workshops, tyre fitting, vehicle inspection, car wash | 45201, 45202, 45203 |
| **SVC-08** | Facilities, Support & Personal Services | Cleaning services, security, pest control, personal styling, home maintenance | 80100, 81210, 81220, 96010 |

---

# PART 3 — THE CATALOGUE PLANE

## 3.1 What the Catalogue Plane Does

The Catalogue Plane is the bridge between the business classification (who you are) and the transaction flow (how you transact). It contains two layers:

- **CRC (Catalogue Resource Category)** — classifies the item being sold
- **Resource / Item** — the specific product or service being transacted

The Catalogue Plane is governed by ION Sector Working Groups and verified at `catalog/publish` time by the ION CDS (Catalogue Discovery Service).

---

## 3.2 CRC — Catalogue Resource Category

The CRC is the answer to one question: **"What kind of thing are you selling?"**

It serves exactly two purposes:
1. **Enforce catalogue completeness** — determines which fields are mandatory on the resource at publish time
2. **Signal to buyer apps** — tells BAPs how to render, filter, and discover this item

**The CRC is not a constraint on what a Segment can sell.** A pharmacy (TRD-04) can list mineral water under `TRC-food-bev` or protein powder under `TRC-food-bev`. ION does not restrict which CRCs a Segment may use — that is the seller's decision. ION only enforces the attribute schema once a CRC is declared.

**CRC vocabulary for Trade** uses GPC (Google Product Category) L0 names as the reference vocabulary — because Indonesian sellers on Tokopedia and Shopee already know these names. For non-Trade sectors, ION defines its own vocabulary independently.

### Trade CRCs (TRC)

| Code | ION Name | GPC L0 Reference | Status | What it covers |
|---|---|---|---|---|
| `TRC-fashion` | Fashion & Accessories | Apparel & Accessories | ACTIVE | Clothing, footwear, bags, jewellery, watches, eyewear, traditional dress (batik, kebaya), sportswear accessories |
| `TRC-electronics` | Electronics & Gadgets | Electronics · Cameras & Optics | ACTIVE | Phones, laptops, TVs, cameras, audio, gaming, wearables, home appliances, smart devices. Cameras merged into Electronics — Indonesian market practice. |
| `TRC-food-bev` | Food, Beverages & Tobacco | Food, Beverages & Tobacco | ACTIVE | Packaged food, beverages, snacks, condiments, coffee, health drinks, protein powder, tobacco. Includes fresh produce. Note: hot/prepared restaurant food is Hospitality, not this CRC. |
| `TRC-health-beauty` | Health & Beauty | Health & Beauty | ACTIVE | OTC medicines, prescription medicines, medical devices, supplements, skincare, cosmetics, haircare, hygiene products |
| `TRC-home-garden` | Home & Garden | Home & Garden | ACTIVE | Household appliances, kitchen equipment, decor, lighting, garden tools, cleaning products, bedding, storage |
| `TRC-furniture` | Furniture | Furniture | ACTIVE | Indoor and outdoor furniture. Kept separate from Home & Garden because of different logistics (oversized/freight), different attributes (dimensions, assembly), different regulatory requirements (SNI) |
| `TRC-automotive` | Vehicles & Parts | Vehicles & Parts | ACTIVE | Motorcycles, cars, auto parts, tyres, lubricants, helmets, vehicle care products |
| `TRC-sports` | Sporting Goods | Sporting Goods | ACTIVE | Sports equipment, fitness gear, outdoor equipment, bicycles, water sports, team sports |
| `TRC-toys-baby` | Toys, Games & Baby | Toys & Games · Baby & Toddler | ACTIVE | Toys, board games, puzzles, educational materials, baby clothing, feeding, nursery items, strollers. Merged because seller profiles significantly overlap in Indonesia. |
| `TRC-media` | Books & Media | Media | ACTIVE | Physical books, magazines, music CDs, DVDs, Blu-rays, physical media formats |
| `TRC-digital` | Digital Goods | Software | ACTIVE | Software licences, pulsa (mobile top-up), data packages, PLN token, game credits, digital vouchers, gift cards, e-books, digital subscriptions |
| `TRC-industrial` | Industrial & Wholesale | Business & Industrial | ACTIVE | Raw materials, industrial equipment, office machinery, packaging, safety equipment, bulk commodities |
| `TRC-arts` | Arts, Crafts & Collectibles | Arts & Entertainment | ACTIVE | Art supplies, craft materials, handmade products, collectibles, antiques, musical instruments, kerajinan tangan (Indonesian handicrafts) |
| `TRC-religious` | Religious & Ceremonial | Religious & Ceremonial | ACTIVE | Prayer equipment (sajadah, tasbih, mukena), religious texts, ceremonial supplies for Lebaran, Natal, Imlek, etc. Significant category in Indonesia. |
| `TRC-luggage` | Luggage & Bags | Luggage & Bags | ACTIVE | Suitcases, travel bags, backpacks, laptop bags, school bags. Separate from Fashion because different attributes (volume, wheel type) and different buyer intent (travel utility vs fashion). |
| `TRC-pets` | Animals & Pet Supplies | Animals & Pet Supplies | INACTIVE | Pet food, accessories, grooming. Recognised but low volume at launch — no attribute schema enforcement yet. |
| `TRC-hardware` | Hardware & Tools | Hardware | INACTIVE | Hand tools, power tools, building materials, plumbing, electrical supplies. Low volume — sellers may use TRC-industrial. |
| `TRC-office` | Office Supplies | Office Supplies | INACTIVE | Stationery, office equipment, paper, writing instruments. Low volume — sellers may use TRC-industrial. |
| `TRC-mature` | Mature | Mature | GOVERNED | Adult products. Blocked pending ION Council policy decision on age verification requirements and BAP rendering obligations. |

### Hospitality CRCs (HSC)

| Code | ION Name | Status | What it covers |
|---|---|---|---|
| `HSC-accommodation` | Accommodation | ACTIVE | Hotel rooms, villas, homestays, guesthouses, serviced apartments, glamping. Resource is a bookable unit with check-in/check-out datetime. |
| `HSC-restaurant` | Restaurant & Dining | ACTIVE | Table reservations at restaurants and dining venues. Resource is a time-slot at a specific table or area. |
| `HSC-fnb-delivery` | Food & Beverage Delivery | ACTIVE | Restaurant meals and beverages ordered for delivery or self-pickup. Resource is a prepared food item — made after order. |
| `HSC-events` | Events & Experiences | ACTIVE | Tours, activities, attractions, entertainment, MICE bookings. Resource is a participation slot with defined start time. |
| `HSC-wellness` | Recreation & Wellness | ACTIVE | Gym, spa, salon, fitness classes, pool access. Resource is a time-bounded session. |

### Logistics CRCs (LGC)

| Code | ION Name | Status | What it covers |
|---|---|---|---|
| `LGC-lastmile` | Last-Mile Delivery | ACTIVE | Parcel and hyperlocal delivery. Resource is a shipment slot covering courier, same-day, next-day, on-demand. |
| `LGC-freight` | Freight Transport | ACTIVE | Trucking, rail, domestic air cargo. Resource is a freight capacity booking. |
| `LGC-international` | Sea & Air Freight | ACTIVE | International and domestic sea/air freight including Ro-Ro. Resource is a cargo space booking. |
| `LGC-warehouse` | Warehousing | ACTIVE | Storage and fulfilment. Resource is a capacity commitment covering inbound, storage, and outbound. |
| `LGC-forwarding` | Freight Forwarding | ACTIVE | Customs clearance, freight forwarding, logistics support. Resource is a forwarding service engagement. |

### Mobility CRCs (MOC)

| Code | ION Name | Status | What it covers |
|---|---|---|---|
| `MOC-ride` | Ride-Hailing | ACTIVE | On-demand passenger transport. Resource is a journey booking. |
| `MOC-scheduled` | Scheduled Transport | ACTIVE | Fixed-route scheduled services — buses, trains, ferries, flights. Resource is a seat on a specific departure. |
| `MOC-rental` | Vehicle Rental | ACTIVE | Short or long-term vehicle rental. Resource is a vehicle for a defined period. |
| `MOC-charter` | Charter | INACTIVE | Exclusive vehicle booking. Reserved for future activation. |

### Finance CRCs (FNC)

| Code | ION Name | Status | What it covers |
|---|---|---|---|
| `FNC-lending` | Lending & Credit | ACTIVE | Personal loans, business loans, BNPL, P2P lending. Resource is a credit product with tenure and rate. |
| `FNC-insurance` | Insurance | ACTIVE | Life, health, motor, property, travel insurance. Resource is a policy with defined coverage and premium. |
| `FNC-payments` | Payments & E-money | ACTIVE | Wallet top-up, bill payment, fund transfer. Resource is a payment transaction. |
| `FNC-investment` | Investment | ACTIVE | Mutual funds, stocks, bonds, digital assets. Resource is an investable instrument. |
| `FNC-banking` | Banking Services | INACTIVE | Account opening, banking product activation. Reserved for future activation. |

### Services CRCs (SVRC)

| Code | ION Name | Status | What it covers |
|---|---|---|---|
| `SVRC-tech` | Technology & Digital Services | ACTIVE | Software development, IT services, SaaS, cloud, digital marketing, data services. |
| `SVRC-professional` | Professional & Advisory | ACTIVE | Legal, accounting, consulting, financial advisory, HR. Resource is a professional engagement. |
| `SVRC-healthcare` | Healthcare & Wellness Services | ACTIVE | Doctor consultations, physiotherapy, diagnostics, telemedicine, mental health. Resource is a professional service slot. Healthcare *products* (medicines, devices) are Trade, not Services. |
| `SVRC-telecom` | Telecom & Connectivity | ACTIVE | Internet plans, mobile plans, enterprise connectivity. Resource is a service subscription. |
| `SVRC-creative` | Creative & Marketing | ACTIVE | Design, content production, advertising, media buying, events production. |
| `SVRC-home` | Home & Facility Services | ACTIVE | Cleaning, repair, plumbing, electrical, pest control, home installation. Resource is a service appointment. |
| `SVRC-education` | Education & Training | ACTIVE | Tutoring, professional courses, corporate training, e-learning. Resource is a course enrolment or session booking. |
| `SVRC-auto` | Automotive Services | INACTIVE | Vehicle servicing, repair, inspection. Reserved for future activation. |

---

## 3.3 Resource / Item

A Resource is the atomic unit of value in the ION catalogue — a specific product, service slot, or entitlement that a seller publishes and a buyer can purchase.

Every Resource has:
- A **CRC** — which enforces which attributes must be declared
- A **Transaction Type** — the structural nature of what is being transacted
- **Attributes** — the specific fields declared for this item

### The Seven Transaction Types

The Transaction Type is not declared by the seller — it is derived from the resource's attributes and sector membership. It determines which patterns are structurally possible.

| Type | What it is | Examples | Key constraints |
|---|---|---|---|
| **PHYSICAL_GOOD** | Tangible item requiring logistics | Medicines, electronics, groceries, fashion | Needs logistics · has availability signal · can be returned · COD possible |
| **PREPARED_GOOD** | Made after order — no pre-existing inventory | Restaurant meals, custom cakes, bespoke tailoring | Preparation time mandatory · return not possible once prepared · cancellation closes at preparation start |
| **DIGITAL_ENTITLEMENT** | Value or access right delivered electronically | Pulsa, PLN token, game voucher, software licence | No logistics · target validation required · return almost impossible · no COD |
| **SLOT** | Time-bounded unit of capacity. Time is the product. | Hotel booking, doctor appointment, ride, concert ticket | Datetime mandatory · cancellation = primary post-order flow · rescheduling natural |
| **CAPACITY** | Space or movement as the product. Logistics IS the product. | Freight booking, warehouse space, parcel delivery | Weight/volume primary · route mandatory · damage/loss liability primary |
| **FINANCIAL_INSTRUMENT** | Money or financial commitment as the product | Personal loan, insurance policy, mutual fund | KYC mandatory · rate and tenure primary · no physical fulfilment · OJK/BI regulated |
| **PROFESSIONAL_SERVICE** | Human skill or expertise as the product | Legal consultation, IT project, tutoring, home repair | Credentials primary compliance · scope defines contract · IP ownership may apply |

### The Six-Layer Resource Attribute Structure

Every resource on ION is described through six layers. Layers A–E are universal. Layer F is CRC-specific.

```
Layer A — Classification
  @context, @type (always required — identifies the schema pack)

Layer B — Core Identity
  resourceStructure, resourceTangibility, images[], availability,
  ageRestricted, countryOfOrigin, logisticsServiceType, quantity
  (always required for all Trade resources)

Layer C — Physical Profile
  weight, dimensions, brand, productCode
  (required when resourceTangibility = PHYSICAL)

Layer D — Regulatory
  BPOM registrations, SNI, halal certification, HSN code,
  packaged declarations (expiry, manufacturer, net quantity)
  (CRC-driven — which specific regulatory fields appear
   depends on which CRC is declared)

Layer E — Fulfilment Signals
  prescriptionRequired, coldChainRequired, ageRestricted triggers,
  logisticsServiceType, handling flags
  (CRC + attribute driven — some fields trigger mandatory variants)

Layer F — CRC-Specific Schema
  The sub-object that changes entirely by CRC:
  pharmacy.* for TRC-health-beauty (medicine items)
  food.* for TRC-food-bev
  fashion.* for TRC-fashion
  electronics.* for TRC-electronics
  beauty.* for TRC-health-beauty (cosmetics items)
  digital.* for TRC-digital
```

### What ION Mandates vs What Sellers Decide

ION mandates only what the **network cannot function without** or what **Indonesian law requires at point of sale**. Everything else is the seller's decision.

```
ION MANDATES                         SELLER DECIDES
────────────────────────────────     ────────────────────────────────
resourceStructure                    Product description richness
resourceTangibility                  Image count beyond minimum 1
At least 1 image                     Variant depth
availability.status                  Feature highlights
countryOfOrigin                      Comparison data
BPOM/SNI/relevant registration       Internal SKU references
Expiry date (food/pharma)            Recommended use cases
Prescription flag (pharma)           Price history
warranty (electronics)               Social proof content
SNI + POSTEL (electronics)
Energy rating (major appliances)
```

---

# PART 4 — THE PROTOCOL / TECHNICAL PLANE

## 4.1 Patterns

Patterns are **sector-native transaction blueprints** — they describe how a complete transaction (discover → select → confirm → fulfil → settle) operates for a specific commerce scenario. Every Pattern maps to a Sector.

Patterns are guidance, not protocol mandates. They represent ION's recommended implementation model. New patterns can be added as commerce evolves without changing the underlying Beckn protocol.

### Trade Patterns

| Pattern | What it means | Typical Segments |
|---|---|---|
| `storefront` | Buyer browses a seller's catalogue, picks items, completes purchase. Standard retail flow. | TRD-01, TRD-02, TRD-03, TRD-04, TRD-05 |
| `marketplace-listed` | Seller lists on a third-party marketplace; marketplace intermediates discovery. Seller retains fulfilment. | TRD-02, TRD-03, TRD-05 |
| `marketplace-inhouse` | Marketplace owns both listing and fulfilment. | TRD-02 |
| `live-commerce` | Seller runs a live broadcast; buyers discover and purchase during the session. | TRD-01, TRD-02, TRD-03 |
| `digital-goods` | Item delivered is purely digital — no physical fulfilment. | TRD-02 |
| `made-to-order` | Item does not exist until buyer commissions it. Confirmation triggers production. | TRD-03, TRD-05, TRD-06 |
| `subscription` | Buyer commits to recurring delivery or access. Each cycle auto-initiates. | TRD-01, TRD-04 |
| `business-procurement` | Business buyer raises a structured purchase order against a supplier. | TRD-06, TRD-07 |
| `forward-auction` | Seller lists an item; multiple buyers bid up. Highest bid at close wins. | TRD-06, TRD-07 |
| `reverse-auction` | Buyer posts a requirement; multiple sellers bid down. Lowest qualifying bid wins. | TRD-06, TRD-07 |
| `cross-border` | Transaction crosses an international border. Customs, duties, and cross-currency settlement apply. | TRD-02, TRD-06 |
| `government` | Procurement under government or public sector rules. | TRD-06 |

### Hospitality Patterns

| Pattern | What it means |
|---|---|
| `reservation` | Buyer books a time-bound experience at a specific property or venue. |
| `experience-booking` | Buyer books a structured experience — tour, attraction, activity, event. |
| `dine-in-order` | Buyer orders food at a physical table. No prior reservation required. |
| `delivery-order` | Buyer orders food or beverage for delivery. A logistics leg is embedded. |
| `event-commission` | Buyer commissions a catering or event service. Scope negotiated before confirmation. |
| `pass` | Buyer purchases recurring or multi-use access — gym membership, museum pass. |

### Logistics Patterns

| Pattern | What it means |
|---|---|
| `parcel` | A discrete package is picked up from a sender and delivered to a recipient. |
| `hyperlocal` | Same-day or on-demand delivery within a tight geographic radius. |
| `freight` | Large-volume or heavy cargo movement. |
| `warehouse` | A business places goods into managed storage. |
| `roro` | Roll-on/Roll-off vessel movement. |
| `cross-border` | Shipment crosses an international border. |

### Mobility Patterns

| Pattern | What it means |
|---|---|
| `on-demand-ride` | A passenger requests a ride in real time. |
| `scheduled-journey` | A passenger books a seat on a fixed-route service. |
| `charter` | A passenger or group books exclusive use of a vehicle. |
| `pass` | A passenger purchases recurring or multi-use transit access. |

### Finance Patterns

| Pattern | What it means |
|---|---|
| `account-opening` | A customer applies to open a financial account. |
| `loan-application` | A customer applies for credit. |
| `payment` | A payer initiates a transfer of funds to a payee. |
| `policy-issuance` | A customer applies for an insurance policy. |
| `investment-order` | A customer places an order to buy or sell a financial instrument. |
| `service-activation` | A customer activates an ongoing financial service. |

### Services Patterns

| Pattern | What it means |
|---|---|
| `engagement-booking` | A client books a professional for a defined scope of work. |
| `retainer` | A client engages a professional on an ongoing basis. |
| `subscription` | Client subscribes to a recurring digital or operational service. |
| `project-order` | Client commissions a defined deliverable with milestones. |
| `marketplace-listed` | Service provider lists capacity on a platform. |

---

## 4.2 Variants

A Variant is a **named modification of a Pattern** that alters specific flows, fields, or rules without requiring a new Pattern.

**Every Pattern has exactly one default Variant: the happy flow** — the baseline success path with no complications. All other Variants are named departures from this baseline.

A transaction can invoke **multiple Variants simultaneously**. They are additive.

**How Variants are triggered:**
- Some Variants are triggered by item attributes (e.g. `prescriptionRequired = true` triggers `delivery-time-kyc`)
- Some are triggered by payment method (e.g. buyer selects COD → `cash-on-delivery` variant)
- Some are triggered by buyer choice at checkout (e.g. `self-pickup`)
- Some are triggered by events during the transaction (e.g. address change → `mid-transaction-changes`)

The seller does not manually "configure" Variants. Variants are derived from the combination of item attributes, offer terms, and buyer choices.

### Complete Variant Reference

| Variant | Applies to | What it modifies | Trigger |
|---|---|---|---|
| `cash-on-delivery` | Trade storefront, Hospitality delivery-order, Logistics parcel/hyperlocal | Payment collected at delivery by agent instead of prepaid | Buyer selects COD at checkout |
| `self-pickup` | Trade storefront/made-to-order, Hospitality delivery-order | Buyer collects from seller location instead of delivery | Buyer selects self-pickup at checkout |
| `sender-drop-off` | Logistics parcel | Sender brings parcel to logistics hub instead of courier pickup | Seller/buyer specifies drop-off mode |
| `redelivery-attempts` | Trade storefront, Logistics parcel/hyperlocal | Up to N reattempts if delivery fails first time | First delivery attempt fails |
| `return-to-sender-handoff` | Trade storefront/marketplace, Logistics parcel | Return shipment from buyer back to seller, with QC inspection | Buyer initiates return within return window |
| `delivery-time-kyc` | Trade storefront/subscription, Logistics parcel/hyperlocal, Services engagement-booking | Identity verification (OTP or KTP check) required at delivery before handover | `prescriptionRequired = true` OR `ageRestricted = true` on resource |
| `advance-ride-booking` | Mobility on-demand-ride | Journey booked in advance rather than real-time dispatch | Buyer specifies future pickup time |
| `mid-journey-stop-change` | Mobility on-demand-ride | Buyer changes destination during an active ride | Buyer requests during journey |
| `seat-upgrade` | Mobility scheduled-journey | Buyer upgrades seat class after initial booking | Buyer requests upgrade |
| `early-checkin` | Hospitality reservation | Check-in before standard check-in time | Buyer requests at booking |
| `late-checkout` | Hospitality reservation | Check-out after standard checkout time | Buyer requests at booking |
| `scope-change` | Hospitality event-commission, Services project-order/retainer/engagement | Modification to agreed scope after confirmation | Either party requests change |
| `referral-handoff` | Services engagement-booking | Patient/client referred to another practitioner during service | Practitioner triggers referral |
| `plan-change` | Finance service-activation, Services subscription/service-activation | Buyer switches service plan during active subscription | Buyer requests plan change |
| `joint-account` | Finance account-opening | Two or more account holders on one account | Buyer specifies joint account |
| `partial-disbursement` | Finance loan-application | Loan disbursed in tranches rather than full upfront | Loan terms specify milestone-based disbursement |
| `top-up-loan` | Finance loan-application | Additional credit extended on existing loan | Borrower requests top-up |
| `early-settlement` | Finance loan-application | Borrower repays loan before scheduled end | Borrower initiates prepayment |
| `split-payment` | Finance payment | Payment split across multiple methods or payers | Buyer selects split payment |
| `mid-term-endorsement` | Finance policy-issuance | Policy modified (coverage, beneficiary) during active period | Policyholder requests change |
| `business-credit` | Trade business-procurement | Payment on credit terms (NET-7/30/60) rather than upfront | B2B buyer qualifies for credit terms |
| `mid-transaction-changes` | All sectors — most patterns | Buyer-initiated modification after confirmation (item, quantity, address, time) | Buyer requests change after confirm |

---

## 4.3 Test Sets and Test Cases

**Test Set:** A named collection of Test Cases scoped to a specific Pattern + Variant combination. This is the unit of conformance certification. Before a Seller App or Buyer App goes live on ION, it must pass the required Test Sets for every Pattern + Variant combination it declares support for.

**Test Case:** An atomic executable scenario within a Test Set.

| Type | Description | Example |
|---|---|---|
| **Stateless** | Self-contained. Tests a single request-response pair or schema validation. No dependency on prior calls. | Validate that `prescriptionRequired = true` in resourceAttributes triggers correct CDS flagging |
| **Stateful** | Depends on prior state. Tests a multi-step sequence where output of one call feeds the next. | Full flow: `/discover` → `/select` → `/init` → `/confirm` → `/on_status[DELIVERED]` for `storefront / delivery-time-kyc` |

Test Sets are owned and maintained by the ION governance team under a dedicated validation framework.

---

# PART 5 — THE EXTENSION MODEL

## 5.1 Four Extension Zones

Every Attributes bag in ION has four zones. Understanding these zones is essential for any NP building on ION.

### Zone 1 — Beckn Defined
Fields explicitly in the Beckn 2.0 core schema (beckn.yaml). ION cannot change these. The `@context` and `@type` fields, and all base Beckn object structures, live here.

```
Governed by: Beckn Protocol Foundation
Can ION change: No
```

### Zone 2 — ION Mandated (GOVERNED)
Fields ION has defined and enforces. Three sub-zones:

| Sub-zone | What it contains | Example fields |
|---|---|---|
| **ION Core** | Always required regardless of sector | `quantity`, `resourceStructure`, `resourceTangibility`, `images`, `availability`, `countryOfOrigin` |
| **ION Sector** | Required for this sector's attribute pack | `pharmacy.*`, `food.*`, `electronics.*`, `providerCategory`, `kycStatus` |
| **ION Conditional** | Required when a condition is met | `prescriptionRequired` triggers `dataResidency` · `resourceStructure=VARIANT` triggers `parentResourceId` |

```
Governed by: ION Council + Sector Working Groups
Validated by: CDS at catalog/publish time
```

### Zone 3 — Participant Extensions
Fields a network participant declares under their own IRI namespace at onboarding. Not validated by ION Central. Size-capped at gateway.

```
Governed by: Each network participant
Validated by: Nobody — participant's own responsibility
Example: A marketplace adds loyalty tier, internal SKU mapping,
          seller performance score — fields specific to their platform
```

### Zone 4 — Experimental
Fields proposed by the ION Sector Working Group for piloting. Auto-expire after the pilot window. Promoted to Zone 2 (ION Mandated) if successful. Carry `x-ion-experimental: true` tag.

```
Governed by: ION Sector Working Group
Active window: Defined per pilot
Future examples: Carbon footprint per shipment,
                  accessibility feature flags,
                  geographic indication signals
```

---

## 5.2 ION's 8 Extension Endpoints

ION adds 8 endpoints to Beckn's 30. These are mandatory for every ION participant regardless of sector.

| Endpoint | Direction | What it does |
|---|---|---|
| `POST /raise` | NP → ION | Submit a formal dispute or issue ticket to ION governance |
| `POST /on_raise` | ION → NP | Returns ticket ID, SLA commitments |
| `POST /raise_status` | NP → ION | Poll for current status of an open ticket |
| `POST /on_raise_status` | ION → NP | Returns current ticket state and updates |
| `POST /raise_details` | NP → ION | Request full ticket thread with all messages and evidence |
| `POST /on_raise_details` | ION → NP | Returns complete audit record |
| `POST /reconcile` | BAP → BPP | Initiate financial settlement true-up after contract completion |
| `POST /on_reconcile` | BPP → BAP | Returns reconciliation status (AGREED / DISPUTED) |

**Raise vs Support/IGM:**
- `/support + IGM` = buyer→seller consumer complaint (through Beckn `/support` endpoint)
- `/raise` = network participant→ION platform-level escalation

**Reconciliation:** After delivery, the BAP (which collected payment from the consumer) settles the net amount to the BPP. The reconciliation payload declares: base order value, finder/commission fee, withholding for return window, SLA penalty deductions, tax withholdings (PPh22, PPh23, PPN), and net payable to the seller.

---

# PART 6 — JOINING ION

## 6.1 The Classification Decision Tree

Any business joining ION follows this path:

```
Step 1: What does your business do?
         ↓
         Apply the sector principle test:
         "What does the buyer end up with?"
         → Select your Sector

Step 2: What specific type of business are you
         within that sector?
         → Select your Segment

Step 3: What is your Role?
         Seller / Seller App / Buyer App / TSP
         → Your Profile = Role + Sector

Step 4: Verify your regulatory credentials
         NIB + NPWP (all)
         + sector-specific licences as applicable
         (PBF for pharmacy, OJK licence for finance, etc.)
         → Profile authorised

Step 5: Publish your catalogue
         For each item, select the CRC
         → CRC determines which attributes are mandatory
         → Fill in mandatory fields honestly
         → CDS validates and indexes

Step 6: Declare your patterns and variants
         Which transaction flows do you support?
         → storefront? subscription? marketplace-listed?
         → Some variants are automatic (triggered by attributes)

Step 7: Run the Test Sets
         For every Pattern + Variant combination declared,
         run the ION conformance test suite
         → Pass = go-live approved
```

## 6.2 Complete Example — Multiple Business Types

Here is how five different businesses would classify themselves on ION:

---

**Business A: A pharmacy chain with 50 stores**

```
Sector:   Trade
Segment:  TRD-04 — Pharmacy & Health Retail
Role:     Seller (each store) + Seller App (the chain's own platform)
Profile:  seller / trade (per store) · SA / trade (the platform)
KBLI:     47730, 47740
Licence:  PBF pharmacy licence per location

Items they list:
  OTC medicines      → TRC-health-beauty · storefront · happy-flow
  Prescription meds  → TRC-health-beauty · storefront · delivery-time-kyc
  Chronic meds       → TRC-health-beauty · subscription · delivery-time-kyc
  Mineral water      → TRC-food-bev · storefront · happy-flow
  Protein powder     → TRC-food-bev · storefront · happy-flow
```

---

**Business B: A ride-hailing platform**

```
Sector:   Mobility
Segment:  MOB-01 — Ride-Hailing & On-Demand Transport
Role:     Buyer App (for passengers) + Seller App (for drivers)
Profile:  BA / mobility · SA / mobility
KBLI:     49230
Licence:  Kemenhub DISHUB transport permit

CRC:      MOC-ride
Pattern:  on-demand-ride
Variants: advance-ride-booking (when passenger books ahead)
          mid-journey-stop-change (when destination changes)
```

---

**Business C: A B2B industrial supplier**

```
Sector:   Trade
Segment:  TRD-06 — Wholesale & Supply Trade
Role:     Seller + Seller App
Profile:  seller / trade · SA / trade
KBLI:     46610, 46900
Licence:  SIUP-B (wholesale trading licence)

Items:
  Industrial equipment  → TRC-industrial · business-procurement · business-credit
  Raw materials         → TRC-industrial · reverse-auction · mid-transaction-changes
  Bulk food ingredients → TRC-food-bev · business-procurement · business-credit

Payment terms: NET-30 or NET-60 credit terms via business-credit variant
```

---

**Business D: A multi-sector conglomerate (Gojek equivalent)**

```
Profile 1: BA / mobility       (ride-hailing app)   KBLI 49230
Profile 2: SA / hospitality    (food platform)       KBLI 56400
Profile 3: SA / finance        (e-wallet)            KBLI 64950
Profile 4: SA / logistics      (delivery arm)        KBLI 53300

Each profile is separately authorised.
Each has its own KYC.
Each maps to its own KBLI codes.
A change in one profile does not affect others.
```

---

**Business E: A telemedicine and health products platform**

```
This business straddles two sectors:

Health products sold:
  Sector:   Trade (buyer ends up owning the product)
  Segment:  TRD-04 — Pharmacy & Health Retail
  Profile:  SA / trade
  CRCs:     TRC-health-beauty (medicines), TRC-food-bev (supplements)

Doctor consultations:
  Sector:   Services (human expertise delivered)
  Segment:  SVC-05 — Healthcare & Wellness Services
  Profile:  SA / services
  CRC:      SVRC-healthcare
  Pattern:  engagement-booking

Two profiles. Two sectors. Same platform.
Products are Trade. Consultations are Services.
The classification test ("what does the buyer end up with?")
resolves the distinction cleanly.
```

---

# PART 7 — QUICK REFERENCE TABLES

## 7.1 All Segments — Complete List

| Code | Sector | Segment Name |
|---|---|---|
| TRD-01 | Trade | Grocery, FMCG & Quick Commerce |
| TRD-02 | Trade | E-commerce & Digital Marketplaces |
| TRD-03 | Trade | Specialty Retail |
| TRD-04 | Trade | Pharmacy & Health Retail |
| TRD-05 | Trade | Automotive Sales & Parts |
| TRD-06 | Trade | Wholesale & Supply Trade |
| TRD-07 | Trade | Energy & Commodity Trade |
| HSP-01 | Hospitality | Hotels & Accommodation |
| HSP-02 | Hospitality | Short-Stay & Alternative Accommodation |
| HSP-03 | Hospitality | Travel Platforms & OTAs |
| HSP-04 | Hospitality | F&B — Restaurants & Dining |
| HSP-05 | Hospitality | Food Delivery & Online Ordering |
| HSP-06 | Hospitality | Events, Catering & MICE |
| HSP-07 | Hospitality | Recreation, Attractions & Wellness |
| LOG-01 | Logistics | Last-Mile & Courier Delivery |
| LOG-02 | Logistics | Freight Transport |
| LOG-03 | Logistics | Warehousing & Fulfilment |
| LOG-04 | Logistics | Sea & Air Freight |
| LOG-05 | Logistics | Freight Support & Forwarding |
| MOB-01 | Mobility | Ride-Hailing & On-Demand Transport |
| MOB-02 | Mobility | Mass Transit & Scheduled Transport |
| MOB-03 | Mobility | Aviation — Passenger |
| MOB-04 | Mobility | Future Mobility |
| FIN-01 | Finance | Banking |
| FIN-02 | Finance | Lending & Consumer Credit |
| FIN-03 | Finance | Payments & E-money |
| FIN-04 | Finance | Insurance & Risk |
| FIN-05 | Finance | Capital Markets & Wealth |
| FIN-06 | Finance | Digital Assets & Carbon Finance |
| FIN-07 | Finance | Financial Infrastructure & Support |
| SVC-01 | Services | Technology & Digital Services |
| SVC-02 | Services | Telecom & Connectivity |
| SVC-03 | Services | Professional & Advisory Services |
| SVC-04 | Services | Marketing, Media & Creative |
| SVC-05 | Services | Healthcare & Wellness Services |
| SVC-06 | Services | Real Estate & Construction Services |
| SVC-07 | Services | Automotive & Vehicle Services |
| SVC-08 | Services | Facilities, Support & Personal Services |

**Total: 38 segments across 6 sectors**

---

## 7.2 All CRCs — Complete List

| Code | Sector | ION Name | Status |
|---|---|---|---|
| TRC-fashion | Trade | Fashion & Accessories | ACTIVE |
| TRC-electronics | Trade | Electronics & Gadgets | ACTIVE |
| TRC-food-bev | Trade | Food, Beverages & Tobacco | ACTIVE |
| TRC-health-beauty | Trade | Health & Beauty | ACTIVE |
| TRC-home-garden | Trade | Home & Garden | ACTIVE |
| TRC-furniture | Trade | Furniture | ACTIVE |
| TRC-automotive | Trade | Vehicles & Parts | ACTIVE |
| TRC-sports | Trade | Sporting Goods | ACTIVE |
| TRC-toys-baby | Trade | Toys, Games & Baby | ACTIVE |
| TRC-media | Trade | Books & Media | ACTIVE |
| TRC-digital | Trade | Digital Goods | ACTIVE |
| TRC-industrial | Trade | Industrial & Wholesale | ACTIVE |
| TRC-arts | Trade | Arts, Crafts & Collectibles | ACTIVE |
| TRC-religious | Trade | Religious & Ceremonial | ACTIVE |
| TRC-luggage | Trade | Luggage & Bags | ACTIVE |
| TRC-pets | Trade | Animals & Pet Supplies | INACTIVE |
| TRC-hardware | Trade | Hardware & Tools | INACTIVE |
| TRC-office | Trade | Office Supplies | INACTIVE |
| TRC-mature | Trade | Mature | GOVERNED |
| HSC-accommodation | Hospitality | Accommodation | ACTIVE |
| HSC-restaurant | Hospitality | Restaurant & Dining | ACTIVE |
| HSC-fnb-delivery | Hospitality | Food & Beverage Delivery | ACTIVE |
| HSC-events | Hospitality | Events & Experiences | ACTIVE |
| HSC-wellness | Hospitality | Recreation & Wellness | ACTIVE |
| LGC-lastmile | Logistics | Last-Mile Delivery | ACTIVE |
| LGC-freight | Logistics | Freight Transport | ACTIVE |
| LGC-international | Logistics | Sea & Air Freight | ACTIVE |
| LGC-warehouse | Logistics | Warehousing | ACTIVE |
| LGC-forwarding | Logistics | Freight Forwarding | ACTIVE |
| MOC-ride | Mobility | Ride-Hailing | ACTIVE |
| MOC-scheduled | Mobility | Scheduled Transport | ACTIVE |
| MOC-rental | Mobility | Vehicle Rental | ACTIVE |
| MOC-charter | Mobility | Charter | INACTIVE |
| FNC-lending | Finance | Lending & Credit | ACTIVE |
| FNC-insurance | Finance | Insurance | ACTIVE |
| FNC-payments | Finance | Payments & E-money | ACTIVE |
| FNC-investment | Finance | Investment | ACTIVE |
| FNC-banking | Finance | Banking Services | INACTIVE |
| SVRC-tech | Services | Technology & Digital Services | ACTIVE |
| SVRC-professional | Services | Professional & Advisory | ACTIVE |
| SVRC-healthcare | Services | Healthcare & Wellness Services | ACTIVE |
| SVRC-telecom | Services | Telecom & Connectivity | ACTIVE |
| SVRC-creative | Services | Creative & Marketing | ACTIVE |
| SVRC-home | Services | Home & Facility Services | ACTIVE |
| SVRC-education | Services | Education & Training | ACTIVE |
| SVRC-auto | Services | Automotive Services | INACTIVE |

**Total: 46 CRCs across 6 sectors (33 ACTIVE · 8 INACTIVE · 1 GOVERNED)**

---

## 7.3 Classification Edge Cases

Common questions that arise when classifying a business or item:

| Situation | Classification | Reasoning |
|---|---|---|
| Medicine sold in a pharmacy | Trade → TRD-04 → TRC-health-beauty | Buyer ends up owning the medicine |
| Doctor consultation in a clinic | Services → SVC-05 → SVRC-healthcare | Buyer receives expertise, not a product |
| Hospital bed booking | Hospitality → HSP-01 → HSC-accommodation | Time-bounded reservation of capacity |
| Health insurance | Finance → FIN-04 → FNC-insurance | Financial instrument |
| Protein powder sold in a pharmacy | Trade → TRD-04 → TRC-food-bev | BPOM classifies as food (Pangan Olahan) — item's regulatory nature, not where it's sold, determines CRC |
| Restaurant delivery via app | Hospitality → HSP-05 → HSC-fnb-delivery | Prepared food (made after order) |
| Packaged noodles in a grocery | Trade → TRD-01 → TRC-food-bev | Physical good buyer owns |
| Same product sold B2C and B2B | Same CRC for both | Different Segment + Pattern + Offer. The item doesn't change — the transaction terms do. |
| Wheelchair (medical device) | Trade → TRD-04 → TRC-health-beauty | Physical good, buyer owns it |
| Car repair service | Services → SVC-07 → SVRC-auto | Human labour |
| Car parts | Trade → TRD-05 → TRC-automotive | Physical good buyer owns |
| E-book | Trade → TRD-02 → TRC-digital | Digital entitlement |
| Physical book | Trade → TRD-03 → TRC-media | Physical good buyer owns |

---

*This document is the complete plain-English reference for ION's structural concepts. It is designed to be inserted into a project context and used as the background for generating specifications, onboarding materials, technical documentation, or compliance guides.*

*Companion documents:*
- *ION Entity Hierarchy Model v0.4 — formal governance document with decisions log*
- *ION Resource Categories — CRC reference with GPC crosswalk*
- *Bu Sari Wizard — non-technical seller onboarding walkthrough*
- *Bu Sari Complete Journey — full API lifecycle with JSON payloads*
- *Beckn Base Spec Excel — all 30 Beckn core endpoints*
- *ION Extensions Excel — all 8 ION extension endpoints*
- *Trade Sector Fields Excel — all Trade attribute schemas*
