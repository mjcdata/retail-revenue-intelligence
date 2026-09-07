# Business Analysis

This analysis examines revenue, product, geographic, and customer performance within the cleaned Online Retail II dataset. SQL was used to answer eight business questions and identify key patterns in retail performance.
Revenue Analysis

### Business Question 1: How has revenue changed over time?

Monthly net revenue shows a recurring seasonal pattern, with performance increasing substantially from September through November in both 2010 and 2011.
November was the strongest complete month in each year, reaching approximately $1.42 million in 2010 and $1.46 million in 2011.
Revenue then declined following the peak period.
December 2011 is a partial month containing transactions only through December 9 and should not be directly compared with complete months.

### Business Question 2: What periods generate the highest and lowest revenue?

November 2011 generated the highest monthly net revenue at approximately $1.46 million, followed closely by November 2010 at approximately $1.42 million.
Four of the five highest revenue months occurred during September through November, reinforcing the strong fall seasonal pattern observed in the revenue trend analysis.
Among complete months, April 2011 generated the lowest net revenue at approximately $493,000, followed by February 2011 at approximately $498,000.
December 2011 produced the lowest reported monthly revenue at approximately $434,000, but it contains transactions only through December 9 and should not be treated as the lowest performing complete month.

## Product Analysis
### Business Question 3: Which products generate the most revenue?

REGENCY CAKESTAND 3 TIER generated the highest net revenue at approximately $314,500.
WHITE HANGING HEART T-LIGHT HOLDER ranked second at approximately $248,700, followed by JUMBO BAG RED RETROSPOT at approximately $178,700.
Revenue was concentrated among the highest performing products, with the top two products generating substantially more revenue than the remaining products in the top 10.

### Business Question 4: Which products have the highest sales volume?

WORLD WAR 2 GLIDERS ASSTD DESIGNS had the highest sales volume at approximately 106,400 units sold.
JUMBO BAG RED RETROSPOT ranked second at approximately 96,900 units, followed by PACK OF 72 RETROSPOT CAKE CASES at approximately 95,000 units.
Several high volume products also appeared among the top revenue generating products, including JUMBO BAG RED RETROSPOT, WHITE HANGING HEART T-LIGHT HOLDER, SMALL POPCORN HOLDER, and ASSORTED COLOUR BIRD ORNAMENT.
The highest volume product was not the highest revenue product, showing that unit volume and revenue contribution are not necessarily the same.

## Geographic Analysis
### Business Question 5: Which countries generate the most revenue?

The United Kingdom generated the most net revenue by a substantial margin at approximately $16.19 million.
EIRE ranked second at approximately $610,000, followed by the Netherlands at approximately $548,000.
Germany and France also represented major non UK markets, generating approximately $413,000 and $322,000 in net revenue respectively.
Revenue was heavily concentrated in the United Kingdom, while international revenue was distributed across a much smaller set of markets.

### Business Question 6: How does revenue performance outside the United Kingdom compare across markets?

EIRE generated the highest net revenue outside the United Kingdom at approximately $610,000, supported by 626 orders and an average order value of approximately $1,053.
The Netherlands generated approximately $548,000 from only 228 orders but had the highest average order value among the leading international markets at approximately $2,430.
Germany generated approximately $413,000 across 789 orders, the highest order count among the top international markets, but had a substantially lower average order value of approximately $539.
International markets therefore show different revenue patterns, with some markets driven by higher order volume and others by larger average order values.

## Customer Analysis
### Business Question 7: Which identified customers generate the most revenue?
Customer level net revenue was analyzed to identify the known customers contributing the most revenue to the business.
Solution
Customer 18102 generated the highest net revenue at approximately $570,400.
Customer 14646 ranked second at approximately $523,300, followed by Customer 14156 at approximately $296,200.
The top two customers generated substantially more net revenue than the rest of the top 10, indicating that a relatively small number of identified customers contributed a large amount of revenue.
Customer level analysis is based only on transactions with a populated Customer ID, so unidentified customers are excluded from this ranking.

### Business Question 8: How does purchasing behavior vary across identified customers?

The highest value customers reached similar revenue levels through different purchasing behaviors, demonstrating that revenue contribution is driven by both order frequency and order size.
Customer 14911 placed the most orders among the top customers with 398 orders, but had a relatively lower average order value of approximately $733.
In contrast, Customer 12415 placed only 28 orders but had the highest average order value among the top customers at approximately $5,159.
Customers 18102 and 14646 combined relatively high order frequency with large average order values, resulting in the two highest net revenue totals among identified customers.

