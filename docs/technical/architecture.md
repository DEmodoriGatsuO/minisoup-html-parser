---
layout: default
title: MiniSoup Architecture
description: Detailed information about the MiniSoup HTML Parser architecture and design principles
---

# MiniSoup Architecture

This document provides a comprehensive overview of the MiniSoup HTML Parser's architecture, design principles, and component structure.

## Design Principles

MiniSoup's architecture is guided by several key design principles that address the specific constraints and requirements of the Power Automate environment.

### 1. Practicality-First Approach

MiniSoup prioritizes practical use cases over theoretical completeness. Instead of implementing a full DOM parser with all possible capabilities, the library focuses on the HTML parsing tasks most commonly needed in business scenarios. This approach allows MiniSoup to:

- Deliver high value for common use cases
- Maintain a smaller codebase
- Operate efficiently within resource constraints
- Provide a simpler API for business users

### 2. Adaptation to Environmental Constraints

Power Automate custom connectors operate with several limitations:

- **Code Size Limit**: Custom connector code must remain below 1MB
- **Execution Time Limit**: Operations must complete within 2 minutes
- **Library Usage Restrictions**: Limited access to external libraries
- **Memory Constraints**: Efficient memory usage is essential

MiniSoup's architecture is specifically designed to work within these constraints while still providing powerful HTML parsing capabilities.

### 3. Stepwise Parsing Approach

MiniSoup employs a stepwise approach to HTML parsing, breaking the process into discrete operations:

1. Fetch HTML content
2. Select or find elements of interest
3. Extract specific information
4. Process structured data (like tables)

This approach allows for more efficient resource usage and better control over the parsing process.

### 4. Regex-Based Parsing Strategy

MiniSoup uses regular expressions for HTML parsing rather than building a complete DOM tree. This strategy:

- Reduces memory consumption for large documents
- Allows targeted extraction of specific elements
- Eliminates the need for external DOM parsing libraries
- Provides sufficient capabilities for most business use cases

## Architectural Overview

The MiniSoup architecture consists of well-defined layers that handle different aspects of HTML parsing:

```
┌─────────────────────────────────────────────────────┐
│ API Interface Layer (ExecuteAsync)                  │
├─────────────────────────────────────────────────────┤
│ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐     │
│ │ HTML Fetch  │ │ Element     │ │ Data        │     │
│ │ Operations  │ │ Operations  │ │ Operations  │     │
│ └─────────────┘ └─────────────┘ └─────────────┘     │
├─────────────────────────────────────────────────────┤
│ Core HTML Parsing Engine                            │
├─────────────────────────────────────────────────────┤
│ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐     │
│ │ CSS         │ │ XPath       │ │ Element     │     │
│ │ Selectors   │ │ Selectors   │ │ Finders     │     │
│ └─────────────┘ └─────────────┘ └─────────────┘     │
├─────────────────────────────────────────────────────┤
│ HTML Element Model                                  │
└─────────────────────────────────────────────────────┘
```

### Layer Details

#### 1. API Interface Layer

The API Interface Layer serves as the entry point for all operations. It:

- Receives HTTP requests from Power Automate
- Parses the request parameters
- Routes the request to the appropriate operation handler
- Formats and returns the response

The main component is the `ExecuteAsync` method that handles the initial request processing:

```csharp
public override async Task<HttpResponseMessage> ExecuteAsync()
{
    try
    {
        // Parse the request
        string contentAsString = await this.Context.Request.Content.ReadAsStringAsync().ConfigureAwait(false);
        var contentAsJson = JObject.Parse(contentAsString);
        
        // Get operation type
        string operation = (string)contentAsJson["operation"];
        
        // Route to appropriate operation handler
        switch (operation?.ToLower())
        {
            case "fetch_html":
                return await FetchHtmlOperation(contentAsJson);
            // Other operations...
        }
    }
    catch (Exception ex)
    {
        return CreateErrorResponse($"Error: {ex.Message}");
    }
}
```

#### 2. Operations Layer

The Operations Layer contains handlers for specific operations:

1. **HTML Fetch Operations**: Retrieve HTML content from URLs
   ```csharp
   private async Task<HttpResponseMessage> FetchHtmlOperation(JObject request)
   ```

2. **Element Operations**: Select and find HTML elements
   ```csharp
   private async Task<HttpResponseMessage> SelectOperation(JObject request)
   private async Task<HttpResponseMessage> FindAllOperation(JObject request)
   ```

3. **Data Operations**: Extract and process information
   ```csharp
   private async Task<HttpResponseMessage> ExtractOperation(JObject request)
   private async Task<HttpResponseMessage> ParseTableOperation(JObject request)
   ```

Each operation handler validates inputs, calls the appropriate core parsing functions, and formats the results.

#### 3. Core HTML Parsing Engine

The Core HTML Parsing Engine contains the central parsing functionality:

1. **HTML Preprocessing**: Normalize and clean HTML content
   ```csharp
   private string PreprocessHtml(string html)
   ```

2. **Element Finding**: Locate elements based on criteria
   ```csharp
   private List<HtmlElement> FindAllElements(string html, string tagName, Dictionary<string, string> attributes)
   ```

3. **Selector Processing**: Process CSS selectors and XPath expressions
   ```csharp
   private List<HtmlElement> SelectElementsByCssSelector(string html, string cssSelector)
   private List<HtmlElement> SelectElementsByXPath(string html, string xpath)
   ```

4. **Attribute Handling**: Parse and process element attributes
   ```csharp
   private Dictionary<string, string> ParseAttributes(string attributesString)
   ```

This layer does the heavy lifting of HTML parsing and is optimized for performance and memory efficiency.

#### 4. HTML Element Model

The HTML Element Model provides the data structures for representing HTML elements:

```csharp
public class HtmlElement
{
    public string TagName { get; set; }
    public string OuterHtml { get; set; }
    public string InnerHtml { get; set; }
    public string InnerText { get; set; }
    public Dictionary<string, string> Attributes { get; }
    public bool IsSelfClosing { get; set; }
    
    public string GetAttribute(string name) { ... }
    public JObject ToJson() { ... }
}

public class TableData
{
    public List<string> Headers { get; }
    public List<List<string>> Rows { get; }
}
```

These classes encapsulate the essential properties and behaviors of HTML elements and provide methods for accessing attributes and converting to JSON.

## Component Interactions

The interactions between components follow a clear pattern:

1. The API Interface Layer receives a request and routes it to the appropriate operation handler
2. The operation handler validates inputs and calls the Core HTML Parsing Engine
3. The Core HTML Parsing Engine processes the HTML and returns HtmlElement instances
4. The operation handler formats the results and returns them to the caller

This separation of concerns allows for easier maintenance, testing, and optimization of each component.

## Data Flow

A typical data flow through the system looks like this:

1. **Input**: A request comes in with operation parameters (HTML content, selectors, etc.)
2. **Preprocessing**: The HTML content is normalized and prepared for parsing
3. **Parsing**: The appropriate parsing functions are applied to extract elements
4. **Element Creation**: HtmlElement instances are created for matching elements
5. **Filtering**: Elements may be further filtered based on additional criteria
6. **Data Extraction**: Specific data is extracted from the elements as needed
7. **Response Generation**: Results are formatted and returned to the caller

## Design Decisions

### Why Regex-Based Parsing?

MiniSoup uses regex-based parsing instead of a full DOM parser for several reasons:

1. **No External Dependencies**: Power Automate custom connectors have limited access to external libraries
2. **Memory Efficiency**: Regex-based parsing can be more memory-efficient for large documents
3. **Performance**: For targeted extraction, regex can be faster than building a full DOM tree
4. **Simplicity**: The implementation is simpler and easier to maintain

### Element Finding Strategy

MiniSoup uses a direct string manipulation approach for finding elements:

1. Search for opening tags matching the criteria
2. Find the corresponding closing tag
3. Extract the full tag and its contents
4. Parse attributes and create an HtmlElement

This approach avoids recursive parsing of the entire document and allows for more efficient memory usage.

### CSS Selector Implementation

The CSS selector implementation focuses on the most commonly used selectors:

- Element selectors (`div`, `span`, etc.)
- ID selectors (`#header`, `#content`, etc.)
- Class selectors (`.product`, `.menu`, etc.)
- Attribute selectors (`[type="text"]`, `[href]`, etc.)

More complex selectors like pseudo-classes, combinators, and hierarchical selectors are not fully supported due to complexity and performance considerations.

### Error Handling Strategy

MiniSoup employs a robust error handling strategy:

1. Catch and report exceptions at the operation level
2. Provide detailed error messages to help diagnose issues
3. Use try-catch blocks in critical parsing sections to prevent total failure
4. Include fallback mechanisms for attribute parsing and other error-prone areas

## Architecture Evolution

The MiniSoup architecture has evolved based on practical experience and real-world usage patterns:

1. **Initial Design**: Simple regex-based parsing with basic CSS selector support
2. **Enhanced Robustness**: Improved error handling and recovery mechanisms
3. **Performance Optimization**: Refined algorithms for better performance
4. **Extended Capabilities**: Added XPath support and table parsing

Future directions include:

1. **Performance Enhancements**: Further optimization for large documents
2. **Extended Selector Support**: More comprehensive CSS selector support
3. **Specialized Parsers**: Domain-specific parsing capabilities for e-commerce, news, etc.

## Architectural Constraints

The MiniSoup architecture operates within several constraints:

1. **Power Automate Limitations**: Code size, execution time, and library access restrictions
2. **Regex Limitations**: Some complex HTML patterns may be challenging to parse reliably
3. **Memory Efficiency**: Need to balance memory usage with parsing capabilities
4. **Performance Boundaries**: Complex operations on large documents may approach time limits

## Conclusion

The MiniSoup architecture represents a pragmatic approach to HTML parsing within the constraints of the Power Automate environment. By focusing on the most common use cases and employing efficient parsing strategies, MiniSoup provides powerful HTML parsing capabilities without requiring external services or complex dependencies.

<style>
pre {
  background-color: #f5f5f5;
  padding: 15px;
  border-radius: 5px;
  overflow-x: auto;
}

code {
  font-family: Consolas, Monaco, 'Andale Mono', monospace;
}
</style>