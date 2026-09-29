# Workforce Analytics | Microsoft Fabric Design
### Employment Data · Data Quality · Analytical Modelling

A workforce analytics project foundation with CSV and Parquet source assets. The proposed Fabric solution would support analysis of employment patterns across time, geography, and workforce segments.

**Status:** source assets and solution design. A deployed Fabric implementation is not included.

## Source assets

- [CSV dataset](Unemployment%2Bdata%2Bfor%2Bproject.csv)
- [Parquet dataset](UnEmployment.parquet)

The CSV contains Year, Month, State, Labor Force, Employed, Unemployed, Unemployment Rate, Industry, Gender, and Education Level. Parquet schema and equivalence with the CSV must be validated before treating the files as interchangeable.

## Proposed data flow

```mermaid
flowchart LR
    A[CSV and Parquet sources] --> B[Bronze source retention]
    B --> C[Silver schema and consistency checks]
    C --> D[Gold workforce summaries]
    D --> E[Power BI model]
```

The layer names follow the [Microsoft Fabric medallion design](https://learn.microsoft.com/en-us/fabric/onelake/onelake-medallion-lakehouse-architecture). The diagram is a design target, not evidence of deployment.

## Analytical decisions

**Grain:** verify uniqueness of Year, Month, State, Industry, Gender, and Education Level. Do not remove repeated rows until the source key and correction rules are understood.

**Time:** convert month names to a month number and a first-of-month date. Sort chronologically rather than alphabetically.

**Percentages:** parse values such as `10.00%` as 0.10 for storage and format them as percentages in the report.

**Aggregation:** calculate unemployment rate as total Unemployed divided by total Labor Force only across compatible, non-overlapping population groups for the same reporting period. Do not sum monthly population counts into an annual headcount or take an unweighted average of rates.

## Quality checks

| Check | Expected handling |
| --- | --- |
| Missing year, month, or state | Quarantine and report |
| Negative population counts | Flag for investigation |
| Employed + Unemployed differs from Labor Force | Apply a documented source-specific tolerance |
| Reported rate differs from calculated rate | Check rounding and denominator definitions |
| Duplicate candidate keys | Investigate source grain before deduplication |
| CSV/Parquet mismatch | Compare schema, keys, counts, and canonical values |

## Implementation and evidence plan

1. Document source publisher, reference period, geography definitions, and reuse terms.
2. Confirm CSV and Parquet equivalence or record why they differ.
3. Implement typed ingestion with a file manifest and replay protection.
4. Build curated tables with documented aggregation rules.
5. Test missing data, zero denominators, schema changes, and reruns.
6. Add a semantic model, report preview, refresh evidence, and measured performance results.

No official statistical provenance, causal conclusions, or production deployment is claimed from the filenames alone. The aim is a transparent engineering case study with reproducible evidence.

---
[Explore the full Power BI, Fabric & Data Engineering portfolio](https://github.com/Osama-data/Power-Bi-Projects)
