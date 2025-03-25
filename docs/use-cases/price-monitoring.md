---
layout: default
title: Competitive Price Monitoring
description: Using MiniSoup to track competitor prices across e-commerce websites
---

# Competitive Price Monitoring

## Business Need

In today's rapidly changing retail and e-commerce market, keeping track of competitors' pricing strategies is essential for maintaining competitiveness. Manually monitoring competitor prices is time-consuming, error-prone, and difficult to scale. An automated solution provides:

- Real-time visibility into market prices
- Data-driven pricing decisions
- Quick response to competitive price changes
- Comprehensive market coverage
- Reduced manual effort

## Implementation

The MiniSoup HTML Parser can be used to create an automated price monitoring system that regularly checks competitor websites and extracts current prices.

### Flow Architecture

![Price Monitoring Flow Architecture](../assets/images/price-monitoring-flow.png)

### Implementation Steps

#### 1. Create a Product Watchlist

First, create a SharePoint list or Excel file to maintain your product watchlist with the following columns:

- **ProductID**: Your internal product identifier
- **ProductName**: The name of your product
- **CompetitorURL**: URL of the competitor's product page
- **PriceSelector**: CSS selector for the price element (e.g., `span.price`, `div.product-price`)
- **LastPrice**: The last recorded price
- **LastUpdated**: Timestamp of the last update

#### 2. Create the Monitoring Flow

```
Trigger: Schedule (Daily at 6 AM)
↓
Action 1: Get Items from Product Watchlist
  • Data Source: SharePoint List / Excel Table
↓
Action 2: Initialize Variable
  • Name: Results
  • Type: Array
  • Value: []
↓
Action 3: Apply to Each (Loop through products)
  • Input: Value from Get Items
  ↓
  Action 3.1: MiniSoup HTML Parser - FetchHTML
    • URL: Current item's CompetitorURL
  ↓
  Action 3.2: MiniSoup HTML Parser - ExtractValues
    • HTML: Outputs from FetchHTML action
    • Selector: Current item's PriceSelector
    • Attribute: text
  ↓
  Condition: Extraction Successful?
    Check if body('MiniSoup_HTML_Parser_2').count > 0
   ├── Yes
   │   ↓
   │   Action 3.3: Parse Price Value
   │     • Text: first(body('MiniSoup_HTML_Parser_2').values)
   │     • Regular Expression: Replace non-numeric characters
   │     • Store in variable: CurrentPrice
   │   ↓
   │   Action 3.4: Append to Results Array
   │     • Add to Results: {
   │         "ProductID": Current item's ProductID,
   │         "ProductName": Current item's ProductName,
   │         "CompetitorURL": Current item's CompetitorURL,
   │         "PreviousPrice": Current item's LastPrice,
   │         "CurrentPrice": CurrentPrice variable,
   │         "PriceChange": Calculate difference,
   │         "PriceChangePercent": Calculate percentage,
   │         "Timestamp": utcNow()
   │       }
   │
   └── No
       ↓
       Action 3.5: Append Error to Results Array
       • Add to Results: Error information
↓
Action 4: Process Results
  • Filter for Price Changes: Filter where PreviousPrice != CurrentPrice
  • Generate Summary Statistics
↓
Action 5: Update Product Watchlist
  • Update LastPrice and LastUpdated for each monitored product
↓
Condition: Any Price Changes Detected?
 ├── Yes
 │   ↓
 │   Action 6: Generate Price Change Report
 │   • Create HTML table of price changes
 │   • Calculate summary statistics
 │   ↓
 │   Action 7: Send Notification Email
 │   • To: Pricing team email
 │   • Subject: Daily Price Monitoring Report: X price changes detected
 │   • Body: Price change report with link to detailed data
 │
 └── No
     ↓
     Action 8: Update Log
     • Record successful monitoring with no changes
```

### Price Extraction Techniques

Extracting prices from websites can be challenging due to varying formats. Here are some strategies:

#### 1. Multiple Selector Options

Some websites may have different price elements depending on sale status, availability, etc. Include multiple selectors in your configuration:

```
Action: MiniSoup HTML Parser - ExtractValues
  • HTML: [HTML content]
  • Selector: span.regular-price, span.special-price
  • Attribute: text
```

#### 2. Price Cleanup

Prices may include currency symbols, commas, spaces, and other characters. Use a regular expression to extract the numeric value:

```
Action: Compose
  • Inputs: replace(replace(replace(first(body('Extract_Values').values), '$', ''), ',', ''), ' ', '')
```

#### 3. Handling "Out of Stock" Scenarios

Create a condition to handle cases where the price is not available due to out-of-stock status:

```
Condition: contains(outputs('Extract_Values'), 'Out of stock')
```

## Sample Flow

You can download a sample flow template that implements this price monitoring solution:

[Download Price Monitoring Flow Template](../assets/templates/price-monitoring-flow.zip)

## Best Practices

### Ethical and Legal Considerations

- Review the terms of service of competitor websites before scraping
- Implement rate limiting to avoid overwhelming target websites
- Do not access non-public information or bypass security measures
- Consider using a dedicated IP address for scraping activities

### Technical Optimization

- **Rate Limiting**: Space out requests to avoid being blocked
- **Error Handling**: Implement robust error handling for site changes
- **Incremental Monitoring**: Monitor high-priority products more frequently
- **Proxy Rotation**: For large-scale monitoring, consider rotating IPs
- **Data Validation**: Implement checks to verify extracted prices are valid

### Business Process Integration

- Connect price monitoring data to pricing decision workflows
- Establish clear thresholds for automatic vs. manual price adjustments
- Integrate historical price data with sales performance metrics
- Create executive dashboards showing competitive positioning

## Real-World Results

Organizations implementing this solution have reported:

- 15-20% reduction in time spent on competitive research
- Ability to monitor 10x more competitor products
- Faster response to market changes (hours vs. days)
- More informed pricing decisions backed by comprehensive data
- Improved profit margins through optimized pricing

## Customization Options

### Extended Data Collection

Beyond price monitoring, this flow can be extended to collect:

- Product availability status
- Shipping costs and options
- Promotional offers and discounts
- Review counts and ratings
- Product specifications

### Advanced Analysis

Enhance the analysis phase with:

- Price trend visualization
- Competitor pricing strategy detection
- Market positioning analysis
- Price elasticity estimation
- Automatic pricing recommendations

## Related Resources

- [MiniSoup Extract Operation](../operations/extract) - Detailed documentation on extracting values
- [Error Handling Guide](../technical/error-handling) - Best practices for robust error handling
- [News Monitoring Use Case](news-extraction) - Similar approach for monitoring news content