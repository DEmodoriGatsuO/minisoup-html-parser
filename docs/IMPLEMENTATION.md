# MiniSoup Implementation Guide

## 1. Introduction

MiniSoup is a lightweight HTML parsing library designed specifically for Microsoft Power Automate custom connectors. It provides HTML scraping and extraction capabilities without requiring external services, enabling business users to collect and process web data directly within their automation flows.

This document provides step-by-step instructions for implementing MiniSoup as a custom connector in Microsoft Power Automate, along with guidance on its effective use in various business scenarios.

## 2. Custom Connector Creation Process

### 2.1. Prerequisites

Before implementing MiniSoup, ensure you have:

- A Power Automate Premium license with custom connector capabilities
- Administrative permissions to create and manage custom connectors
- Basic understanding of HTML structure and selection concepts

### 2.2. Setting Up the Custom Connector

Follow these steps to create and configure the MiniSoup custom connector:

1. **Navigate to the custom connectors section:**
   - Log in to Power Automate
   - Select "Data" from the left navigation
   - Click on "Custom connectors"
   - Click "New custom connector" and select "Create from blank"

2. **Configure general information:**
   - Connector name: `MiniSoup HTML Parser`
   - Description: `Lightweight HTML parsing library inspired by Beautiful Soup for HTML element analysis and extraction`
   - Host: `api.example.com` (this is formal only and not actually used)
   - Base URL: `/api` (this is formal only and not actually used)
   - Upload an appropriate icon (optional)

3. **Configure security:**
   - Authentication type: `No authentication` (for simplicity)
   - Click "Update connector"

### 2.3. Defining the Connector's Operations

Navigate to the "Definition" tab and create the following operations:

#### 2.3.1. Fetch HTML Operation

1. Click "New action"
2. Enter the following details:
   - Summary: `Fetch HTML Content`
   - Description: `Fetches HTML content from a specified URL`
   - Operation ID: `FetchHTML`
   - Visibility: `important`

3. For the request:
   - Method: `POST`
   - Relative path: `/fetch-html`

4. Define the request body:
   ```json
   {
     "type": "object",
     "properties": {
       "operation": {
         "type": "string",
         "description": "Operation type (internal parameter)",
         "default": "fetch_html",
         "x-ms-visibility": "internal"
       },
       "url": {
         "type": "string",
         "description": "URL to fetch HTML content from",
         "x-ms-visibility": "important",
         "example": "https://example.com"
       }
     },
     "required": [
       "operation",
       "url"
     ]
   }
   ```

5. Define the response body (200 OK):
   ```json
   {
     "type": "object",
     "properties": {
       "success": {
         "type": "boolean",
         "description": "Indicates whether the operation was successful"
       },
       "html": {
         "type": "string",
         "description": "HTML content retrieved from the specified URL"
       }
     },
     "required": [
       "success",
       "html"
     ]
   }
   ```

6. Define the default response (for errors):
   ```json
   {
     "type": "object",
     "properties": {
       "success": {
         "type": "boolean",
         "description": "Indicates that the operation failed"
       },
       "error": {
         "type": "string",
         "description": "Detailed error message explaining what went wrong"
       }
     },
     "required": [
       "success",
       "error"
     ]
   }
   ```

#### 2.3.2. Select Elements Operation

1. Click "New action"
2. Enter the following details:
   - Summary: `Select HTML Elements`
   - Description: `Selects HTML elements matching the provided selector`
   - Operation ID: `SelectElements`
   - Visibility: `important`

3. For the request:
   - Method: `POST`
   - Relative path: `/select`

4. Define the request body:
   ```json
   {
     "type": "object",
     "properties": {
       "operation": {
         "type": "string",
         "description": "Operation type (internal parameter)",
         "default": "select",
         "x-ms-visibility": "internal"
       },
       "html": {
         "type": "string",
         "description": "HTML content to be parsed",
         "x-ms-visibility": "important"
       },
       "selector": {
         "type": "string",
         "description": "CSS selector or XPath for targeting elements",
         "x-ms-visibility": "important"
       },
       "selector_type": {
         "type": "string",
         "description": "Type of selector to use",
         "enum": [
           "css",
           "xpath"
         ],
         "default": "css",
         "x-ms-visibility": "advanced"
       }
     },
     "required": [
       "operation",
       "html",
       "selector"
     ]
   }
   ```

5. Define the response body (200 OK) with reference to the HtmlElement schema:
   ```json
   {
     "type": "object",
     "properties": {
       "success": {
         "type": "boolean",
         "description": "Indicates whether the operation was successful"
       },
       "elements": {
         "type": "array",
         "description": "Array of HTML elements that match the specified selector",
         "items": {
           "$ref": "#/definitions/HtmlElement"
         }
       },
       "count": {
         "type": "integer",
         "description": "Number of elements found"
       }
     },
     "required": [
       "success",
       "elements",
       "count"
     ]
   }
   ```

6. Define the default response (for errors) as in the previous operation.

#### 2.3.3. Extract Values Operation

1. Click "New action"
2. Enter the following details:
   - Summary: `Extract Values from HTML Elements`
   - Description: `Extracts specific attribute values from HTML elements matching the provided selector`
   - Operation ID: `ExtractValues`
   - Visibility: `important`

3. For the request:
   - Method: `POST`
   - Relative path: `/extract`

4. Define the request body:
   ```json
   {
     "type": "object",
     "properties": {
       "operation": {
         "type": "string",
         "description": "Operation type (internal parameter)",
         "default": "extract",
         "x-ms-visibility": "internal"
       },
       "html": {
         "type": "string",
         "description": "HTML content to be parsed",
         "x-ms-visibility": "important"
       },
       "selector": {
         "type": "string",
         "description": "CSS selector or XPath for targeting elements",
         "x-ms-visibility": "important"
       },
       "attribute": {
         "type": "string",
         "description": "Attribute to extract from selected elements. Use 'text' for inner text, 'html' for inner HTML, or specific attribute name",
         "x-ms-visibility": "important"
       },
       "selector_type": {
         "type": "string",
         "description": "Type of selector to use",
         "enum": [
           "css",
           "xpath"
         ],
         "default": "css",
         "x-ms-visibility": "advanced"
       }
     },
     "required": [
       "operation",
       "html",
       "selector",
       "attribute"
     ]
   }
   ```

5. Define the response body (200 OK):
   ```json
   {
     "type": "object",
     "properties": {
       "success": {
         "type": "boolean",
         "description": "Indicates whether the operation was successful"
       },
       "values": {
         "type": "array",
         "description": "Array of extracted values from the matching elements",
         "items": {
           "type": "string"
         }
       },
       "count": {
         "type": "integer",
         "description": "Number of values extracted"
       }
     },
     "required": [
       "success",
       "values",
       "count"
     ]
   }
   ```

6. Define the default response (for errors) as in the previous operations.

#### 2.3.4. Find All Elements Operation

1. Click "New action"
2. Enter the following details:
   - Summary: `Find All Matching Elements`
   - Description: `Finds all HTML elements matching the specified tag name and optional attributes`
   - Operation ID: `FindAllElements`
   - Visibility: `important`

3. For the request:
   - Method: `POST`
   - Relative path: `/find-all`

4. Define the request body:
   ```json
   {
     "type": "object",
     "properties": {
       "operation": {
         "type": "string",
         "description": "Operation type (internal parameter)",
         "default": "find_all",
         "x-ms-visibility": "internal"
       },
       "html": {
         "type": "string",
         "description": "HTML content to be parsed",
         "x-ms-visibility": "important"
       },
       "tag_name": {
         "type": "string",
         "description": "HTML tag name to search for",
         "x-ms-visibility": "important"
       },
       "attributes": {
         "type": "object",
         "properties": {},
         "additionalProperties": true,
         "description": "Filter criteria for attributes",
         "x-ms-visibility": "advanced"
       }
     },
     "required": [
       "operation",
       "html",
       "tag_name"
     ]
   }
   ```

5. Define the response body (200 OK) with reference to the HtmlElement schema:
   ```json
   {
     "type": "object",
     "properties": {
       "success": {
         "type": "boolean",
         "description": "Indicates whether the operation was successful"
       },
       "elements": {
         "type": "array",
         "description": "Array of HTML elements that match the specified tag name and attributes",
         "items": {
           "$ref": "#/definitions/HtmlElement"
         }
       },
       "count": {
         "type": "integer",
         "description": "Number of elements found"
       }
     },
     "required": [
       "success",
       "elements",
       "count"
     ]
   }
   ```

6. Define the default response (for errors) as in the previous operations.

#### 2.3.5. Parse Table Operation

1. Click "New action"
2. Enter the following details:
   - Summary: `Parse HTML Table`
   - Description: `Parses an HTML table into structured data with headers and rows`
   - Operation ID: `ParseTable`
   - Visibility: `important`

3. For the request:
   - Method: `POST`
   - Relative path: `/parse-table`

4. Define the request body:
   ```json
   {
     "type": "object",
     "properties": {
       "operation": {
         "type": "string",
         "description": "Operation type (internal parameter)",
         "default": "parse_table",
         "x-ms-visibility": "internal"
       },
       "html": {
         "type": "string",
         "description": "HTML content containing the table",
         "x-ms-visibility": "important"
       },
       "table_selector": {
         "type": "string",
         "description": "CSS selector to locate the HTML table element",
         "default": "table",
         "x-ms-visibility": "advanced"
       },
       "header_rows_exist": {
         "type": "boolean",
         "description": "Whether the table has header rows",
         "default": true,
         "x-ms-visibility": "advanced"
       }
     },
     "required": [
       "operation",
       "html"
     ]
   }
   ```

5. Define the response body (200 OK):
   ```json
   {
     "type": "object",
     "properties": {
       "success": {
         "type": "boolean",
         "description": "Indicates whether the operation was successful"
       },
       "data": {
         "type": "object",
         "description": "Parsed table data structure",
         "properties": {
           "Headers": {
             "type": "array",
             "description": "Column headers extracted from the table",
             "items": {
               "type": "string"
             }
           },
           "Rows": {
             "type": "array",
             "description": "Table rows, each containing an array of cell values",
             "items": {
               "type": "array",
               "items": {
                 "type": "string"
               }
             }
           }
         },
         "required": [
           "Headers",
           "Rows"
         ]
       }
     },
     "required": [
       "success",
       "data"
     ]
   }
   ```

6. Define the default response (for errors) as in the previous operations.

### 2.4. Define the HtmlElement Schema

In the "Definitions" section, create a new definition for the HtmlElement schema:

```json
{
  "type": "object",
  "properties": {
    "tag": {
      "type": "string",
      "description": "The HTML tag name of the element (e.g., 'div', 'span', 'a')"
    },
    "outerHtml": {
      "type": "string",
      "description": "The complete HTML of the element including the element itself"
    },
    "innerHtml": {
      "type": "string",
      "description": "The HTML content inside the element, which may include other elements"
    },
    "innerText": {
      "type": "string",
      "description": "The text content inside the element with all HTML tags removed"
    },
    "attributes": {
      "type": "object",
      "additionalProperties": true,
      "description": "All attributes of the element as name-value pairs"
    },
    "isSelfClosing": {
      "type": "boolean",
      "description": "Indicates whether the element is a self-closing tag (e.g., <img>, <br>)"
    }
  },
  "required": [
    "tag",
    "outerHtml",
    "innerHtml",
    "innerText",
    "attributes",
    "isSelfClosing"
  ],
  "description": "Represents an HTML element with its properties and attributes"
}
```

### 2.5. Implementing the Custom Code

1. Go to the "Code" tab of your custom connector
2. Click "Edit code"
3. Delete any existing code
4. Copy and paste the entire contents of the `MiniSoup.cs` file
5. Click "Save"
6. Click "Validate" to check for any errors
7. If validation is successful, click "Update connector"

### 2.6. Testing the Connector

1. Go to the "Test" tab
2. Create a new connection (if prompted)
3. Test each operation individually:
   - For Fetch HTML: Enter a URL (e.g., "https://example.com")
   - For other operations: Use sample HTML and appropriate selectors

4. Verify the responses match the expected format and contain the expected data

### 2.7. Sharing the Connector

1. Go to the "General" tab
2. Under "Sharing", select the appropriate sharing options:
   - For organization-wide use: Share with specific users or groups
   - For personal use: No sharing required
3. Click "Update connector"

## 3. Flow Implementation Examples

### 3.1. Website Metadata Extraction Flow

This flow extracts Open Graph metadata from a website:

```
Trigger: Manual trigger (input: website URL)
↓
Action 1: MiniSoup HTML Parser - FetchHTML
  • URL: [Trigger input URL]
↓
Action 2: Initialize variable
  • Name: HTML
  • Type: String
  • Value: outputs('FetchHTML').html
↓
Action 3: MiniSoup HTML Parser - ExtractValues
  • HTML: variables('HTML')
  • Selector: meta[property='og:title']
  • Attribute: content
↓
Action 4: Initialize variable
  • Name: OGTitle
  • Type: String
  • Value: first(outputs('ExtractValues').values)
↓
Action 5: MiniSoup HTML Parser - ExtractValues
  • HTML: variables('HTML')
  • Selector: meta[property='og:description']
  • Attribute: content
↓
Action 6: Initialize variable
  • Name: OGDescription
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
  • Body:
    {
      "title": variables('OGTitle'),
      "description": variables('OGDescription'),
      "image": variables('OGImage')
    }
```

### 3.2. Price Monitoring Flow

This flow monitors an e-commerce site for price changes:

```
Trigger: Schedule (recurrence: daily at 9:00 AM)
↓
Action 1: MiniSoup HTML Parser - FetchHTML
  • URL: https://example-store.com/product/12345
↓
Action 2: MiniSoup HTML Parser - ExtractValues
  • HTML: outputs('FetchHTML').html
  • Selector: span.product-price
  • Attribute: text
↓
Action 3: Initialize variable
  • Name: CurrentPrice
  • Type: String
  • Value: replace(first(outputs('ExtractValues').values), '$', '')
↓
Action 4: Get row from SharePoint list
  • List: Price History
  • Filter: ProductID eq '12345'
↓
Condition: variables('CurrentPrice') != outputs('Get_row')['LastPrice']
  • If Yes:
    ↓
    Action 5: Update item in SharePoint list
      • List: Price History
      • ID: outputs('Get_row')['ID']
      • LastPrice: variables('CurrentPrice')
      • LastUpdated: utcNow()
    ↓
    Action 6: Send an email
      • To: your-email@example.com
      • Subject: Price Change Alert
      • Body:
        Price for Product #12345 has changed:
        Old price: $outputs('Get_row')['LastPrice']
        New price: $variables('CurrentPrice')
  
  • If No: (Do nothing)
```

### 3.3. News Headlines Collection Flow

This flow collects news headlines and posts them to a Teams channel:

```
Trigger: Schedule (recurrence: every hour)
↓
Action 1: MiniSoup HTML Parser - FetchHTML
  • URL: https://example-news.com
↓
Action 2: MiniSoup HTML Parser - SelectElements
  • HTML: outputs('FetchHTML').html
  • Selector: h2.article-title
↓
Action 3: Initialize variable
  • Name: HeadlinesList
  • Type: Array
↓
Action 4: Apply to each
  • Input: outputs('SelectElements').elements
  • Actions:
    ↓
    Action 4.1: Append to array variable
      • Name: HeadlinesList
      • Value: item().innerText
↓
Action 5: Post a message in Teams
  • Team: News Monitoring
  • Channel: Latest Headlines
  • Message:
    ## Today's Headlines (formatDateTime(utcNow(), 'yyyy-MM-dd HH:mm'))
    
    join(variables('HeadlinesList'), '\n\n')
```

### 3.4. Extracting Next-Head-Count Flow

This flow extracts the value of the "next-head-count" meta tag:

```
Trigger: Manual trigger (input: website URL)
↓
Action 1: MiniSoup HTML Parser - FetchHTML
  • URL: [Trigger input URL]
↓
Action 2: MiniSoup HTML Parser - ExtractValues
  • HTML: outputs('FetchHTML').html
  • Selector: meta[name='next-head-count']
  • Attribute: content
↓
Condition: outputs('ExtractValues').count > 0
  • If Yes:
    ↓
    Action 3: Respond to PowerApp or Flow
      • Status code: 200
      • Body:
        {
          "next-head-count": first(outputs('ExtractValues').values)
        }
  
  • If No:
    ↓
    Action 3: Respond to PowerApp or Flow
      • Status code: 404
      • Body:
        {
          "error": "next-head-count meta tag not found."
        }
```

## 4. Advanced Usage Strategies

### 4.1. Handling Large HTML Documents

When working with large HTML documents, consider these optimization techniques:

1. **Targeted Extraction**: Extract only the specific section of the HTML that contains your target content first, then perform detailed parsing on that smaller section.

   ```
   Action 1: MiniSoup HTML Parser - ExtractValues
     • HTML: [Full HTML content]
     • Selector: div#main-content
     • Attribute: html
   
   Action 2: MiniSoup HTML Parser - SelectElements
     • HTML: outputs('Extract_Main_Content').values[0]
     • Selector: [More specific selector]
   ```

2. **Preprocessing**: Remove scripts, styles, and comments before detailed parsing.

3. **Batched Processing**: Process large amounts of data in smaller batches to avoid timeouts.

### 4.2. Working with Authentication-Required Sites

For sites requiring authentication:

1. Use the standard HTTP action with authentication to fetch the page:
   ```
   Action 1: HTTP
     • Method: GET
     • URI: https://protected-site.com/page
     • Authentication: [Appropriate auth method]
   
   Action 2: MiniSoup HTML Parser - SelectElements
     • HTML: outputs('HTTP').body
     • Selector: [Your selector]
   ```

2. Remember to handle cookies or tokens if required for session management.

### 4.3. Error Handling Best Practices

Implement robust error handling in your flows:

1. **Check Success Status**: Always verify the `success` property in the response before using the results.

2. **Handle Empty Results**: Use conditions to check for empty arrays before processing results:
   ```
   Condition: length(outputs('SelectElements').elements) > 0
   ```

3. **Fallback Selectors**: Use multiple selectors in sequence if website structure might change:
   ```
   Action 1: MiniSoup HTML Parser - ExtractValues
     • HTML: [HTML content]
     • Selector: div.price-new
     • Attribute: text
   
   Condition: outputs('ExtractValues').count == 0
     • If Yes:
       ↓
       Action 2: MiniSoup HTML Parser - ExtractValues
         • HTML: [HTML content]
         • Selector: span.price
         • Attribute: text
   ```

4. **Notification on Failures**: Send alerts when parsing unexpectedly fails.

## 5. Performance Optimization

### 5.1. Minimizing API Calls

1. **Batch Processing**: Process multiple elements from a single HTML fetch rather than making multiple separate calls.

2. **Caching Responses**: Store fetched HTML in variables or SharePoint when appropriate to reduce duplicate calls.

3. **Selective Parsing**: Extract only what you need - use the `extract` operation for simple needs instead of selecting all elements.

### 5.2. Flow Execution Improvements

1. **Concurrent Branches**: Use parallel branches for independent processing paths.

2. **Scheduled Execution**: Schedule flows during off-peak hours for non-time-sensitive tasks.

3. **Throttling Consideration**: Implement delays between requests when making multiple calls to the same site.

## 6. Troubleshooting Common Issues

### 6.1. Element Not Found

If elements aren't being found despite appearing in the HTML:

1. **Check Selector Syntax**: Verify CSS selector or XPath syntax is correct.

2. **Inspect Dynamic Content**: The content might be generated by JavaScript after page load, which MiniSoup can't access directly.

3. **Try Alternative Selectors**: Use browser developer tools to find more reliable selectors.

4. **Check for Iframes**: Content might be in iframes that need to be accessed separately.

### 6.2. Incorrect Data Extracted

If data is found but incorrect:

1. **Check for Hidden Elements**: Some pages have hidden elements with the same selector.

2. **Examine Text Formatting**: Look for whitespace or special characters in the extracted text.

3. **Review Nested Elements**: The target content might be nested within other elements affecting the selection.

### 6.3. Performance Issues

If flows are slow or timing out:

1. **Reduce HTML Size**: Extract smaller sections before detailed parsing.

2. **Simplify Selectors**: Complex selectors take longer to process.

3. **Check for Redirects**: Sites with multiple redirects can slow down the fetch operation.

## 7. Security and Compliance Considerations

### 7.1. Respectful Web Scraping

1. **Rate Limiting**: Avoid excessive requests to the same site in short periods.

2. **Review Terms of Service**: Ensure scraping is allowed by the target website's terms.

3. **User Agent Configuration**: Use appropriate user agent strings and identify your bot when possible.

### 7.2. Data Privacy

1. **Sensitive Data Handling**: Be cautious when scraping, storing, or processing potentially sensitive information.

2. **Data Retention**: Implement appropriate retention policies for scraped data.

3. **Access Control**: Restrict access to flows that extract potentially sensitive information.

## 8. Conclusion

MiniSoup provides a powerful yet lightweight solution for HTML parsing within Power Automate. By following this implementation guide, you can effectively extract, analyze, and manipulate web content directly in your automation flows without external dependencies.

For advanced scenarios and customizations, refer to the project's GitHub repository for the latest updates and community contributions.