# Sample: `POST /catalog/publish` — ION retail + Samsung OEM + Muthu reseller

This folder contains a **worked example** of a Beckn Protocol **v2.0.0 LTS** catalog publish body (`context` + `message`) for the ION network. The JSON file is **`POST-catalog-publish-request-body.sample.json`**.

The example layers three **namespaces** (IRIs are illustrative; hostnames are syntactically valid `https` URIs for documentation only):

| Layer | Namespace base (sample) | Role |
|--------|-------------------------|------|
| ION network retail | `https://schema.ion.id/ns/2026/vocab#` | Cross-participant **trade** semantics: identifiers and categories (`retailSku`, `tradeBrand`, `tradeItemCategory`, …). **Does not** model whether a handset is new, used, refurbished, or damaged — that is out of scope for ION. |
| Vendor (Samsung OEM) | `https://schema.samsung.com/ion/retail/v1/vocab#` | **Product / handset specification** only (`knoxMajorVersion`, `galaxySeries`, `modelNumber`, …). **Does not** model inventory condition, “used phone,” grading, or resale — Samsung’s schema describes the **device**, not the **seller’s stock condition**. **Hosted by Samsung** (not ION). |
| Reseller (Muthu) | `https://reseller.muthu.example/ns/7a3f9c2d/vocab#` | **Only this layer** introduces **inventory and condition** semantics for listings: new vs used, cosmetic / functional grade, optional **schema.org** `itemCondition`, `batteryHealthPercent`, etc. This is **Muthu’s extension** as a BPP/reseller, not part of ION or Samsung core vocabularies. |

**Samsung OEM layer:** The smartphone extension context and vocabulary IRIs use **`https://schema.samsung.com/ion/retail/v1/...`** so that **Samsung** publishes and hosts the authoritative JSON-LD (ION only references those URLs in `schemaContext` and merged `@context`; it does not host Samsung’s OEM terms). The path segment `ion/retail` is illustrative of an ION-aligned retail profile; Samsung may choose a different path under their domain in production.

**Why `ResellerPhoneListing` appears on every resource row:** The JSON-LD `@type` **`ResellerPhoneListing`** is defined in **Muthu’s** context. It names “a phone listing offered by Muthu,” including **new** stock (`stockKind: "NEW"`) and **used** stock (`"USED"`). ION and Samsung types alone are insufficient to express **outlet-specific inventory**; the `resourceAttributes` object therefore combines ION + Samsung + **Muthu** terms, with condition-related keys **only** from Muthu (and schema.org via Muthu’s mapping).

Companion JSON-LD context fragments live under **`jsonld/`** (mirrors of what would be hosted at the `https://…` URLs).

**Strict protection profile (applied):** each owner context sets `"@protected": true` (ION, Samsung, Muthu). Downstream layers can add new terms, but cannot redefine protected terms from earlier layers.

**Typing reminder (Beckn v2):** in this sample, `Attributes.@context` is always a **single string URI** (as typed in `beckn.yaml`), while `Context.schemaContext` is the **array of context URIs** for the overall request envelope.

**Mode A administrative pattern (applied):** each listing points `resourceAttributes.@context` to **Muthu’s canonical context URL**. That Muthu context document imports Beckn core + ION + Samsung contexts and then adds Muthu terms. This keeps ownership federated (no partner files copied to ION) while retaining a single-string `@context` in the payload.

---

## 1. Transport: endpoint and envelope

- **HTTP:** `POST /catalog/publish` on the CDS (or publishing endpoint your network defines).  
  **Source:** [beckn/protocol-specifications-v2 — `api/v2.0.0/beckn.yaml`](https://github.com/beckn/protocol-specifications-v2/blob/main/api/v2.0.0/beckn.yaml) — path `/catalog/publish`, `operationId: catalogPublish`.
- **Request body:** top-level object with required keys **`context`** and **`message`**.  
  **Source:** same file, request body schema under `/catalog/publish` (`required: context, message`).
- **Authentication:** production traffic expects the **`Authorization`** header (Beckn HTTP Signature). This sample is body-only; see `#/components/parameters/AuthorizationHeader` and schema `Signature` in the same OpenAPI.  
  **Source:** [`beckn.yaml` components](https://github.com/beckn/protocol-specifications-v2/blob/main/api/v2.0.0/beckn.yaml).

---

## 2. `message.catalogs` and `Catalog`

- **`CatalogPublishAction`** requires **`catalogs`** (array, `minItems: 1`).  
  **Source:** `CatalogPublishAction` in [`beckn.yaml`](https://github.com/beckn/protocol-specifications-v2/blob/main/api/v2.0.0/beckn.yaml).
- Each **`Catalog`** must include **`id`**, **`descriptor`**, **`provider`**, and **either** `resources` **or** `offers` (`anyOf`). This sample includes **both** `resources` and `offers`.  
  **Source:** `Catalog` schema in the same file (`required`, `anyOf`).
- **`bppId` / `bppUri` on `Catalog`:** still present in v2.0.0 transport schema; some implementers also rely on **`context.bppId` / `context.bppUri`** as the routing source of truth. For a discussion of separation of concerns, see e.g. [protocol-specifications-v2 issue #74](https://github.com/beckn/protocol-specifications-v2/issues/74).  
  **Source:** `Catalog` properties in [`beckn.yaml`](https://github.com/beckn/protocol-specifications-v2/blob/main/api/v2.0.0/beckn.yaml).

---

## 3. `context` — protocol metadata and `schemaContext`

- **`context.version`:** must be **`2.0.0`** for this LTS line when validating against the published API contract.  
  **Source:** `Context` schema, property `version` with `const: 2.0.0` in [`beckn.yaml`](https://github.com/beckn/protocol-specifications-v2/blob/main/api/v2.0.0/beckn.yaml).
- **`context.action`:** for this call, use **`catalog/publish`** (alternate token `catalog_publish` is allowed by enum in the same spec).  
  **Source:** `/catalog/publish` request body `context.action` enum in [`beckn.yaml`](https://github.com/beckn/protocol-specifications-v2/blob/main/api/v2.0.0/beckn.yaml).
- **`context.schemaContext`:** optional array of **JSON-LD context URIs** relevant to the payload — “schemas relevant to the request.” The sample lists Beckn core + ION + Samsung + Muthu + pricing contexts.  
  **Source:** `Context.schemaContext` in [`beckn.yaml`](https://github.com/beckn/protocol-specifications-v2/blob/main/api/v2.0.0/beckn.yaml).
- **`transactionId` / `messageId`:** UUID-shaped strings in the sample; `messageId` identifies the request/callback cycle per field descriptions in `Context`.  
  **Source:** `Context` in [`beckn.yaml`](https://github.com/beckn/protocol-specifications-v2/blob/main/api/v2.0.0/beckn.yaml).
- **`networkId`:** format described on `Context.networkId` (registry / namespace on DeDi). The value in the sample is **illustrative**.  
  **Source:** `Context.networkId` description in [`beckn.yaml`](https://github.com/beckn/protocol-specifications-v2/blob/main/api/v2.0.0/beckn.yaml).

---

## 4. Domain extension: `Resource.resourceAttributes` → `Attributes`

- **`Resource`** is intentionally minimal; **domain semantics** go in **`resourceAttributes`**, which references the **`Attributes`** schema.  
  **Source:** `Resource` description and `resourceAttributes` → `#/components/schemas/Attributes` in [`beckn.yaml`](https://github.com/beckn/protocol-specifications-v2/blob/main/api/v2.0.0/beckn.yaml).
- **`Attributes`** requires **`@context`** and **`@type`**; **additional properties are allowed** and are interpreted per JSON-LD.  
  **Source:** `Attributes` in [`beckn.yaml`](https://github.com/beckn/protocol-specifications-v2/blob/main/api/v2.0.0/beckn.yaml).
- **Single `@context` URI (this sample):** The OpenAPI types `@context` as a **string**. The sample uses Muthu’s canonical context URL — **`https://reseller.muthu.example/ns/7a3f9c2d/context/muthu-reseller-phones-v1.jsonld`** — and that context document imports Beckn core + ION + Samsung before defining Muthu terms. This keeps the **transport JSON** aligned with the string form while still expressing **JSON-LD 1.1 context composition** when the context is dereferenced.  
  **Sources:** `Attributes` in [`beckn.yaml`](https://github.com/beckn/protocol-specifications-v2/blob/main/api/v2.0.0/beckn.yaml); JSON-LD 1.1 context processing — [W3C JSON-LD 1.1](https://www.w3.org/TR/json-ld11/).  
  **Note:** If you prefer each `Attributes` object to carry **`@context` as a JSON array** of URIs directly, that is valid **JSON-LD**, but it may require an **ION-extended JSON Schema profile** because the stock OpenAPI component types `@context` as `string` only.
- **Do not redefine Beckn core terms:** the Beckn root context is **`@protected`**; domain/network contexts **must not** remap core vocabulary.  
  **Source:** [beckn/core_schema — `docs/3_JSON_LD_Context_and_Vocabulary.md`](https://github.com/beckn/core_schema/blob/main/docs/3_JSON_LD_Context_and_Vocabulary.md) (requirements **JLD-02**, **JLD-03**).

---

## 5. Layered semantics in the three `Resource` rows

| Resource id (sample) | Story | ION + Samsung (same across rows) | Muthu-only (inventory / condition) |
|----------------------|-------|----------------------------------|-----------------------------------|
| `…-new-sealed-muthu-001` | New, sealed retail unit | `tradeBrand`, `retailSku`, `tradeItemCategory`, `handsetMarketingName`, `galaxySeries`, `modelNumber`, `oneUiVersion`, `knoxMajorVersion` | `stockKind`, `gradeLabel`, `itemCondition`, `batteryHealthPercent` |
| `…-used-mint-muthu-042` | Open-box / used, high grade | (same pattern) | `stockKind: "USED"`, `gradeLabel: "MINT"`, … |
| `…-damaged-muthu-099` | Damaged / as-is | (same pattern) | `gradeLabel: "DAMAGED"`, `itemCondition: DamagedCondition`, … |

- **Condition columns (`stockKind`, `gradeLabel`, `itemCondition`, `batteryHealthPercent`)** are defined only under **`muthu:`** in **`context-muthu-reseller-phones-v1.jsonld`**. Neither **`ion:`** nor **`sam:`** contexts declare “used phone” or cosmetic grade — by design.
- **`itemCondition`** is mapped in Muthu’s context (via `schema:itemCondition`) with `@type: @id` so values can be **schema.org OfferCondition** IRIs.  
  **Reference:** [schema.org OfferCondition](https://schema.org/OfferCondition) (hierarchy includes `NewCondition`, `UsedCondition`, `DamagedCondition`).

---

## 6. `Offer` and `Consideration` (pricing)

- **`Offer`** requires **`id`**; **`considerations`** is an array of **`Consideration`**.  
  **Source:** `Offer` and `Consideration` in [`beckn.yaml`](https://github.com/beckn/protocol-specifications-v2/blob/main/api/v2.0.0/beckn.yaml).
- **`Consideration`** requires **`id`** and **`status`** (`Descriptor`). Monetary detail is placed in **`considerationAttributes`** → **`Attributes`** (per field description pointing to price patterns).  
  **Source:** `Consideration` in [`beckn.yaml`](https://github.com/beckn/protocol-specifications-v2/blob/main/api/v2.0.0/beckn.yaml).
- The sample uses a small ION vocabulary **`MonetaryConsiderationSpec`** with `priceAmount` / `priceCurrency` in **`context-ion-monetary-consideration-v1.jsonld`**.

---

## 7. Local files ↔ canonical URLs

| Canonical URL (sample) | Local mirror file |
|------------------------|-------------------|
| `https://schema.ion.id/ns/2026/context/ion-retail-trade-item-v1.jsonld` | `jsonld/context-ion-retail-trade-item-v1.jsonld` |
| `https://schema.samsung.com/ion/retail/v1/context/smartphone-v1.jsonld` | `jsonld/context-samsung-smartphone-v1.jsonld` (local mirror; **canonical URL is Samsung-hosted**) |
| `https://reseller.muthu.example/ns/7a3f9c2d/context/muthu-reseller-phones-v1.jsonld` | `jsonld/context-muthu-reseller-phones-v1.jsonld` |
| `https://schema.ion.id/ns/2026/context/ion-monetary-consideration-v1.jsonld` | `jsonld/context-ion-monetary-consideration-v1.jsonld` |
| `https://schema.beckn.io/core/v2.0/context.jsonld` | Hosted by Beckn (not vendored here) |

---

## 8. ION gateway validation (policy, not Beckn core)

Split enforcement by **who owns the vocabulary**:

- **ION overlay:** e.g. mandatory `retailSku`, `tradeBrand`, types for ION trade fields — **not** “used vs new” (that is not an ION vocabulary concern).
- **Samsung (vendor) overlay:** if the network requires OEM fields when `schemaContext` includes Samsung’s context, validate **`sam:`** product attributes — still **not** inventory condition.
- **Reseller (e.g. Muthu) overlay:** optional per participant; if the BPP publishes Muthu’s context, validate **`muthu:`** condition fields under whatever policy Muthu and the network agree (e.g. `stockKind` enum, `gradeLabel` allowed values).

All of this is **network policy** on top of Beckn, expressed with JSON Schema **`allOf`** — analogous to the “constitution / laws” split in internal ION design docs.  
**Beckn reference for extension without forking core types:** domain packs extend Tier-2 schemas via **`allOf`** — [beckn/core_schema — `docs/2_Schema_Structure.md`](https://github.com/beckn/core_schema/blob/main/docs/2_Schema_Structure.md) and [`docs/1_Introduction.md`](https://github.com/beckn/core_schema/blob/main/docs/1_Introduction.md).

---

## 9. Files in this folder

| File | Purpose |
|------|---------|
| `POST-catalog-publish-request-body.sample.json` | Full **`context` + `message`** body for `/catalog/publish`. |
| `README-catalog-publish-sample-explained.md` | This commentary and citations. |
| `jsonld/context-*.jsonld` | Publishable JSON-LD context documents matching the URIs used in the sample. |
