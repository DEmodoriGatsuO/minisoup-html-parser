---
layout: default
title: MiniSoup Technical Documentation
description: Architecture, design principles, and technical details of the MiniSoup HTML Parser
---

# MiniSoup Technical Documentation

This section provides detailed technical information about the MiniSoup HTML Parser, including its architecture, design principles, implementation details, and performance characteristics.

## Design Philosophy

MiniSoup was designed with several core principles in mind:

1. **Practicality Over Completeness**: Focus on solving the most common HTML parsing needs rather than implementing a complete DOM parsing solution.

2. **Power Automate Constraints**: Optimized for the execution environment of Power Automate custom connectors, including code size limits, execution time limits, and available libraries.

3. **Business User Accessibility**: Simple API design that is approachable for Power Automate users without deep technical knowledge.

4. **Performance Efficiency**: Balanced approach to memory usage and execution speed to handle typical business scenarios effectively.

## Technical Documentation Sections

<div class="tech-grid">
  <div class="tech-card">
    <h3><a href="architecture">Architecture</a></h3>
    <p>High-level overview of MiniSoup's component architecture and design patterns</p>
    <ul>
      <li>Component structure</li>
      <li>Layer interactions</li>
      <li>Design decisions</li>
    </ul>
  </div>
  
  <div class="tech-card">
    <h3><a href="parsing-engine">HTML Parsing Engine</a></h3>
    <p>Detailed explanation of the HTML parsing approach and implementation</p>
    <ul>
      <li>Regex-based parsing strategy</li>
      <li>Element extraction algorithm</li>
      <li>Attribute parsing</li>
    </ul>
  </div>
  
  <div class="tech-card">
    <h3><a href="selectors">Selector System</a></h3>
    <p>How CSS selectors and XPath expressions are implemented and processed</p>
    <ul>
      <li>Supported selector patterns</li>
      <li>Selector parsing</li>
      <li>Element matching algorithm</li>
    </ul>
  </div>
  
  <div class="tech-card">
    <h3><a href="performance">Performance Optimization</a></h3>
    <p>Performance characteristics and optimization techniques</p>
    <ul>
      <li>Memory usage patterns</li>
      <li>Execution time analysis</li>
      <li>Optimization strategies</li>
    </ul>
  </div>
  
  <div class="tech-card">
    <h3><a href="error-handling">Error Handling</a></h3>
    <p>Approach to error detection, reporting, and resilience</p>
    <ul>
      <li>Error categories</li>
      <li>Recovery mechanisms</li>
      <li>Reporting patterns</li>
    </ul>
  </div>
  
  <div class="tech-card">
    <h3><a href="limitations">Limitations & Constraints</a></h3>
    <p>Known limitations and constraints of the current implementation</p>
    <ul>
      <li>Unsupported HTML features</li>
      <li>Selector limitations</li>
      <li>Performance boundaries</li>
    </ul>
  </div>
</div>

## Technical Implementation Overview

### HTML Element Model

MiniSoup represents HTML elements using a custom `HtmlElement` class that captures the essential properties:

```csharp
public class HtmlElement
{
    public string TagName { get; set; }
    public string OuterHtml { get; set; }
    public string InnerHtml { get; set; }
    public string InnerText { get; set; }
    public Dictionary<string, string> Attributes { get; }
    public bool IsSelfClosing { get; set; }
    
    // Methods for attribute access and JSON conversion
}
```

### Regex-Based Parsing Approach

Unlike traditional HTML parsers that build a complete DOM tree, MiniSoup uses a regex-based approach that:

- Processes HTML content sequentially
- Targets specific elements rather than parsing everything
- Uses pattern matching to identify elements and attributes
- Minimizes memory usage for large documents

This approach makes MiniSoup well-suited for the constraints of Power Automate custom connectors while still providing powerful HTML parsing capabilities.

### Core Parsing Algorithm

The core element extraction algorithm follows this pattern:

1. Define a regex pattern based on the tag name and other criteria
2. Find all matches in the HTML content
3. For each match, extract relevant components (tag name, attributes, inner content)
4. Parse attributes into name-value pairs
5. Create an `HtmlElement` instance for each match
6. Apply any additional filtering criteria

This approach allows for efficient extraction of elements without building a complete DOM tree.

## Architectural Decision Records

For key design decisions, we've provided detailed rationales in the Architecture section. These explain why specific approaches were chosen over alternatives, considering the constraints and requirements of the Power Automate environment.

## Future Development

The [Limitations & Roadmap](limitations) section outlines current limitations and potential areas for future enhancement, including:

- Expanded selector support
- Performance optimizations
- Additional parsing capabilities
- Integration with other Power Automate features

## Developer Reference

For developers looking to understand or extend MiniSoup, the technical documentation provides:

- Detailed implementation explanations
- Code structure and organization
- Key algorithms and data structures
- Performance characteristics and optimization opportunities

<style>
.tech-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 20px;
  margin: 30px 0;
}

.tech-card {
  background-color: #f5f5f5;
  border-radius: 5px;
  padding: 20px;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

.tech-card h3 {
  margin-top: 0;
  border-bottom: 1px solid #ddd;
  padding-bottom: 10px;
}

.tech-card ul {
  margin-bottom: 0;
  padding-left: 20px;
}

.tech-card li {
  margin-bottom: 5px;
}

code {
  background-color: #f0f0f0;
  padding: 2px 4px;
  border-radius: 3px;
  font-family: Consolas, Monaco, 'Andale Mono', monospace;
  font-size: 0.9em;
}
</style>