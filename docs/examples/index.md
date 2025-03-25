---
layout: default
title: MiniSoup Examples
description: Flow examples and code samples for the MiniSoup HTML Parser
---

# MiniSoup Examples

This section provides practical examples and sample flows to help you get started with the MiniSoup HTML Parser. These examples demonstrate common usage patterns and best practices.

## Example Categories

The examples are organized into different categories based on complexity and purpose:

<div class="examples-grid">
  <div class="example-card">
    <div class="example-icon">🔰</div>
    <h3><a href="basic-flows">Basic Flows</a></h3>
    <p>Simple examples demonstrating fundamental MiniSoup operations.</p>
    <ul>
      <li>Website metadata extraction</li>
      <li>Simple product price monitoring</li>
      <li>News headline collection</li>
      <li>Basic HTML table parsing</li>
    </ul>
  </div>
  
  <div class="example-card">
    <div class="example-icon">🔄</div>
    <h3><a href="integration-flows">Integration Flows</a></h3>
    <p>Examples showing MiniSoup integration with other systems.</p>
    <ul>
      <li>SharePoint integration</li>
      <li>Teams notifications</li>
      <li>Excel report generation</li>
      <li>Power BI dashboard updates</li>
    </ul>
  </div>
  
  <div class="example-card">
    <div class="example-icon">⚙️</div>
    <h3><a href="advanced-flows">Advanced Flows</a></h3>
    <p>Complex examples for sophisticated HTML parsing needs.</p>
    <ul>
      <li>Multi-page scraping with pagination</li>
      <li>Authenticated website access</li>
      <li>Complex data extraction and transformation</li>
      <li>Error handling and retry logic</li>
    </ul>
  </div>
  
  <div class="example-card">
    <div class="example-icon">🧩</div>
    <h3><a href="selector-examples">Selector Examples</a></h3>
    <p>Examples focusing on different selector techniques.</p>
    <ul>
      <li>CSS selector patterns</li>
      <li>XPath expressions</li>
      <li>Attribute selectors</li>
      <li>Complex selector combinations</li>
    </ul>
  </div>
  
  <div class="example-card">
    <div class="example-icon">🏢</div>
    <h3><a href="industry-examples">Industry-Specific Examples</a></h3>
    <p>Examples tailored to specific industry needs.</p>
    <ul>
      <li>Retail and e-commerce</li>
      <li>Financial data extraction</li>
      <li>Healthcare information monitoring</li>
      <li>Legal and regulatory tracking</li>
    </ul>
  </div>
  
  <div class="example-card">
    <div class="example-icon">🛠️</div>
    <h3><a href="utility-flows">Utility Flows</a></h3>
    <p>Helper flows and components for common tasks.</p>
    <ul>
      <li>HTML cleaning and preprocessing</li>
      <li>Selector testing and validation</li>
      <li>Flow templates and snippets</li>
      <li>Performance monitoring utilities</li>
    </ul>
  </div>
</div>

## Featured Examples

Here are some complete examples that demonstrate the capabilities of MiniSoup:

### Website Metadata Extraction

This example demonstrates how to extract metadata (title, description, and Open Graph data) from a website:

```
Trigger: Manual (Input: Website URL)
↓
Action 1: MiniSoup HTML Parser - FetchHTML
  • URL: Trigger input URL
↓
Action 2: Initialize variable
  • Name: HTML
  • Type: String
  • Value: outputs('FetchHTML').html
↓
Action 3: MiniSoup HTML Parser - ExtractValues
  • HTML: variables('HTML')
  • Selector: title
  • Attribute: text
↓
Action 4: Initialize variable
  • Name: PageTitle
  • Type: String
  • Value: first(outputs('ExtractValues').values)
↓
Action 5: MiniSoup HTML Parser - ExtractValues
  • HTML: variables('HTML')
  • Selector: meta[name='description']
  • Attribute: content
↓
Action 6: Initialize variable
  • Name: PageDescription
  • Type: String
  • Value: first(outputs('ExtractValues').values)
↓
Action 7: MiniSoup HTML Parser - ExtractValues
  • HTML: variables('HTML')
  • Selector: meta[property='og:image']
  • Attribute: content
↓
Action 8: Initialize variable
  • Name: OGImage
  • Type: String
  • Value: first(outputs('ExtractValues').values)
↓
Action 9: Respond to PowerApp or Flow
  • Status code: 200
  • Body: JSON object with extracted metadata
```

[View the complete flow template →](basic-flows#website-metadata-extraction)

### Product Price Monitoring

This example shows how to monitor a product price on an e-commerce website:

```
Trigger: Schedule (Daily at 9:00 AM)
↓
Action 1: MiniSoup HTML Parser - FetchHTML
  • URL: Product page URL
↓
Action 2: MiniSoup HTML Parser - ExtractValues
  • HTML: outputs('FetchHTML').html
  • Selector: span.product-price
  • Attribute: text
↓
Action 3: Process Price
  • Clean price text (remove currency symbols, etc.)
  • Convert to number
↓
Action 4: Get Previous Price
  • Read from SharePoint list or database
↓
Condition: Price Changed?
 ├── Yes
 │   ↓
 │   Action 5: Send Notification
 │   • Send email with price change details
 │   ↓
 │   Action 6: Update Price Record
 │   • Save new price to SharePoint or database
 │
 └── No
     ↓
     Action 7: Log Monitoring Activity
     • Record that price was checked but unchanged
```

[View the complete flow template →](basic-flows#product-price-monitoring)

## Download Sample Flow Collection

We've prepared a collection of sample flows that you can download and import directly into Power Automate:

<div class="download-section">
  <a href="../assets/templates/minisoup-sample-flows.zip" class="download-button">
    <span class="download-icon">📥</span>
    <span class="download-text">Download Sample Flow Collection</span>
  </a>
  <p class="download-description">A complete collection of MiniSoup sample flows, ready to import and customize.</p>
</div>

## How to Use These Examples

Each example includes:

1. **Flow Diagram**: Visual representation of the flow
2. **Step-by-Step Instructions**: Detailed explanation of each action
3. **Downloadable Template**: Ready-to-import flow template
4. **Customization Guidance**: How to adapt the example to your needs
5. **Common Issues**: Troubleshooting tips for potential problems

To use a flow template:

1. Download the flow template ZIP file
2. In Power Automate, go to **My flows** → **Import** → **Import from a file**
3. Select the downloaded ZIP file
4. Follow the import wizard to configure connections
5. Test and customize the flow as needed

## Contributing Examples

Have you created an interesting flow using MiniSoup? Consider contributing it to our examples collection:

1. Create a GitHub issue with the "Example Contribution" template
2. Describe your example and its purpose
3. Attach your flow export (ZIP file)
4. We'll review and add it to the collection

## Examples Request

Need an example for a specific use case not covered here? Let us know by creating a GitHub issue with the "Example Request" template.

<style>
.examples-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 20px;
  margin: 30px 0;
}

.example-card {
  background-color: #f5f5f5;
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 3px 6px rgba(0,0,0,0.1);
  transition: transform 0.2s, box-shadow 0.2s;
}

.example-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 6px 12px rgba(0,0,0,0.15);
}

.example-icon {
  font-size: 2.5rem;
  margin-bottom: 15px;
  text-align: center;
}

.example-card h3 {
  margin-top: 0;
  border-bottom: 1px solid #ddd;
  padding-bottom: 10px;
}

.example-card ul {
  padding-left: 20px;
}

.example-card li {
  margin-bottom: 5px;
}

.download-section {
  background-color: #f0f7ff;
  border-radius: 8px;
  padding: 25px;
  margin: 30px 0;
  text-align: center;
}

.download-button {
  display: inline-block;
  background-color: #0366d6;
  color: white;
  padding: 12px 24px;
  border-radius: 4px;
  text-decoration: none;
  font-weight: bold;
  margin-bottom: 15px;
  transition: background-color 0.2s;
}

.download-button:hover {
  background-color: #0256c2;
}

.download-icon {
  margin-right: 10px;
}

.download-description {
  margin: 0;
  color: #555;
}

code {
  background-color: #f0f0f0;
  border-radius: 3px;
  padding: 2px 4px;
  font-family: Consolas, Monaco, 'Andale Mono', monospace;
}

pre {
  background-color: #f5f5f5;
  padding: 15px;
  border-radius: 5px;
  overflow-x: auto;
}
</style>