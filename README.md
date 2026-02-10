# Shipping & Tracking Normalization API — Examples & Documentation

<p align="center">
  <a href="https://rapidapi.com/precisionsolutionstech/api/shipping-tracking-normalization-api">
    <img src="https://img.shields.io/badge/Try_on_RapidAPI-Shipping_%26_Tracking_Normalization-00A1F1?style=for-the-badge&logo=rapidapi" alt="Try Shipping & Tracking Normalization API on RapidAPI" />
  </a>
</p>

**Unify UPS, FedEx, USPS, DHL, and every other carrier into one canonical tracking schema.** Stop maintaining per-carrier parsers, status maps, and timezone hacks. One API call turns tracking numbers and carrier payloads into a single shipment model—same status taxonomy, chronological event timeline, provenance, and confidence. Same input → same output every time. You cannot scale multi-carrier tracking without it.

**[Try the API on RapidAPI →](https://rapidapi.com/precisionsolutionstech/api/shipping-tracking-normalization-api)**

---

## Table of Contents

- [Why You Need This API](#why-you-need-this-api)
- [What is the Shipping & Tracking Normalization API?](#what-is-the-shipping--tracking-normalization-api)
- [Quick Start](#quick-start)
- [Real-World Examples](#real-world-examples)
- [API Reference](#api-reference)
- [Canonical Status & Schema](#canonical-status--schema)
- [Related APIs](#related-apis)
- [FAQ](#faq)

---

## Why You Need This API

**Multi-carrier tracking is a mess.** Every carrier exposes different APIs, different status strings ("Out for Delivery" vs "On FedEx vehicle for delivery" vs "With delivery courier"), different event shapes, and different timestamps. Building one tracking page, one support view, or one analytics pipeline means:

- Writing and maintaining **separate integrations** for UPS, FedEx, USPS, DHL, and more
- **Mapping dozens of status strings** to your internal states—and breaking when carriers change wording
- **Handling unreliable carrier detection** and ambiguous tracking numbers
- **Normalizing timestamps and locations** yourself—timezones, missing data, silent failures
- **Fragile logic** that explodes when a new carrier or webhook format appears

**The cost of doing it yourself:** High integration cost, brittle code, poor observability, and inconsistent analytics. Customer support wastes time deciphering carrier jargon. Notifications fire on the wrong status because your mapping was wrong. BI dashboards can't compare delivery performance across carriers because every source looks different.

**With this API:** One request → one canonical shipment object → zero carrier-specific logic downstream. You send tracking numbers (and optionally pre-fetched payloads); you get back a **fixed status taxonomy**, **chronological event timeline**, **provenance**, and **confidence**. Marketplaces, fulfillment systems, support tools, and analytics pipelines all speak one language. You can't manage multi-carrier tracking at scale without a single source of truth—this API is that source of truth.

---

## What is the Shipping & Tracking Normalization API?

The **Shipping & Tracking Normalization API** is a REST API that converts carrier-specific tracking data (or tracking numbers only) into one **canonical shipment schema**. It maps UPS, FedEx, USPS, DHL, and custom payloads to the same structure: identifiers, carrier, currentStatus, events (chronological timeline), delivery metadata, provenance, and confidence. Status strings are normalized to a **fixed taxonomy** (e.g. `in_transit`, `out_for_delivery`, `delivered`, `exception`); original carrier text is always preserved. Output is **deterministic** and **stateless**—no data stored, 25MB max per request.

### Key Features

| Feature | Description |
|---------|-------------|
| **Single schema** | Every shipment has the same shape regardless of carrier |
| **Canonical status** | Fixed taxonomy: pending, label_created, accepted, in_transit, out_for_delivery, delivered, exception, failed_attempt, returned, cancelled, customs_cleared, unknown |
| **Event timeline** | Chronological events with canonicalStatus, originalCarrierStatus, timestampIso, location, eventType |
| **Provenance & confidence** | Every shipment includes data source type, parsing strategy, and 0–1 confidence score |
| **Carrier detection** | Infer carrier from tracking number when not declared; mismatch → warning and lower confidence |
| **Partial success** | One bad item does not fail the request; item-scoped errors and metadata counts |
| **Stateless** | No data stored or logged; no carrier API keys required |

### Use Cases

- **Marketplace order tracking** — One tracking page for orders fulfilled by multiple sellers and carriers (e.g. Amazon, eBay, Etsy)
- **Customer support** — Agents see one timeline and status language instead of UPS/FedEx/USPS jargon
- **Fulfillment & OMS** — Ingest tracking from all carriers into one schema for inventory and exception handling
- **Notifications** — Trigger "Out for delivery" or "Delivered" alerts using one status model across all carriers
- **BI & analytics** — Analyze delivery performance, on-time rate, and exceptions across carriers with one dataset
- **Webhooks** — Normalize carrier webhook payloads on receipt so downstream systems never see raw carrier formats

### Also Searchable As

Shipping tracking normalizer API • UPS FedEx USPS DHL unifier • tracking data canonical schema • multi-carrier tracking API • parcel tracking normalization • shipment status normalization API • delivery tracking combiner • tracking event timeline API • unified tracking API • carrier-agnostic tracking • logistics tracking normalization • e-commerce tracking API

---

## Quick Start

**Endpoint:** `POST /normalize`  
**Try it:** [RapidAPI — Shipping & Tracking Normalization API](https://rapidapi.com/precisionsolutionstech/api/shipping-tracking-normalization-api)

### cURL

```bash
curl -X POST "https://shipping-tracking-normalization-api.p.rapidapi.com/normalize" \
  -H "Content-Type: application/json" \
  -H "x-rapidapi-key: YOUR_RAPIDAPI_KEY" \
  -H "x-rapidapi-host: shipping-tracking-normalization-api.p.rapidapi.com" \
  -d '{
    "inputs": [
      { "trackingNumber": "1Z999AA10123456784", "carrier": "ups" },
      { "trackingNumber": "9405511899223191234567", "carrier": "usps" }
    ]
  }'
```

### JavaScript / Node.js

```javascript
const response = await fetch('https://shipping-tracking-normalization-api.p.rapidapi.com/normalize', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'x-rapidapi-key': 'YOUR_RAPIDAPI_KEY',
    'x-rapidapi-host': 'shipping-tracking-normalization-api.p.rapidapi.com'
  },
  body: JSON.stringify({
    inputs: [
      { trackingNumber: '1Z999AA10123456784', carrier: 'ups' },
      { trackingNumber: '9405511899223191234567', carrier: 'usps' }
    ]
  })
});
const { shipments, results, errors, metadata } = await response.json();
console.log(shipments); // Same schema for every carrier
```

### Python

```python
import requests

url = "https://shipping-tracking-normalization-api.p.rapidapi.com/normalize"
headers = {
    "Content-Type": "application/json",
    "x-rapidapi-key": "YOUR_RAPIDAPI_KEY",
    "x-rapidapi-host": "shipping-tracking-normalization-api.p.rapidapi.com"
}
payload = {
    "inputs": [
        {"trackingNumber": "1Z999AA10123456784", "carrier": "ups"},
        {"trackingNumber": "9405511899223191234567", "carrier": "usps"}
    ]
}
response = requests.post(url, json=payload, headers=headers)
data = response.json()
print(data["shipments"])  # One schema for all
print(data["metadata"])   # totalInputItems, normalizedCount, errorCount
```

### With Pre-Fetched Carrier Payload (Full Normalization)

Send the raw JSON you got from a carrier API to get a full event timeline and status.

```bash
curl -X POST "https://shipping-tracking-normalization-api.p.rapidapi.com/normalize" \
  -H "Content-Type: application/json" \
  -H "x-rapidapi-key: YOUR_RAPIDAPI_KEY" \
  -H "x-rapidapi-host: shipping-tracking-normalization-api.p.rapidapi.com" \
  -d '{
    "inputs": [{
      "trackingNumber": "1Z999AA10123456784",
      "carrier": "ups",
      "payload": {
        "shipment": {
          "package": {
            "activity": [
              { "date": "2024-01-14", "time": "08:00", "description": "Picked up", "location": { "address": { "city": "Chicago" } } },
              { "date": "2024-01-15", "time": "10:00", "description": "In transit", "location": { "address": { "city": "Louisville" } } }
            ]
          }
        }
      }
    }]
  }'
```

---

## Real-World Examples

### Example 1: Marketplace Tracking Page (Multiple Sellers, Multiple Carriers)

You run a marketplace. Orders are fulfilled by different sellers using UPS, FedEx, USPS, and DHL. You need one tracking UI that works for every order.

```javascript
const API_URL = 'https://shipping-tracking-normalization-api.p.rapidapi.com/normalize';
const RAPIDAPI_HEADERS = {
  'Content-Type': 'application/json',
  'x-rapidapi-key': process.env.RAPIDAPI_KEY,
  'x-rapidapi-host': 'shipping-tracking-normalization-api.p.rapidapi.com'
};

async function getUnifiedTracking(orders) {
  const inputs = orders.map(o => ({
    trackingNumber: o.trackingNumber,
    carrier: o.carrier || undefined,
    payload: o.carrierPayload || undefined  // if you already fetched from carrier API
  }));
  const res = await fetch(API_URL, {
    method: 'POST',
    headers: RAPIDAPI_HEADERS,
    body: JSON.stringify({ inputs })
  });
  const { shipments, errors } = await res.json();
  return { shipments, errors };  // One schema for your UI—no per-carrier rendering
}
```

### Example 2: Customer Support — One Timeline, One Language

Support agents should see "In transit" and "Out for delivery" instead of carrier-specific strings. Normalize on load and display the canonical status and events.

```javascript
async function getTrackingForSupport(trackingNumber, carrier) {
  const res = await fetch('https://shipping-tracking-normalization-api.p.rapidapi.com/normalize', {
    method: 'POST',
    headers: RAPIDAPI_HEADERS,
    body: JSON.stringify({
      inputs: [{ trackingNumber, carrier, payload: await fetchFromCarrierApi(trackingNumber, carrier) }]
    })
  });
  const { shipments } = await res.json();
  const s = shipments[0];
  return {
    status: s.currentStatus,           // e.g. "out_for_delivery"
    events: s.events,                  // Chronological; same shape for every carrier
    estimatedDelivery: s.delivery?.estimatedDeliveryDate,
    confidence: s.confidence.confidence
  };
}
```

### Example 3: Webhook → Notifications (No Carrier-Specific Logic)

When a carrier webhook fires, normalize the payload once and drive all notifications from the canonical status.

```javascript
async function onCarrierWebhook(webhookBody) {
  const res = await fetch('https://shipping-tracking-normalization-api.p.rapidapi.com/normalize', {
    method: 'POST',
    headers: RAPIDAPI_HEADERS,
    body: JSON.stringify({
      inputs: [{
        trackingNumber: webhookBody.tracking_number,
        carrier: webhookBody.carrier,
        payload: webhookBody
      }]
    })
  });
  const { shipments } = await res.json();
  const s = shipments[0];
  if (s.currentStatus === 'delivered') await sendDeliveryEmail(s);
  else if (s.currentStatus === 'out_for_delivery') await sendOutForDeliverySms(s);
  else if (s.currentStatus === 'exception') await alertSupport(s);
}
```

### Example 4: Analytics — One Schema for All Carriers

Load normalized shipments into your warehouse or BI tool. Same dimensions and measures for every carrier.

```python
import requests

def normalize_batch_for_analytics(tracking_inputs: list) -> list:
    res = requests.post(
        "https://shipping-tracking-normalization-api.p.rapidapi.com/normalize",
        headers={
            "Content-Type": "application/json",
            "x-rapidapi-key": RAPIDAPI_KEY,
            "x-rapidapi-host": "shipping-tracking-normalization-api.p.rapidapi.com"
        },
        json={"inputs": tracking_inputs}
    )
    data = res.json()
    return data["shipments"]  # Same schema → same columns in your DB or BI
```

---

## API Reference

### POST /normalize

Normalize one or more tracking inputs into the canonical shipment schema.

**Request body (application/json):**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `inputs` | array | Yes | List of tracking inputs (see below). |

**Each element of `inputs`:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `trackingNumber` | string | Yes | The tracking number. |
| `carrier` | string | No | e.g. `ups`, `fedex`, `usps`, `dhl`, `custom`. Omit to auto-detect from tracking number. |
| `payload` | object | No | Pre-fetched carrier JSON to normalize. Omit for minimal response (carrier + warning). |

**Response (200):**

| Field | Type | Description |
|-------|------|-------------|
| `results` | array | Per-item: index, trackingNumber, shipment or null, error, warnings. |
| `shipments` | array | All successfully normalized shipments (canonical schema). |
| `errors` | array | Item-scoped errors (code, message, trackingNumber, index, detail). |
| `metadata` | object | totalInputItems, normalizedCount, errorCount. |

**Example response:**

```json
{
  "results": [
    {
      "index": 0,
      "trackingNumber": "1Z999AA10123456784",
      "shipment": {
        "identifiers": { "trackingNumber": "1Z999AA10123456784" },
        "carrier": "ups",
        "currentStatus": "in_transit",
        "originalCarrierStatus": null,
        "events": [
          {
            "canonicalStatus": "accepted",
            "originalCarrierStatus": "Picked up",
            "timestampIso": "2024-01-14T13:00:00.000Z",
            "locationRaw": "Chicago",
            "eventType": "pickup",
            "carrier": "ups"
          },
          {
            "canonicalStatus": "in_transit",
            "originalCarrierStatus": "In transit",
            "timestampIso": "2024-01-15T15:00:00.000Z",
            "locationRaw": "Louisville",
            "eventType": "scan",
            "carrier": "ups"
          }
        ],
        "delivery": null,
        "provenance": { "carrier": "ups", "dataSourceType": "official_api", "parsingStrategy": "ups_track" },
        "confidence": { "confidence": 0.95, "warnings": [] }
      },
      "warnings": null
    }
  ],
  "shipments": [ ... ],
  "errors": [],
  "metadata": { "totalInputItems": 1, "normalizedCount": 1, "errorCount": 0 }
}
```

**Error codes:** `INVALID_REQUEST`, `INVALID_JSON`, `INVALID_TRACKING_NUMBER`, `INVALID_PAYLOAD`, `NO_TRACKING_DATA`, `PARSE_ERROR`, `PAYLOAD_TOO_LARGE`, `INTERNAL_ERROR`

### GET /health

Returns `{ "status": "ok" }`.

---

## Canonical Status & Schema

**Status taxonomy (fixed):**  
`pending` · `label_created` · `accepted` · `in_transit` · `out_for_delivery` · `delivered` · `exception` · `failed_attempt` · `returned` · `cancelled` · `customs_cleared` · `unknown`

**Shipment fields:**  
identifiers (trackingNumber, carrierTrackingNumber, referenceNumbers, previousTrackingNumbers) · carrier · currentStatus · originalCarrierStatus · events (array) · delivery (estimatedDeliveryDate, actualDeliveryTimestamp, signedBy, proofOfDelivery) · provenance · confidence

**Event fields:**  
canonicalStatus · originalCarrierStatus · timestampRaw · timestampIso · locationRaw · location (city, state, country, raw) · eventType · carrier

**Supported carriers (first-class):** UPS, FedEx, USPS, DHL. Others use `custom` normalization.

---

## Related APIs

Explore more developer tools from [Precision Solutions Tech on RapidAPI](https://rapidapi.com/user/precisionsolutionstech):

| API | Description |
|-----|-------------|
| [**Shipping & Tracking Normalization**](https://rapidapi.com/precisionsolutionstech/api/shipping-tracking-normalization-api) | This API — unify UPS, FedEx, USPS, DHL into one schema |
| [Calendar Event Normalization](https://rapidapi.com/precisionsolutionstech/api/calendar-event-normalization) | Normalize calendar events from Google, Outlook, Apple, and more |
| [Job Posting Normalization](https://rapidapi.com/precisionsolutionstech/api/job-posting-normalization-api) | Normalize job postings from LinkedIn, Indeed, Greenhouse, 25+ platforms |
| [API Error & Status Normalization](https://rapidapi.com/precisionsolutionstech/api/api-error-status-normalization) | Canonical error taxonomy and retry guidance |
| [Retail Data Normalization & Comparison](https://rapidapi.com/precisionsolutionstech/api/retail-data-normalization-comparison) | Normalize product data across Amazon, Walmart, eBay |
| [Sensitive Data Detection & Redaction](https://rapidapi.com/precisionsolutionstech/api/sensitive-data-detection-redaction-api) | Detect and redact PII in text |
| [HTML to Markdown Converter](https://rapidapi.com/precisionsolutionstech/api/html-to-markdown-converter1) | Convert HTML to GitHub Flavored Markdown |
| [JSON Schema Validator](https://rapidapi.com/precisionsolutionstech/api/json-schema-validator-api) | Validate JSON against schemas |

**[View all APIs →](https://rapidapi.com/user/precisionsolutionstech)**

---

## FAQ

### Do I need carrier API keys?

No. This API only processes data you send. You obtain tracking data through your own integrations (carrier APIs, webhooks, scrapers) and pass it to `/normalize`.

### What if I only have tracking numbers (no payload)?

Send inputs with just `trackingNumber` (and optional `carrier`). The API returns a minimal shipment (carrier, currentStatus: pending, no events) and a warning. Use it to show carrier or to know which API to call for full data.

### What if one input is invalid?

The API returns per-item results and errors. Invalid items get an entry in `results` with an `error` and null `shipment`; other inputs are still normalized. Check `errors` and `results[].error` for codes and messages. `metadata.errorCount` matches the number of failed items.

### How is carrier detected when I don't specify it?

The API uses tracking number patterns (e.g. 1Z... for UPS, length and prefixes for FedEx/USPS/DHL). When you declare a carrier that doesn't match the detected one, a warning is added and confidence is reduced.

### Is the output deterministic?

Yes. Same input produces the same output. No randomness or timestamps in the canonical structure.

### Is my data stored or logged?

No. The API is stateless. Payloads are processed in memory and not persisted.

### What's the maximum payload size?

25MB per request. Larger payloads return 413 `PAYLOAD_TOO_LARGE`.

### Can I mix carriers in one request?

Yes. Send an `inputs` array with different carriers (or omit carrier for auto-detect). You get one `shipments` array with the same schema for every carrier.

### What carriers are supported?

UPS, FedEx, USPS, and DHL have first-class normalizers. Other carriers use `custom` normalization (best-effort from common field names). You can still send any carrier name; unknown names fall back to custom.

---

<p align="center">
  <a href="https://rapidapi.com/precisionsolutionstech/api/shipping-tracking-normalization-api">Try Shipping & Tracking Normalization API on RapidAPI</a> ·
  <a href="https://rapidapi.com/user/precisionsolutionstech">All APIs by Precision Solutions Tech</a>
</p>
