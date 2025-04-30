![Alt Text](wai-logos/wai-logo-w-800.png)

# WAI - Web Agent Interface

WAI (Web Agent Interface) is a standardized JSON schema designed to expose public information about a company and its agent-facing services for consumption by automated bots, digital assistants, and AI agents. By providing a single, machine-readable `/wai` endpoint, agents can discover, integrate, and subscribe to your organization’s capabilities—whether RESTful APIs, push/event feeds, or agent-to-agent interfaces.

This README covers:

- [Overview](#overview)
- [Endpoints](#endpoints)
- [Schema Sections](#schema-sections)
- [Business Information](#business-information)
- [Website Public API](#website-public-api)
- [Website Agent Interfaces](#website-agent-interfaces)
- [Example JSON (`wai.json`)](#example-json-waijson)
- [Publishing and Usage](#publishing-and-usage)
- [Extensibility](#extensibility)

---

## Overview

The WAI JSON document must be served publicly at the following endpoints via GET requests:

- `http://<your-domain>/wai`
- `https://<your-domain>/wai`

When an agent fetches `/wai`, it receives:

1. **Business Information**: Core company details and metadata.  
2. **Website Public API**: A registry of your REST/HTTP endpoints.  
3. **Website Agent Interfaces**: Agent-facing capabilities, including event feeds, MCP contexts, and A2A interfaces.

By centralizing discovery in `/wai`, you streamline onboarding, enable dynamic integration, and reduce documentation overhead.

---

## Endpoints

- `GET /wai           # Returns the full WAI JSON`
- `GET /wai.json      # Alias for compatibility`

Additional endpoints (e.g., `/agent/<name>.json`, `/api/...`) are listed inside the WAI payload.

---

## Schema Sections

1. **businessInformation** (object)  
2. **publicAPI** (array)  
3. **agentInterfaces** (array)  

Each section conforms to a well-defined structure (see details below).

---

## Business Information

The `businessInformation` object holds your organization’s core profile.

```yaml
businessInformation:
  wai_version:      number       # WAI schema version, e.g. 1
  updated:          string       # MM/DD/YYYY when last updated
  name:             string
  legalName?:       string
  description:      string
  tagline?:         string
  industry?:        string
  keywords?:        [string]
  founded?:         string       # ISO date preferred
  website:          string       # URL
  logo:             string       # URL
  social:           [            # multiple social links
    {
      platform:     string       # e.g. "twitter"
      link:         string       # full URL
      description?: string       # e.g. "company news"
    }
  ]
  locations?:       [object]     # Inline locations or omit for large sets
  ownedBusinesses?: [object]
  additional?:      { employees?: number, other?: object }
```

---

## Public API

The `publicAPI` array catalogs your RESTful or HTTP-based endpoints.

```yaml
publicAPI:
  - endpoint: string            # full URL, e.g. "https://.../api/products"
    type:     string            # e.g. "REST"
    options:
      method:     string        # "GET", "POST", etc.
      parameters:
        - name:        string   # parameter name
          required:    boolean  # true/false
          type:        string   # "string", "integer", etc.
          description?:string  # optional description
```

---

## Agent Interfaces

The `agentInterfaces` array lists agent-facing contexts and services.

```yaml
agentInterfaces:
  - name:          string       # e.g. "Calendar Booking"
    description:   string       # human-readable summary
    interfaceUrl:  string       # URL to the context/schema JSON
    interfaceType: string       # e.g. "MCP", "A2A", "Event", "Webhook"
```

Supported `interfaceType` values:

- **MCP**: Model Context Protocol files for structured AI workflows.  
- **A2A**: Direct agent-to-agent synchronization interfaces.  
- **Event**: Publish/subscribe event feeds (e.g. fraud alerts).  
- **Webhook**: HTTP callbacks for real-time handoffs.

---

## Example JSON (`wai.json`)

```json
{
  "businessInformation": {
    "wai_version": 1,
    "updated": "04/16/2025",
    "name": "Example Business Inc.",
    "legalName": "Example Business Incorporated",
    "description": "A cutting-edge provider of innovative solutions tailored for digital transformation.",
    "tagline": "Empowering your digital journey",
    "industry": "Technology",
    "keywords": ["innovation", "digital", "transformation"],
    "founded": "2010-05-12",
    "website": "https://www.examplebusiness.com",
    "logo": "https://www.examplebusiness.com/assets/logo.png",
    "social": [
      { "platform": "facebook", "link": "https://facebook.com/examplebusiness", "description": "company page" },
      { "platform": "twitter",  "link": "https://twitter.com/examplebiz",      "description": "product updates" },
      { "platform": "linkedin", "link": "https://linkedin.com/company/examplebusiness", "description": "corporate profile" }
    ],
    "locations": [],
    "ownedBusinesses": [],
    "additional": { "employees": 150, "other": { "certifications": ["ISO9001", "Tech Innovator Award 2023"] } }
  },

  "publicAPI": [
    {
      "endpoint": "https://example.com/api/products",
      "type": "REST",
      "options": {
        "method": "GET",
        "parameters": [
          { "name": "perPage", "required": false, "type": "integer" },
          { "name": "page",    "required": false, "type": "integer" }
        ]
      }
    },
    {
      "endpoint": "https://example.com/api/subscribeemail",
      "type": "REST",
      "options": {
        "method": "POST",
        "parameters": [
          { "name": "email", "required": true,  "type": "string" },
          { "name": "phone", "required": false, "type": "string" }
        ]
      }
    }
  ],

  "agentInterfaces": [
    {
      "name": "Calendar Booking",
      "description": "Check availability and book meetings via our calendar API.",
      "interfaceUrl": "https://exampledomain.com/agent/calendar-booking.json",
      "interfaceType": "MCP"
    },
    {
      "name": "Order Management",
      "description": "Place and manage orders via our public ordering system.",
      "interfaceUrl": "https://exampledomain.com/agent/order-management.json",
      "interfaceType": "MCP"
    },
    {
      "name": "Inventory Sync",
      "description": "Agent-to-agent stock synchronization for partner platforms.",
      "interfaceUrl": "https://exampledomain.com/agent/inventory-sync.json",
      "interfaceType": "A2A"
    },
    {
      "name": "Live Chat Relay",
      "description": "Real-time handoff between AI assistant and human chat operator.",
      "interfaceUrl": "https://exampledomain.com/agent/live-chat-relay.json",
      "interfaceType": "Webhook"
    },
    {
      "name": "Fraud Alert",
      "description": "Push notifications of suspicious transactions to security agents.",
      "interfaceUrl": "https://exampledomain.com/agent/fraud-alert.json",
      "interfaceType": "Event"
    }
  ]
}
```

---

## Publishing and Usage

1. Place your `wai.json` at the root of your public web server.  
2. Configure your web server to serve them at `/wai` and `/wai.json`.  
3. Agents simply `GET /wai` to discover everything your company want to share, and what action interfaces your website provides.

---

## Extensibility

- **Add new APIs** by appending to `publicAPI`.  
- **Add new agent interfaces** by appending to `agentInterfaces` with the appropriate `interfaceType`.  
- **Versioning**: bump `updated` field and replace the old wai.json file.  
