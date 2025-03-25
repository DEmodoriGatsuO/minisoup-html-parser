---
layout: default
title: Fetch HTML Operation
description: Documentation for the MiniSoup Fetch HTML operation
---

# Fetch HTML Operation

The **Fetch HTML** operation retrieves HTML content from a specified URL and performs preliminary processing to prepare it for subsequent operations.

## Overview

This operation is typically the first step in a MiniSoup processing flow. It handles HTTP requests, follows redirects, and performs basic HTML preprocessing to ensure the content is ready for parsing.

| Operation Information |                                     |
|-----------------------|-------------------------------------|
| **Endpoint**          | `/fetch-html`                       |
| **Method**            | `POST`                              |
| **Operation ID**      | `FetchHTML`                         |
| **Required Parameters** | `url`                             |

## Request Parameters

| Parameter  | Type   | Required | Description                             | Default |
|------------|--------|----------|-----------------------------------------|---------|
| `operation` | string | Yes      | Operation type (internal parameter)     | `fetch_html` |
| `url`       | string | Yes      | URL to fetch HTML content from          | None    |

## Response Structure

### Success Response

A successful response includes the fetched HTML content:

```json
{
  "success": true,
  "html": "<html><head><title>Example Domain</title></head><body><h1>Example Domain</h1><p>This domain is for use in illustrative examples in documents.</p></body></html>"
}
```

### Error Response

An error response provides details about what went wrong:

```json
{
  "success": false,
  "error": "Failed to fetch URL: 404 Not Found"
}
```

## Implementation Details

The Fetch HTML operation performs the following steps:

1. Validates and normalizes the provided URL
2. Sets appropriate HTTP headers (User-Agent, Accept)
3. Sends an HTTP GET request to the target URL
4. Handles HTTP errors and redirects
5. Retrieves the response content
6. Preprocesses the HTML (removes comments, normalizes whitespace)
7. Returns the processed HTML content

## HTML Preprocessing

The HTML content undergoes the following preprocessing:

- Removal of HTML comments (`<!-- ... -->`)
- Normalization of whitespace (consecutive spaces, tabs, and line breaks are replaced with a single space)
- Optional: Script tag removal (commented out by default)

This preprocessing makes the HTML more consistent and easier to parse in subsequent operations.

## Usage in Power Automate

### Basic Example

```
Action: MiniSoup HTML Parser - FetchHTML
Inputs:
  - URL: https://example.com
Outputs:
  - Success: true
  - HTML: [HTML content of the page]
```

### Full Flow Example

```
Trigger: Manual
↓
Action 1: MiniSoup HTML Parser - FetchHTML
  - URL: https://example.com
↓
Action 2: Initialize variable
  - Name: HTMLContent
  - Type: String
  - Value: outputs('MiniSoup_HTML_Parser').html
↓
Action 3: MiniSoup HTML Parser - Select Elements
  - HTML: variables('HTMLContent')
  - Selector: h1
```

## Tips and Best Practices

- **URL Formatting**: The URL should include the protocol (`http://` or `https://`). If omitted, `https://` will be added automatically.
- **Throttling**: Avoid excessive requests to the same domain in a short period to prevent being blocked.
- **Error Handling**: Always check the `success` property in the response before processing the HTML content.
- **Large Pages**: For very large web pages, consider extracting only the relevant sections using a subsequent Select operation.
- **Authentication**: For sites requiring authentication, use the standard HTTP action to fetch the content with authentication and pass the response to other MiniSoup operations.

## Common Error Scenarios

| Error Message | Possible Cause | Resolution |
|---------------|----------------|------------|
| "URL is required" | The URL parameter is missing or empty | Provide a valid URL in the request |
| "Failed to fetch URL: 404 Not Found" | The requested page does not exist | Verify the URL is correct and the page exists |
| "Failed to fetch URL: 403 Forbidden" | Access to the page is restricted | The site may be blocking automated access |
| "Error fetching HTML: The operation has timed out" | The request took too long to complete | The site may be slow or unresponsive |

## Related Operations

- [Select Elements](select) - Use the fetched HTML to select specific elements
- [Extract Values](extract) - Extract values from the fetched HTML
- [Find All Elements](find-all) - Find all elements of a certain type in the fetched HTML
- [Parse Table](parse-table) - Parse tables in the fetched HTML