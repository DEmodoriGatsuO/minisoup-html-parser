---
layout: default
title: MiniSoup Operations
description: Overview of all operations available in the MiniSoup HTML Parser
---

# MiniSoup Operations

MiniSoup provides five core operations for HTML processing, each designed to handle a specific aspect of HTML parsing and data extraction. This page provides an overview of each operation and links to detailed documentation.

## Operations Overview

<div class="operations-grid">
  <div class="operation-card">
    <h3><a href="fetch-html">Fetch HTML</a></h3>
    <p>Retrieves HTML content from a specified URL</p>
    <div class="operation-details">
      <span class="method">POST</span>
      <span class="endpoint">/fetch-html</span>
    </div>
  </div>
  
  <div class="operation-card">
    <h3><a href="select">Select Elements</a></h3>
    <p>Selects HTML elements matching a CSS selector or XPath</p>
    <div class="operation-details">
      <span class="method">POST</span>
      <span class="endpoint">/select</span>
    </div>
  </div>
  
  <div class="operation-card">
    <h3><a href="extract">Extract Values</a></h3>
    <p>Extracts specific values or attributes from selected elements</p>
    <div class="operation-details">
      <span class="method">POST</span>
      <span class="endpoint">/extract</span>
    </div>
  </div>
  
  <div class="operation-card">
    <h3><a href="find-all">Find All Elements</a></h3>
    <p>Finds all elements matching a tag name and optional attributes</p>
    <div class="operation-details">
      <span class="method">POST</span>
      <span class="endpoint">/find-all</span>
    </div>
  </div>
  
  <div class="operation-card">
    <h3><a href="parse-table">Parse Table</a></h3>
    <p>Parses an HTML table into structured data</p>
    <div class="operation-details">
      <span class="method">POST</span>
      <span class="endpoint">/parse-table</span>
    </div>
  </div>
</div>

## Typical Operation Flow

MiniSoup operations are typically used in sequence to progressively process HTML content:

1. **Fetch HTML** - Retrieve HTML content from a target website
2. **Select Elements** or **Find All Elements** - Identify the relevant elements in the HTML
3. **Extract Values** - Extract specific data from the selected elements
4. **Parse Table** (when applicable) - Convert HTML tables to structured data

## Common Operation Parameters

All MiniSoup operations follow a consistent parameter pattern:

| Parameter | Description |
|-----------|-------------|
| `operation` | Internal parameter specifying the operation type |
| `html` | The HTML content to process (except for Fetch HTML) |
| `success` | Response parameter indicating operation success/failure |
| `error` | Response parameter providing error details when applicable |

## Response Structure

All operations return responses with a consistent structure:

```json
{
  "success": true,
  "count": 5,
  // Operation-specific data
}
```

Error responses follow this structure:

```json
{
  "success": false,
  "error": "Detailed error message"
}
```

## Next Steps

Select an operation from the links above to view detailed documentation, including parameter descriptions, example requests and responses, and usage guidance.

<style>
.operations-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 20px;
  margin: 30px 0;
}

.operation-card {
  background-color: #f5f5f5;
  border-radius: 5px;
  padding: 20px;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

.operation-card h3 {
  margin-top: 0;
  border-bottom: 1px solid #ddd;
  padding-bottom: 10px;
}

.operation-details {
  margin-top: 15px;
  display: flex;
  gap: 10px;
}

.method {
  background-color: #61affe;
  color: white;
  padding: 4px 8px;
  border-radius: 3px;
  font-family: monospace;
  font-weight: bold;
}

.endpoint {
  background-color: #f0f0f0;
  padding: 4px 8px;
  border-radius: 3px;
  font-family: monospace;
}
</style>