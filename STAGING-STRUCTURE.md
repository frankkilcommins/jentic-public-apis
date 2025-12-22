# Staging Repository Structure (WIP)

This document describes the standardized directory structure for an Open Agentic Knowledge (OAK) repository. This repository follows this standard to organize its API specifications and workflows.

## Overview

The repository uses a structured approach to organize:
1. API specifications - standardized OpenAPI documents organized by vendor
2. Workflows - collections of operations that may reference one or more APIs

## Directory Structure

```
.
├── apis                                                  # All API-related files
│   └── openapi                                           # OpenAPI specifications
│       ├── vendor1.com                                   # Vendor directory (usually domain name)
│       │   └── main                                      # Default API for vendors with single API
│       │       ├── 1.0.0                                 # Version directory
│       │       │   ├── openapi.json                      # Standardized OpenAPI spec
│       │       │   ├── apis.json                         # apis.json single API file - see https://apisjson.org/
│       │       │   ├── scorecard.json                    # API scorecard file - see https://jentic.com/scorecard
│       │       │   └── meta                              # A directory containing various metadata files
│       │       │       ├── diagnostics.json              # Diagnostic information about the API
│       │       │       ├── meta.json                     # various metrics and metadata about the API
│       │       │       ├── import                        # A directory containing  metadata about the import process
│       │       │       │   ├── diagnostics-import.json   # Diagnostic information related to the import process
│       │       │       │   └── input-bundled.json        # Imported bundled OpenAPI spec 
│       │       │       │   └── input-entry.json          # Imported unbundled OpenAPI spec
│       │       │       │   └── output-bundled.json       # Processed bundled OpenAPI spec
│       │       │       │   └── output-entry.json         # Processed unbundled OpenAPI spec
│       │       │       │   └── meta.json                 # API metadata for the specific import process
│       │       │       └── source                        # A directory containing a pristine copy of the original file
│       │       │          └── source.dat                 # A pristine copy of the original file
│       │       └── meta.json                             # API metadata 
|       ├── apis.json                                     # apis.json collection APIs file - see https://apisjson.org/
...


## API Structure Details

### Vendor Organization

APIs are organized by vendor identifier first, allowing multiple APIs from the same vendor to be grouped together:

- **Vendor directory**: The top level is the vendor identifier, usually a domain name but not necessarily (e.g., `microsoft.com`, `googleapis.com`, `stripe.com`)
- **API directories**: Within each vendor directory, one or more API directories exist:
  - **main**: For vendors with only one API, the API is stored in the `main` directory
  - **specific-api-name**: For vendors with multiple APIs, each API gets its own directory with a descriptive name

### Version Management

Each API is versioned:

- Version directories are named according to the version in the OpenAPI spec (`info.version`)
- Each version contains:
  - `openapi.json`: The standardized OpenAPI specification in JSON format
  - `apis.json`: An [APIs.json](https://apisjson.org/) file representing the API
  - `meta/`: A directory containing various metadata files, including the original file downloaded from the vendor

### Metadata

Each API has a `meta.json` file at the API directory level containing:

```json
{
  "oak_meta": "1.0.0",
  "api_info": {
    "id": "md5hash",
    "format": "openapi",
    "vendor": "vendor",
    "api_name": "api_name",
    "friendly_id": "vendor" or "vendor/api_name"
  }
}
```

- **id**: An MD5 hash of "vendor/api_name" for unique identification
- **format**: The specification format (typically "openapi")
- **vendor**: The vendor identifier (usually a domain name)
- **api_name**: The API name (or "main" for single APIs)
- **friendly_id**: A human-readable identifier:
  - For main APIs: just the vendor identifier (e.g., "microsoft.com")
  - For non-main APIs: vendor/api_name (e.g., "microsoft.com/graph")

The source URL is stored as `x-jentic-source-url` in the `info` section of the standardized OpenAPI spec. This provides traceability back to the original source of the API specification.

## Workflow Structure Details

Workflows are organized to clearly indicate which APIs they reference:

### Directory Naming Conventions

- **Single API workflows**: Directory named after the API they reference
  - For main APIs: just the vendor identifier (e.g., `stripe.com`)
  - For non-main APIs: vendor~api-name (e.g., `microsoft.com~graph`)
- **Multi-API workflows**: Directory name combines APIs in alphabetical order
  - Uses `+` to separate different APIs (e.g., `stripe.com+twilio.com~messaging`)
  - Uses `~` to separate vendor and API name for non-main APIs
