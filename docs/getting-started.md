---
layout: default
title: Getting Started with MiniSoup
description: Installation and basic usage instructions for MiniSoup HTML Parser
---

# Getting Started with MiniSoup

This guide will walk you through the process of installing and setting up the MiniSoup HTML Parser as a custom connector in Microsoft Power Automate, and demonstrate basic usage patterns.

## Prerequisites

Before implementing MiniSoup, ensure you have:

- Microsoft Power Automate Premium license with custom connector capabilities
- Administrative permissions to create and manage custom connectors
- Basic understanding of HTML structure and CSS selectors

## Installation Steps

### 1. Create a New Custom Connector

1. Log in to [Microsoft Power Automate](https://flow.microsoft.com/)
2. Navigate to **Data** in the left sidebar
3. Click on **Custom connectors**
4. Select **New custom connector** → **Create from blank**

### 2. Configure General Information

Configure the basic information for your connector:

1. **Connector name**: `MiniSoup HTML Parser`
2. **Description**: `Lightweight HTML parsing library for web scraping and data extraction`
3. **Host**: `api.example.com` (this is just a placeholder)
4. **Base URL**: `/api` (this is just a placeholder)
5. Upload an icon (optional)
6. Click **Continue**

### 3. Set Up Security

1. In the **Security** tab, choose **No authentication** from the dropdown
2. Click **Continue**

### 4. Import the API Definition

1. In the **Definition** tab, click on **Import** → **Import from file**
2. Upload the `apiDefinition.swagger.json` file from this repository
3. Click **Continue**

### 5. Implement Custom Code

1. Go to the **Code** tab
2. Click **Update connector**
3. Delete any existing code in the editor
4. Copy and paste the entire contents of the `MiniSoup.cs` file from this repository
5. Click **Update connector**

### 6. Create the Connector

1. Click **Create connector** at the top of the page
2. Your MiniSoup HTML Parser is now ready to use in your flows!

## Basic Usage

Once the connector is set up, you can use MiniSoup in your flows. Here are some basic usage patterns:

### Fetching HTML from a Website

```
Action: MiniSoup HTML Parser - FetchHTML
Input:
  - URL: https://example.com
Output:
  - HTML content from the website
```

### Selecting Elements with CSS Selectors

```
Action: MiniSoup HTML Parser - SelectElements
Input:
  - HTML: [HTML content from previous action]
  - Selector: div.product-container
  - Selector Type: css
Output:
  - Array of matching HTML elements
```

### Extracting Values from Elements

```
Action: MiniSoup HTML Parser - ExtractValues
Input:
  - HTML: [HTML content]
  - Selector: span.price
  - Attribute: text
Output:
  - Array of extracted text values
```

### Finding Elements by Tag and Attributes

```
Action: MiniSoup HTML Parser - FindAllElements
Input:
  - HTML: [HTML content]
  - Tag Name: a
  - Attributes: {"class": "product-link", "data-category": "electronics"}
Output:
  - Array of matching HTML elements
```

### Parsing an HTML Table

```
Action: MiniSoup HTML Parser - ParseTable
Input:
  - HTML: [HTML content]
  - Table Selector: table.data-table
  - Header Rows Exist: true
Output:
  - Structured table data with headers and rows
```

## CSS Selector Quick Reference

MiniSoup supports these common CSS selector patterns:

| Selector | Example | Description |
|----------|---------|-------------|
| Element | `div` | Selects all `<div>` elements |
| Class | `.product` | Selects elements with `class="product"` |
| ID | `#header` | Selects the element with `id="header"` |
| Attribute | `[type="submit"]` | Selects elements with `type="submit"` |
| Attribute (tag specific) | `input[type="text"]` | Selects `<input>` elements with `type="text"` |

## XPath Quick Reference

MiniSoup supports basic XPath expressions:

| Expression | Example | Description |
|------------|---------|-------------|
| Element | `//div` | Selects all `<div>` elements |
| Attribute | `//div[@class="product"]` | Selects `<div>` elements with `class="product"` |

## Next Steps

Now that you've set up MiniSoup and understand the basics, you're ready to explore more advanced usage patterns:

- [Operations Documentation](operations/) - Detailed explanation of each operation
- [Use Case Examples](use-cases/) - Real-world business applications
- [Technical Documentation](technical/) - Architecture and implementation details
- [Flow Examples](examples/) - Complete flow examples