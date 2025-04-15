# WAI - Web AI Interface

WAI (Web AI Interface) is a standardized JSON schema designed to expose public information about a company. The purpose of WAI is to provide AI agents with a consistent, machine-readable interface to gather details about an organization. The JSON file must be served publicly at the following endpoints via GET requests:

- `http://exampledomain.com/wai`
- `https://exampledomain.com/wai`

These endpoints allow AI agents and other systems to easily discover and consume the company information.

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
- [Example JSON](#example-json)
- [Publishing Your WAI JSON](#publishing-your-wai-json)
- [Usage Guidelines](#usage-guidelines)
- [Extensibility](#extensibility)

---

## Overview

**WAI (Web AI Interface)** defines a JSON structure that presents comprehensive company information. The design provides a "business card on steroids" that is both human- and machine-readable, enabling AI agents to quickly obtain insights about an organization.

For companies with many locations (e.g., McDonald's), it may be impractical to embed full location details directly in the main JSON file. In such cases, a reference (or redirect) should be provided to an external API endpoint that supplies the full list of locations. This endpoint is included in the **publicAPI** array with a single **name** field (e.g., "Locations API").

---

## Schema Structure

The JSON document is divided into two main sections:

1. **business**: Contains all core details about the company, including its identity, locations (or a summary thereof), and any owned businesses.
2. **publicAPI**: An array of objects that describes public API endpoints. These endpoints allow consumers to retrieve extended business-related data (e.g., a full list of locations, product details, subscriptions).

---

## Business Object

The `business` object holds the main profile of the company and is organized into several nested sections.

### Basic Information

- **name** (string): The official company name.
- **description** (string): A brief overview of the company’s operations.
- **tagline** (string): A concise slogan or mission statement.
- **industry** (string): The sector in which the company operates.
- **keywords** (array of strings): Tags to categorize the business.
- **legalName** (string): The registered legal name (if different from the public name).
- **founded** (string): The founding date (preferably in ISO format).
- **website** (string): The primary website URL.
- **logo** (string): URL to the company’s logo image.

### Social Media

- **social** (object): Contains URLs for the company’s social profiles. Recommended keys include:
  - **facebook**
  - **twitter**
  - **linkedin**
  - *Additional platforms as needed.*

### Locations

The company may operate from multiple physical locations. In the JSON file, the `locations` field can include a summary or a few key locations. For companies with a large number of branches, it is recommended to provide a reference to a dedicated API endpoint supplying the full details.

Each location object (when included inline) contains:

- **name** (string): The location’s designation (e.g., "Main Headquarters", "Downtown Outlet").
- **type** (string): The category of the location (e.g., `Production`, `Office`, `retail`, `head quarter`, `warehouse`, `laboratory`, etc.).
- **address** (object): Contains the following:
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

*Note: For companies with many locations, consider omitting detailed location data from the main JSON and instead provide a "Locations API" endpoint in **publicAPI** that returns full location details.*

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

- **name** (string, optional): A short identifier for the endpoint (e.g., "Locations API").
- **description** (string): A brief explanation of the endpoint’s purpose.
- **type** (string): The HTTP method to use (GET, POST, etc.).
- **endpoint** (string): The URL of the API endpoint.
- **parameters** (array, optional): A list of parameters that may be used with this endpoint (e.g., pagination parameters or submission fields).

For example, a "Locations API" endpoint may be provided to return the full list of all business locations when they are too many to embed in the main JSON.

---

## Example JSON

Below is an example JSON document for WAI:

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
      "parameters": ["perPage", "page","latitude","longitude","distance"]
    }
  ]
}
