# Methodology and Data Quality

## 1. Project Scope

This document describes the data-validation checks, analytical rules, and limitations applied in the Retail Sales, Customer & Product Performance Intelligence project.

The analysis was performed using WPS Office Spreadsheets on the UCI Online Retail dataset. The original transaction records were preserved, and a separate working sheet was used for calculated fields and analysis.

## 2. Data Overview

- **Source:** UCI Machine Learning Repository — Online Retail
- **Observation period:** 1 December 2010 to 9 December 2011
- **Transaction records:** 541,909
- **Original columns:** 8

The original fields are InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, and Country.

## 3. Data Quality Audit

| Validation check | Result | Interpretation |
|---|---:|---|
| Missing InvoiceNo | 0 | No missing invoice identifiers found |
| Missing StockCode | 0 | No missing stock codes found |
| Missing Description | 1,454 | Product descriptions are unavailable for these records |
| Missing CustomerID | 135,080 | Customer-level analysis is limited for these records |
| Missing InvoiceDate | 0 | No missing invoice dates found |
| Missing UnitPrice | 0 | No missing unit-price values found |
| Missing Quantity | 0 | No missing quantities found |
| Missing Country | 0 | No missing country values found |
| Cancellation-prefixed invoices | 9,288 | These records require separate treatment in sales analysis |
| Negative quantities | 10,624 | May represent cancellations, returns, or other adjustments |
| Zero unit prices | 2,515 | Require interpretation before inclusion in sales metrics |
| Negative unit prices | 2 | Identified as bad-debt adjustment entries |
| Dates on or after 10 December 2011 | 0 | No dates outside the documented end date were found |

## 4. Missing Values

A total of 135,080 records had missing CustomerID values, representing approximately 24.93% of transaction records. Another 1,454 records had missing descriptions.

All 1,454 records with missing descriptions also had missing CustomerID values.

Missing identifiers were not automatically filled because there was no reliable basis for assigning customer identities. These records were retained in the working data.

## 5. Duplicate Investigation

A composite key was constructed using the original transaction fields to investigate repeated combinations.

The audit identified repeated transaction keys, including a confirmed matching pair. However, repeated combinations were not automatically deleted because identical transaction details do not, by themselves, prove that a record is erroneous.

The investigation was used to flag potential duplicates for review. No transaction records were deleted as part of this process.

## 6. Cancellations and Negative Quantities

The audit identified:

- 9,288 invoice records with invoice numbers beginning with `C`
- 10,624 records with negative quantities
- 1,336 negative-quantity records with zero unit prices

All 9,288 cancellation-prefixed records were included within the 10,624 negative-quantity records. Therefore, these counts overlap.

The records were retained. Cancellation-prefixed records were classified separately, while other negative-quantity records were not automatically assumed to be errors.

## 7. Unit-Price Validation

The audit found 2,515 records with a unit price of zero and two records with a negative unit price.

The two negative-price records were described as `Adjust bad debt`. These were treated as unusual accounting-related entries rather than ordinary merchandise sales.

Zero-price records included different descriptions and transaction patterns. They were flagged for review rather than universally classified as invalid.

## 8. Calculated Fields

The working sheet included calculated fields to support analysis:

- **LineAmount:** Quantity multiplied by UnitPrice
- **TransactionType:** Cancellation or Regular, based on whether InvoiceNo begins with `C`
- **PriceFlag:** Negative Price, Zero Price, or Positive Price
- **CustomerFlag:** Missing CustomerID or Customer Identified
- **DescriptionFlag:** Missing Description or Description Available
- **SalesMonth:** First day of the transaction month, used for monthly grouping
- **SalesEligibility:** Include or Review/Exclude, based on the current analysis rule

The calculated fields were added to the working sheet, leaving the original source sheet unchanged.

## 9. Sales Eligibility Rule

The provisional sales analysis included records meeting both conditions:

- The invoice number did not begin with `C`.
- The unit price was positive.

Records with cancellation-prefixed invoice numbers, zero unit prices, or negative unit prices were marked `Review/Exclude`.

Results under this rule:

| Metric | Result |
|---|---:|
| Total source records | 541,909 |
| Included records | 530,104 |
| Records flagged for review | 11,805 |
| Provisional sales value | 10,666,688.64 |

This rule is an analytical choice, not a verified accounting definition of revenue. Excluding every zero-price record may omit some legitimate transactions, so the resulting sales value remains provisional.

## 10. Validation and Unresolved Checks

The audit formulas were used to check missing values, cancellation counts, price flags, date boundaries, and sales eligibility.

The line-amount comparison also returned 1,336 mismatches under one spreadsheet comparison formula. This result was not conclusively explained and remains an unresolved validation item. It should not be interpreted as proof that 1,336 line amounts are incorrect.

Further review is recommended before treating all calculated line amounts as fully reconciled.

## 11. Limitations

- The dataset covers approximately one year and cannot establish recurring seasonal patterns on its own.
- December 2011 is incomplete because the dataset ends on 9 December.
- Missing CustomerID values limit customer-level analysis.
- Product descriptions include postage and service-related entries.
- The provisional sales value depends on the stated eligibility rule.
- Repeated transaction keys require contextual review before deletion.
- Zero-price records may have legitimate explanations.
- The analysis does not establish profit, inventory availability, or verified accounting revenue.

## 12. Conclusion

The project applied spreadsheet-based validation and transparent analytical rules while preserving source records. The documented checks provide context for interpreting the dashboard and business findings.

The provisional metrics should be used with the stated limitations, and unresolved validation issues should be investigated before the results are treated as final accounting figures.
