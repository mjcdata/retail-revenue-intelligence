# Data Quality & Cleaning Log

## 1. Purpose
The purpose of this document is to assess the data quality of the raw [Online Retail II dataset.](https://archive.ics.uci.edu/dataset/502/online%2Bretail) by identifying missing values, duplicate records, inconsistent values, and other potential data quality issues before analysis. This log also documents how identified issues are addressed and the rationale behind each cleaning decision to ensure the resulting dataset is reliable and appropriate for analysis.

## 2. Dataset Overview
Source Dataset: [Online Retail II dataset.](https://archive.ics.uci.edu/dataset/502/online%2Bretail)

### Worksheets:
* Year 2009-2010
* Year 2010-2011

### Row Counts:
* Year 2009-2010: 525,461 rows
* Year 2010-2011: 541,910 rows
* Combined: 1,067,371 rows

### Dataset Grain: 
One row represents one product line within an invoice/order. A single invoice may therefore appear across multiple rows when the order contains multiple product lines.

### Raw Fields: 
Invoice, StockCode, Description, Quantity, InvoiceDate, Price, Customer ID, and Country.

### Derived Fields: 
Source_Sheet, Transaction_Type, Activity_Type, Line_Revenue, Revenue_Eligible, and Analytical_Revenue. Line_Revenue is calculated as Quantity × Price, while Analytical_Revenue applies the documented revenue eligibility rules used for downstream analysis.

## 3. Data Quality Assessment
#### 3.1 Missing Values
Check performed: 
Reviewed each raw field across both worksheets to identify missing/null values and calculate the number and percentage of affected records.

Findings: 
Missing values were identified only in Description and Customer ID. Description was missing from 4,382 of 1,067,371 records (approximately 0.41%), while Customer ID was missing from 243,007 records (approximately 22.77%). No missing values were identified in Invoice, StockCode, Quantity, InvoiceDate, Price, or Country.

Impact: 
Missing Description values may limit product level reporting based on product names, although StockCode remains available as the product identifier. Missing Customer ID values have a more significant analytical impact because those transactions cannot be reliably associated with an individual customer, limiting customer level metrics such as purchase frequency, retention, and customer value.

Decision / treatment: 
Description: Keep records with missing descriptions. StockCode remains available for product identification, and Description isn't required for revenue calculations.
Customer ID: Keep records with missing Customer IDs in the analysis ready dataset. Exclude them only from customer level analyses requiring a known customer.
Other fields: No treatment required because no missing values were found.

       

#### 3.2 Duplicate Records
Check performed:
Compare records across all eight raw fields to identify exact duplicate rows within each worksheet.

Findings: 
The dataset contains 12,133 records (1.14%) that duplicate a previous record across all available fields. Inspection shows multiple identical transaction lines within the same invoices, including repeated combinations of invoice number, stock code, quantity, price, customer, and timestamp.

Impact: 
If these records are true data duplicates, retaining them could overstate product quantities, transaction activity, and revenue in downstream analysis. However, the dataset does not include a unique transaction line identifier, so identical rows cannot be definitively classified as erroneous based on the available fields alone.

Decision / treatment: 
Retain within source exact duplicate records because the dataset does not include a unique transaction line identifier that would allow them to be confirmed as erroneous. Cross sheet duplication from the overlapping December 1–9, 2010 period is handled separately during cleaning.


#### 3.3 Quantity Validation
Check performed:
Evaluated the Quantity field for negative and zero values and investigated whether negative quantities were associated with identifiable transaction patterns.

Findings: 
22,950 records have negative quantities, while 0 records have a quantity of zero.
19,493 negative quantity records have invoice numbers beginning with C, indicating a distinct transaction pattern consistent with cancellations or returns.
The remaining 3,457 negative quantity records have numeric invoice numbers.
All 3,457 of these records have a price of 0 and a missing Customer ID.
Inspection of their descriptions identified terms such as lost, damaged, wet, and short, along with other apparent product or inventory corrections. This pattern is consistent with internal inventory adjustments rather than customer transactions.

Impact:
Negative quantities should not automatically be treated as invalid data. They appear to represent multiple business processes, including customer cancellations/returns and internal inventory adjustments. Treating all negative quantities as ordinary sales would distort unit and revenue metrics, while simply removing all negative quantities could eliminate legitimate return activity needed to calculate net revenue.

Decision / treatment: 
Retain negative quantity records during profiling. During data cleaning and transformation, distinguish customer cancellation/return activity from apparent internal inventory adjustments. Customer related negative transactions should be preserved where appropriate for net revenue analysis, while non customer inventory adjustments should be excluded from customer sales and revenue metrics.

   
#### 3.4 Price Validation
Check performed:
Evaluated the Price field for negative and zero values and investigated whether non positive prices were associated with identifiable transaction patterns.

Findings: 
5 records have negative prices.
All 5 negative price records have a StockCode of B, a description of Adjust bad debt, a quantity of 1, and a missing Customer ID, indicating that these records represent bad debt adjustments rather than product sales.
6,202 records have a price of zero.
Of the zero price records, 3,457 have negative quantities and 2,745 have positive quantities.
6,131 of 6,202 zero price records have a missing Customer ID, while only 71 are associated with an identified customer.
4,382 zero price records also have a missing description.
The 3,457 negative quantity, zero price records correspond to the inventory adjustment pattern identified during Quantity Validation.
A small number of zero price records are associated with identified customers and recognizable products. The available data does not establish whether these represent free items, promotions, manual adjustments, missing prices, or another business process.


Impact:
Negative price bad debt adjustments are accounting activity rather than product revenue and could distort sales metrics if treated as normal transactions. Zero price records do not contribute revenue, but including them in product quantities, transaction counts, or customer purchasing metrics could misrepresent actual sales activity. Because zero price records represent multiple patterns, they should not all be assigned the same business meaning.

Decision / treatment: 
Exclude identified bad debt adjustments and apparent non customer inventory adjustments from product sales and revenue analysis. Zero price customer associated transactions should be retained as a separately identifiable condition until their analytical treatment is determined. Do not impute or replace zero prices because the dataset does not provide sufficient information to determine what the missing or intended price should have been.
   
#### 3.5 Invoice / Cancellation Validation
Check performed:
Evaluated invoice number formats and prefixes to determine whether different invoice patterns correspond to distinct types of transaction activity.

Findings:
The dataset contains 53,628 distinct invoices.
45,330 distinct invoices are numeric and represent the standard invoice format.
8,292 distinct invoices begin with C, accounting for 19,494 transaction lines.
Of the 19,494 C-prefixed transaction lines, 19,493 have negative quantities. The single exception is a manual transaction with a positive quantity, indicating that the C-prefix pattern is highly consistent but not absolute.
6 distinct invoices begin with A.
All 6 A-prefixed invoices are identified as Adjust bad debt, use StockCode B, have a quantity of 1, have no Customer ID, and include both positive and negative price adjustments.
No additional invoice prefix patterns were identified.


Impact:
Invoice prefixes contain meaningful information about transaction type. Treating all invoice records as standard customer sales would mix normal transactions with cancellation/return activity and bad debt accounting adjustments, potentially distorting revenue, order, quantity, and customer metrics.
Decision / treatment:
Use invoice patterns as one component of transaction classification during data cleaning and transformation. Numeric invoices will generally represent standard transaction activity, C-prefixed invoices will be treated as cancellation/return activity subject to the identified exception, and A-prefixed invoices will be classified as bad debt adjustments and excluded from product sales analysis. Classification rules should account for supporting fields such as quantity, price, StockCode, and description rather than relying solely on invoice prefix.

  
#### 3.6 Product / StockCode Validation
Check performed:
Examined StockCode values for unusual formats and investigated non numeric codes to determine whether the field contains transaction types other than standard merchandise products.

Findings:
StockCode contains both standard product identifiers and special transaction codes.
Non product codes identified through their associated descriptions include POST (postage), DOT (dotcom postage), M/m (manual entries), D (discounts), S (samples), BANK CHARGES, ADJUST (adjustments), AMAZONFEE, CRUK (commission), and B (bad debt adjustments).
These identified special transaction codes account for 5,509 records, or approximately 0.52% of the dataset.
Not all non numeric StockCodes represent non product activity. Codes such as DCGSSGIRL, DCGSSBOY, and PADS are associated with merchandise descriptions, demonstrating that StockCode format alone cannot reliably distinguish products from non product transactions.


Impact:
Treating every StockCode as a merchandise product could cause postage, discounts, fees, commissions, manual entries, and accounting adjustments to be included in product level sales metrics. Conversely, excluding all non numeric StockCodes would incorrectly remove some legitimate merchandise.

Decision / treatment: 
Create an explicit classification for identified special transaction codes during data cleaning rather than excluding records based solely on StockCode format. Non product transaction types will be separated from merchandise where appropriate for product level analysis, while legitimate merchandise codes will be retained.


#### 3.7 InvoiceDate Validation
Check performed:
Evaluated InvoiceDate to confirm the dataset's date range, verify expected coverage across both source periods, and identify any records with dates outside the expected timeframe.

Findings: 
The combined dataset covers transactions from December 1, 2009 through December 9, 2011. The source sheets overlap from December 1–9, 2010, with each sheet containing 22,523 rows during this period.

After excluding Source_Sheet from the comparison and removing repeated records within each sheet, both source sheets contain 22,202 unique transaction records during the overlapping period. A cross sheet comparison confirmed that all 22,202 unique records appear in both sheets, with no records exclusive to either source.

Impact:
Combining the two source sheets without accounting for the overlapping period introduces duplicate transaction data. If these cross sheet duplicates remain in the analytical dataset, transaction counts, quantities, revenue, and other downstream metrics could be overstated.
The overlap also contains repeated transaction lines within the individual source sheets, which should remain distinguishable from duplication introduced by combining the two sheets.

Decision / treatment: 
Retain the original records during profiling. During data cleaning, remove the duplicate copy of the December 1–9, 2010 cross sheet overlap so that each source transaction is represented only once. Handle within source exact duplicates separately according to the duplicate record treatment established during duplicate analysis.
After resolving the overlap, use the resulting transaction dates as the basis for monthly, seasonal, and other time based revenue analysis.


#### 3.8 Customer ID Validation
Check performed:
Evaluated missing Customer ID values to determine which transaction types are associated with unidentified customers and whether missing Customer IDs occur within otherwise normal customer sales activity.

Findings: 
The dataset contains 243,007 records with a missing Customer ID, representing 22.77% of all records.
Of these records, 236,122 (97.17%) have both positive quantities and positive prices and represent 3,109 distinct invoices. 
Inspection of these records shows standard numeric invoices, recognizable merchandise descriptions, and normal positive quantities and prices, indicating that the majority of missing Customer IDs occur within otherwise valid sales activity.
A smaller portion of missing Customer IDs is associated with previously identified non standard transaction activity, including inventory adjustments, zero price transactions, and bad debt adjustments.

Impact:
Removing all records with missing Customer IDs would eliminate a substantial amount of otherwise valid sales activity and could materially understate revenue, quantities sold, and transaction activity.
However, transactions without a Customer ID cannot reliably contribute to customer level metrics such as unique customer counts, purchase frequency, repeat customer analysis, or customer level revenue.


Decision / treatment: 
Retain otherwise valid sales transactions with missing Customer IDs for overall revenue, product, geographic, and transaction level analysis.
Exclude records without a Customer ID from analyses that require an identifiable customer. Do not impute or create Customer IDs because the dataset does not provide sufficient information to reliably determine the missing customer identities.
Previously identified non sale and adjustment records with missing Customer IDs will continue to be handled according to their respective transaction classifications during cleaning.


#### 3.9 Description Validation
Check performed:
Evaluated missing and inconsistent Description values to determine whether missing descriptions are associated with identifiable non sale transaction patterns and whether individual StockCodes are associated with multiple product descriptions.

Findings:
The dataset contains 4,382 records with a missing Description, representing 0.41% of all records. 
All 4,382 missing description records have a Price of 0 and a missing Customer ID. 
Of these records, 2,689 have negative quantities and 1,693 have positive quantities, indicating that missing descriptions are concentrated within non standard transaction activity rather than normal revenue generating sales.
Additionally, 1,232 StockCodes are associated with more than one distinct Description. Inspection of several high variation StockCodes shows that the additional descriptions frequently represent operational notes, corrections, inventory adjustments, damage comments, or status notes rather than different product identities. 
Examples include descriptions such as damaged, faulty, amazon adjustment, wrongly coded, and missing appearing alongside valid product descriptions for the same StockCode.


Impact:
Using Description alone as a product identifier could create inconsistent product groupings because operational notes and correction comments are stored in the same field as product names. This could fragment product level metrics, create duplicate product labels, and reduce the reliability of product reporting.
Missing descriptions are unlikely to materially affect standard sales analysis because the missing values occur only on zero price records with unidentified customers, but they may still be relevant when classifying inventory and adjustment activity.

Decision / treatment: 
Use StockCode as the primary product identifier for product level analysis rather than Description.
During cleaning, preserve valid product descriptions where available while treating operational notes, correction comments, and adjustment descriptions as transaction context rather than separate product identities. Missing Description values will not be imputed because the available data does not always provide sufficient evidence to determine the intended product description.
Non sale and adjustment records with missing or operational descriptions will continue to be handled according to their transaction classification during cleaning.


#### 3.10 Country Validation
Check performed:
Evaluated Country values for missing data, inconsistent naming, unusual categories, and the overall distribution of transaction activity across countries.

Findings: 
The Country field contains 43 distinct values with no missing records.
The United Kingdom accounts for 981,330 records, representing 91.94% of the dataset, while all non UK markets collectively account for 86,041 records, or 8.06%.
A small number of geographic values use alternative or broader labels rather than standard country names, including EIRE, RSA, European Community, Channel Islands, and West Indies. Additionally, 756 records (0.07%) are classified as Unspecified.
No obvious duplicate country categories caused by spelling or capitalization differences were identified during the review.

Impact:
Country can be used for geographic analysis, but the dataset is heavily concentrated in the United Kingdom. Comparisons based on raw transaction or revenue totals will therefore be dominated by UK activity and should be interpreted with this distribution in mind.

Non standard geographic labels may also require consistent treatment if standardized country level reporting is needed. The small number of Unspecified records cannot be reliably assigned to a specific market using the available Country field.

Decision / treatment: 
Retain the existing Country values in the analysis ready dataset, including Unspecified. No geographic labels are reassigned without sufficient evidence, and the existing categories are preserved for downstream geographic analysis.


#### 3.11 Cross Field Consistency Check
Check performed:
Evaluated relationships between key transaction fields—including Invoice, Quantity, Price, Customer ID, StockCode, and Description—to identify records that do not follow the transaction patterns established during individual field validation.

Findings: 
Cross field analysis identified several consistent transaction patterns across the dataset.
C-prefixed invoices are strongly associated with negative quantities, supporting their classification as cancellation or return activity. Only one C-prefixed record has a non negative quantity: a Manual transaction with Quantity = 1, Price = 373.57, and no Customer ID.
All six A-prefixed records consistently use StockCode = B, Description = Adjust bad debt, Quantity = 1, and have no Customer ID. These records include both positive and negative prices and represent a distinct accounting adjustment pattern.
Previous field validations also identified consistent relationships between 
numeric invoices with negative quantities, zero prices, and missing Customer IDs; 
missing descriptions with zero prices and missing Customer IDs; 
and specific StockCodes with identifiable non merchandise transaction activity.
At the same time, individual fields are not sufficient to classify every transaction. For example, missing Customer IDs also occur extensively within otherwise normal positive price, positive quantity sales activity.

Impact:
Transaction type cannot be reliably determined from a single field or condition. 
Classifying records based only on Invoice prefix, Customer ID availability, Quantity, Price, or Description could incorrectly exclude legitimate sales or include non sale activity in revenue metrics.

Decision / treatment: 
Use combinations of transaction attributes to classify records during cleaning rather than relying on individual fields alone.
Invoice prefix, Quantity, Price, Customer ID, StockCode, and Description will be used together where appropriate to distinguish standard sales, cancellations or returns, inventory adjustments, accounting adjustments, and other non merchandise transaction activity.
Document identified exceptions separately so that classification rules remain transparent and reproducible.


## 4. Data Profiling Summary
The combined Online Retail II dataset was profiled to evaluate its structure, completeness, validity, consistency, and suitability for downstream revenue analysis. Profiling identified several distinct transaction patterns and data quality conditions that require documented treatment during the cleaning stage.

## Key findings include:

* Duplicate records: Exact duplicate transaction lines exist within the dataset. In addition, the two source worksheets overlap from December 1–9, 2010. Cross sheet comparison confirmed that 22,202 unique transaction records from this period appear in both source sheets, creating duplication when the worksheets are combined.
* Quantity: The dataset contains 22,950 records with negative quantities and no zero quantity records. Most negative quantities are associated with C-prefixed invoices, while a separate group of negative quantity records with numeric invoices, zero prices, missing Customer IDs, and operational descriptions is consistent with inventory adjustments.
* Price: Five records contain negative prices and are associated with A-prefixed bad debt adjustments. Zero price records represent a mixture of inventory adjustments, records with missing descriptions, and a smaller number of customer associated transactions requiring separate treatment.
* Invoice: Invoice prefixes provide meaningful transaction context. Numeric invoices generally represent standard transaction activity, C-prefixed invoices are strongly associated with cancellations or returns, and A-prefixed invoices represent bad debt adjustments. One C-prefixed manual transaction was identified as an exception to the normal negative quantity pattern.
* StockCode: StockCode contains both merchandise identifiers and special transaction codes representing postage, discounts, samples, bank charges, commissions, manual entries, accounting adjustments, and other non merchandise activity. StockCode values are also stored using mixed Python data types and should be standardized before transformation.
* InvoiceDate: The dataset covers December 1, 2009 through December 9, 2011. The source worksheets contain an overlapping period from December 1–9, 2010, requiring cross sheet deduplication before time based analysis.
* Customer ID: 243,007 records (22.77%) have a missing Customer ID. However, 236,122 of these records have both positive quantities and positive prices and represent 3,109 distinct invoices. Missing Customer IDs therefore do not necessarily indicate invalid transactions, although these records cannot support analyses requiring an identifiable customer.
* Description: 4,382 records (0.41%) have missing descriptions; all have zero prices and missing Customer IDs. Additionally, 1,232 StockCodes are associated with multiple descriptions. Inspection indicates that the Description field contains both product names and operational notes such as damage, inventory, correction, and adjustment comments.
* Country: Country contains no missing values and includes 43 distinct geographic labels. The dataset is heavily concentrated in the United Kingdom, which accounts for 91.94% of all records. A small number of non standard or broader geographic labels are also present, including Unspecified.
* Cross field consistency: Transaction classifications cannot be determined reliably from any single field. Relationships between Invoice, Quantity, Price, Customer ID, StockCode, and Description provide stronger evidence for distinguishing standard sales, cancellations or returns, inventory adjustments, accounting adjustments, and other non merchandise activity.

Overall, the dataset is suitable for revenue analysis, but cleaning and transaction classification are required before reliable business metrics can be calculated. The profiling findings provide the basis for defining those cleaning requirements while preserving legitimate sales and return activity.

## 5. Cleaning Requirements
The profiling process identified several conditions that require treatment before the dataset can be used for reliable revenue analysis. 

The following requirements translate those findings into specific cleaning and transformation actions:


| Profiling Finding | Cleaning Requirement |
|---|---|
| Source_Sheet shows duplicate transaction coverage from December 1–9, 2010. | Remove the redundant cross sheet records while retaining one copy of each transaction. |
| Exact duplicate rows exist within individual Source_Sheet values. | Retain within source exact duplicates because no unique transaction line identifier exists to confirm they are erroneous. |
| Negative Quantity values represent multiple transaction types. | Classify negative Quantity records before determining whether they should contribute to sales, returns, or inventory activity. |
| Invoice values beginning with C are strongly associated with cancellations/returns, with one documented exception. | Classify `Invoice LIKE 'C%'` as cancellation/return activity while accounting for the identified exception. |
| Numeric Invoice records with negative Quantity, Price = 0, and missing Customer ID are consistent with inventory adjustments. | Classify these records as inventory adjustments and exclude them from customer sales and revenue metrics. |
| Invoice values beginning with A represent bad debt adjustments. | Classify A-prefixed invoices as bad debt adjustments and exclude these records from product sales and revenue metrics. |
| Negative Price values occur within the identified bad debt records. | Preserve the original Price values and handle them through the bad debt classification rather than correcting the values. |
| Price = 0 occurs across several transaction types. | Do not automatically remove or impute zero Price values. Determine treatment from the transaction classification. |
| StockCode contains both merchandise and special transaction codes. | Explicitly classify known special StockCode values using documented classification logic rather than treating all non numeric codes as non merchandise. |
| StockCode contains mixed integer and string values. | Standardize StockCode as a string using Python `.astype(str)`. |
| Missing Customer ID values occur extensively within otherwise valid sales. | Retain valid sales with missing Customer ID, but exclude them from metrics requiring an identifiable customer. Do not impute IDs. |
| Missing Description values occur only with Price = 0 and missing Customer ID. | Do not impute missing Description values; determine treatment through transaction classification. |
| Individual StockCode values can have multiple Description values, including operational notes. | Use StockCode as the primary product identifier and avoid using Description alone for product aggregation. |
| Country contains non standard geographic labels and Unspecified values. | Standardize Country only where a defensible mapping exists; retain Unspecified when the country cannot be determined. |
| Country = 'United Kingdom' represents 91.94% of records. | No cleaning action required. Retain the original Country values and account for the dataset's UK concentration when interpreting geographic analysis. |


* Preserve the data and account for the UK concentration when interpreting geographic analysis.
* Invoice, Quantity, Price, Customer ID, StockCode, and Description collectively identify different transaction patterns.
* Create a derived Transaction_Type using documented classification rules to classify sales, returns/cancellations, inventory adjustments, accounting adjustments, and other activity.
* Quantity and Price determine transaction revenue.
* Create Line_Revenue as Quantity × Price, then apply the documented transaction classifications to create Revenue_Eligible and Analytical_Revenue. Standard sales and cancellations/returns remain revenue eligible, while inventory adjustments, zero price transactions, and bad debt adjustments do not contribute to Analytical_Revenue.



## 6. Conclusion
* The dataset has now been structurally and logically profiled. 
* The major transaction types, data quality issues, exceptions, and analytical limitations have been identified and documented. 
* These findings guided the cleaning and transformation process used to produce the analysis ready dataset and support downstream revenue analysis.
