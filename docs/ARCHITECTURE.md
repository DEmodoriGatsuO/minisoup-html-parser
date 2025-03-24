### MiniSoup - Technical Architecture and Design Principles

## 1. Design Principles

MiniSoup is a lightweight library designed for effective HTML parsing within the constraints of Power Automate. Its design philosophy is centered on the following principles:

### 1.1. Practicality-First Approach

MiniSoup prioritizes practical use cases rather than replicating a full-fledged DOM parser like Beautiful Soup. It focuses on solving 80% of common business scenarios efficiently rather than covering every possible case.

### 1.2. Stepwise Parsing Approach

The HTML parsing process is divided into multiple stages, each focusing on a specific task. This modular approach allows efficient handling of large HTML documents, starting with element extraction followed by attribute and content analysis.

### 1.3. Adaptation to Environmental Constraints

Power Automate custom connectors have several limitations:
- Code size limit (below 1MB)
- Execution time limit (within 2 minutes)
- Restricted library usage
- Memory constraints

MiniSoup is designed to balance memory efficiency and execution speed while providing essential functionality.

---

## 2. Architectural Overview

MiniSoup follows a modular architecture with clearly defined layers.

### 2.1. High-Level Architecture

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

### 2.2. Key Components

#### 2.2.1. API Interface Layer

This layer processes requests from Power Automate, routes them to the appropriate operations, and returns standardized responses.

```csharp
public override async Task<HttpResponseMessage> ExecuteAsync()
{
    try
    {
        string contentAsString = await this.Context.Request.Content.ReadAsStringAsync().ConfigureAwait(false);
        var contentAsJson = JObject.Parse(contentAsString);
        
        string operation = (string)contentAsJson["operation"];
        
        switch (operation?.ToLower())
        {
            case "fetch_html":
                return await FetchHtmlOperation(contentAsJson);
            case "select":
                return await SelectOperation(contentAsJson);
        }
    }
    catch (Exception ex)
    {
        return CreateErrorResponse($"Error: {ex.Message}");
    }
}
```

#### 2.2.2. Operation Components

Each operation (`fetch_html`, `select`, `extract`, `find_all`, `parse_table`) is implemented independently, focusing on a specific functionality.

#### 2.2.3. Core HTML Parsing Engine

Handles:
- Preprocessing (comment removal, normalization)
- Element searching and extraction
- Selector interpretation and application
- Text extraction and cleaning

#### 2.2.4. Selector Engine

Interprets CSS selectors and XPath expressions to locate elements.

```csharp
private HtmlElement[] SelectElementsByCssSelector(string html, string cssSelector)
{
    if (string.IsNullOrEmpty(html) || string.IsNullOrEmpty(cssSelector))
        return new HtmlElement[0];
        
    if (cssSelector.StartsWith("#"))
    {
        string id = cssSelector.Substring(1);
        return FindAllElements(html, "*", new Dictionary<string, string> { { "id", id } });
    }
    else if (cssSelector.StartsWith("."))
    {
        string className = cssSelector.Substring(1);
        return FindElementsByClass(html, className);
    }
}
```

#### 2.2.5. HTML Element Model

Defines HTML elements, including tag name, attributes, and content.

```csharp
public class HtmlElement
{
    public string TagName { get; set; }
    public string OuterHtml { get; set; }
    public string InnerHtml { get; set; }
    public string InnerText { get; set; }
    public Dictionary<string, string> Attributes { get; } = new Dictionary<string, string>();
}
```

---

## 3. Implementation Details and Technical Choices

### 3.1. Why Regex-Based Parsing?

MiniSoup opts for regex-based parsing instead of a full DOM parser due to:

1. **Environmental Constraints**
   - Limited access to external libraries
   - Need for lightweight execution in a resource-constrained environment

2. **Performance Efficiency**
   - Avoids full DOM tree construction
   - Extracts only relevant parts for improved processing speed

3. **Targeted Use Case Coverage**
   - Many business cases require structured extraction rather than full DOM manipulation
   - Regex provides a lightweight, flexible approach

---

### 3.2. Key Algorithms and Data Structures

#### 3.2.1. Element Extraction Algorithm

```csharp
private HtmlElement[] FindAllElements(string html, string tagName, Dictionary<string, string> attributes)
{
    if (string.IsNullOrEmpty(html))
        return new HtmlElement[0];
        
    string tagPattern = tagName == "*" ? @"<([a-zA-Z][a-zA-Z0-9]*)([^>]*)(?:>(.*?)</\1>|/>)" : 
                                          $@"<{tagName}([^>]*)(?:>(.*?)</{tagName}>|/>)";
    
    var matches = Regex.Matches(html, tagPattern, RegexOptions.IgnoreCase | RegexOptions.Singleline);
    var elements = new List<HtmlElement>();
    
    foreach (Match match in matches)
    {
        // Parse and construct elements
    }
    
    return elements.ToArray();
}
```

#### 3.2.2. Attribute Parsing

```csharp
var attrMatches = Regex.Matches(tagAttributes, @"([a-zA-Z0-9_-]+)\s*=\s*[""']([^""']*)[""']");
foreach (Match attrMatch in attrMatches)
{
    string attrName = attrMatch.Groups[1].Value;
    string attrValue = attrMatch.Groups[2].Value;
    element.Attributes[attrName] = attrValue;
}
```

---

## 4. Limitations and Future Enhancements

### 4.1. Current Limitations

1. **Limited Selector Support**
   - Partial support for complex CSS selectors (`:nth-child`, `+`, `~`)
   - Only a subset of XPath expressions

2. **Assumptions About HTML Structure**
   - Works best with well-formed HTML
   - Deeply nested structures may impact accuracy

3. **Handling of Dynamic Content**
   - Cannot process JavaScript-generated elements
   - Parses only initial static HTML

4. **Processing Large HTML Files**
   - Performance issues with multi-megabyte documents
   - Memory constraints in large-scale parsing

### 4.2. Potential Future Enhancements

1. **Expanded Selector Support**
   - Broader CSS and XPath compatibility

2. **Domain-Specific Parsers**
   - JSON-LD, OpenGraph, and Microformats

3. **Performance Optimization**
   - Compiled regex for efficiency
   - Caching and parallel processing

4. **Industry-Specific Features**
   - E-commerce: Product price tracking
   - News: Article extraction
   - Job Listings: Structured job information extraction

---

## 5. Lessons Learned and Best Practices

### 5.1. Effective Design in Constrained Environments

1. **Focus on Core Features**
   - Covering essential business needs instead of building a full DOM parser

2. **Stepwise Processing**
   - Incremental parsing to improve efficiency

3. **Resilient Error Handling**
   - Graceful degradation when encountering unusual HTML structures

### 5.2. Regex-Based HTML Parsing Guidelines

1. **Balance Complexity**
   - Avoid overly complex regex patterns

2. **Handle Edge Cases**
   - Self-closing tags, attribute parsing, nested elements

### 5.3. Maintainable Code Design

1. **Clear Component Responsibilities**
   - Modular structure for maintainability

2. **Testability**
   - Functional approach to facilitate unit testing

3. **Scalability Considerations**
   - Extensible architecture to accommodate future enhancements

---

## Summary

MiniSoup is designed as a lightweight HTML parsing solution within the constraints of Power Automate. By leveraging regex-based parsing, it efficiently extracts and processes web data without external dependencies. Its architecture balances performance, memory usage, and practical use cases while maintaining extensibility for future improvements.