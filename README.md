# WAI - Web Agent Interface

WAI (Web Agent Interface) is a standardized JSON schema designed to expose public information about a company for consumption by various agents (e.g., automated bots, digital assistants, and AI agents). The purpose of WAI is to provide agents with a consistent, machine-readable interface to gather comprehensive details about an organization. This JSON file must be served publicly at the following endpoints via GET requests:

- `http://exampledomain.com/wai`
- `https://exampledomain.com/wai`

These endpoints ensure that agents can easily discover, retrieve, and consume the company's information, as well as learn about additional capabilities via MCP (Model Context Protocol) contexts.

---

## Table of Contents

- [Overview](#overview)
- [Schema Structure](#schema-structure)
- [Business Object](#business-object)
  - [Basic Information](#basic-information)
  - [Social Media](#social-media)
  - [Locations](#locations)
  - [Owned Businesses](#owned-businesses)
  - [Additional Information](#additional-information)
- [Public API Endpoints](#public-api-endpoints)
- [MCP Contexts](#mcp-contexts)
- [Example JSON](#example-json)
- [Publishing Your WAI JSON](#publishing-your-wai-json)
- [Usage Guidelines](#usage-guidelines)
- [Extensibility](#extensibility)

---

## Overview

**WAI (Web Agent Interface)** defines a JSON structure that presents comprehensive company information in a "business card on steroids" format. The design is both human- and machine-readable, enabling agents to quickly obtain insights about an organization.

For companies with many physical locations (e.g., global chains like McDonald's), it may be impractical to embed full location details directly in the main JSON file. Instead, a reference (or redirect) should be provided to an external API endpoint that supplies the full list of locations. Such endpoints are included in the **publicAPI** array with appropriate identifiers (e.g., "Locations").

Additionally, WAI serves as a registry listing available MCP (Model Context Protocol) context files. These MCP JSON files provide detailed instructions on how to perform specific actions (for example, booking a meeting, processing orders, etc.), enabling agents to extend their functionality seamlessly.

---

## Schema Structure

The WAI JSON document is divided into three primary sections:

1. **business**: Contains the core company details, including identity, locations (or summaries), and owned businesses.
2. **publicAPI**: An array of objects that define public endpoints. These endpoints allow agents to retrieve extended business-related data (e.g., full location details, product information, subscriptions).
3. **mcpContexts**: An array of objects listing the available MCP (Model Context Protocol) contexts. Each object provides an identifier, description, and a fixed URL where the detailed MCP JSON can be fetched.

---

## Business Object

The `business` object holds the main profile of the company and is organized into several nested sections.

### Basic Information

- **name** (string): The official company name.
- **description** (string): A brief overview of the company’s operations.
- **tagline** (string): A concise slogan or mission statement.
- **industry** (string): The sector in which the company operates.
- **keywords** (array of strings): Tags for categorizing the business.
- **legalName** (string): The registered legal name (if different).
- **founded** (string): The founding date (preferably in ISO format).
- **website** (string): The primary website URL.
- **logo** (string): URL to the company’s logo image.

### Social Media

- **social** (object): Contains URLs for the company’s social profiles. Recommended keys include:
  - **facebook**
  - **twitter**
  - **linkedin**
  - *Additional platforms may be added as needed.*

### Locations

The company may operate from multiple physical locations. In the main JSON file, the `locations` field can include a summary or a few key locations. For companies with a large number of branches, it is recommended to provide a reference to a dedicated API endpoint that supplies the full details.

Each inline location object contains:

- **name** (string): The location’s designation (e.g., "Main Headquarters", "Downtown Outlet").
- **type** (string): The category of the location (e.g., `Production`, `Office`, `retail`, `head quarter`, `warehouse`, `laboratory`).
- **address** (object): Contains:
  - **street**
  - **city**
  - **state**
  - **postalCode**
  - **country**
  - **geo** (object):
    - **lat** (string or number): Latitude.
    - **lon** (string or number): Longitude.
- **contact** (object, optional): Location-specific contact details:
  - **phone** (string)
  - **email** (string)
- **hours** (object): Operating hours for each day of the week.

*Note: For companies with many locations, consider omitting detailed location data from the main JSON and instead provide a "Locations" API endpoint in the **publicAPI** section.*

### Owned Businesses

Companies that own or control other business entities list them in the `ownedBusinesses` array. For each subsidiary:

- **name** (string): The subsidiary’s name.
- **description** (string): A brief overview of the subsidiary.
- **website** (string): The subsidiary’s website URL.
- **logo** (string): URL to the subsidiary’s logo image.
- **social** (object): Social media links for the subsidiary.
- **locations** (array, optional): Follows the same structure as the main `locations` array.
- *Additional fields may be added as needed.*

### Additional Information

- **additional** (object): Used for supplementary details:
  - **employees** (number): Approximate number of employees.
  - **other** (object): Miscellaneous information (e.g., certifications, awards).

---

## Public API Endpoints

The **publicAPI** field is an array of objects that define public endpoints for retrieving extended business-related data. Each object includes:

- **name** (string, optional): A short identifier for the endpoint (e.g., "Locations", "Products", "Subscribe").
- **description** (string): A brief explanation of the endpoint’s purpose.
- **type** (string): The HTTP method to use (GET, POST, etc.).
- **endpoint** (string): The URL of the API endpoint.
- **parameters** (array, optional): A list of parameters that may be used with this endpoint (e.g., pagination parameters, filters, or data fields).

For example, the "Locations" endpoint may be provided to return a full list of business locations when there are too many to include inline in the main JSON.

---

## MCP Contexts

The **mcpContexts** field is an array that lists available MCP (Model Context Protocol) context files. These MCP JSON files provide detailed instructions to agents for performing specific actions, such as booking a meeting or processing an order.

Each MCP context object includes:

- **name** (string): A short identifier for the MCP context (e.g., "Calendar Booking").
- **description** (string): A summary of the capability provided by this context.
- **mcpUrl** (string): The fixed URL where the MCP JSON file is located (e.g., `https://exampledomain.com/mcp/calendar-booking.json`).

This registry allows agents to quickly identify and fetch the context they need for executing specific actions.

---

## Example JSON

Below is an example JSON document for WAI that integrates all sections, including MCP contexts:

```json
{
  "business": {
    "name": "Example Business Inc.",
    "description": "A cutting-edge provider of innovative solutions tailored for digital transformation.",
    "tagline": "Empowering your digital journey",
    "industry": "Technology",
    "keywords": ["innovation", "digital", "transformation"],
    "legalName": "Example Business Incorporated",
    "founded": "2010-05-12",
    "website": "https://www.examplebusiness.com",
    "logo": "https://www.examplebusiness.com/assets/logo.png",
    "social": {
      "facebook": "https://facebook.com/examplebusiness",
      "twitter": "https://twitter.com/examplebiz",
      "linkedin": "https://linkedin.com/company/examplebusiness"
    },
    "locations": [
      {
        "name": "Main Headquarters",
        "type": "head quarter",
        "address": {
          "street": "123 Innovation Drive",
          "city": "Techville",
          "state": "CA",
          "postalCode": "90001",
          "country": "USA",
          "geo": {
            "lat": "34.0522",
            "lon": "-118.2437"
          }
        },
        "contact": {
          "phone": "+1-800-555-1234",
          "email": "hq@examplebusiness.com"
        },
        "hours": {
          "monday": "09:00-17:00",
          "tuesday": "09:00-17:00",
          "wednesday": "09:00-17:00",
          "thursday": "09:00-17:00",
          "friday": "09:00-17:00",
          "saturday": "Closed",
          "sunday": "Closed"
        }
      }
    ],
    "ownedBusinesses": [
      {
        "name": "Subsidiary One LLC",
        "description": "A specialized subsidiary focusing on advanced research and innovative product development.",
        "website": "https://subsidiary1.examplebusiness.com",
        "logo": "https://subsidiary1.examplebusiness.com/assets/logo.png",
        "social": {
          "facebook": "https://facebook.com/subsidiary1",
          "twitter": "https://twitter.com/subsidiary1"
        },
        "locations": [
          {
            "name": "Subsidiary One Office",
            "type": "office",
            "address": {
              "street": "789 Research Ave",
              "city": "Innovation City",
              "state": "TX",
              "postalCode": "75001",
              "country": "USA",
              "geo": {
                "lat": "32.7767",
                "lon": "-96.7970"
              }
            },
            "contact": {
              "phone": "+1-800-555-6789",
              "email": "office@subsidiary1.com"
            },
            "hours": {
              "monday": "08:00-16:00",
              "tuesday": "08:00-16:00",
              "wednesday": "08:00-16:00",
              "thursday": "08:00-16:00",
              "friday": "08:00-16:00",
              "saturday": "Closed",
              "sunday": "Closed"
            }
          }
        ]
      }
    ],
    "additional": {
      "employees": 150,
      "other": {
        "certifications": ["ISO9001", "Tech Innovator Award 2023"]
      }
    }
  },
  "publicAPI": [
    {
      "name": "Products",
      "description": "List of Products",
      "type": "GET",
      "endpoint": "https://example.com/api/products",
      "parameters": ["perPage", "page"]
    },
    {
      "name": "Subscribe",
      "description": "Receive email news from our products",
      "type": "POST",
      "endpoint": "https://example.com/api/subscribeemail",
      "parameters": ["email", "phone"]
    },
    {
      "name": "Locations",
      "description": "Retrieve full or partial list of all business locations",
      "type": "GET",
      "endpoint": "https://example.com/api/locations",
      "parameters": ["perPage", "page", "latitude", "longitude", "distance"]
    }
  ],
  "mcpContexts": [
    {
      "name": "Calendar Booking",
      "description": "Allows agents to check availability and book meetings via our calendar API.",
      "mcpUrl": "https://exampledomain.com/mcp/calendar-booking.json"
    },
    {
      "name": "Order Management",
      "description": "Enables agents to place and manage orders via our public ordering system.",
      "mcpUrl": "https://exampledomain.com/mcp/order-management.json"
    }
  ]
}
