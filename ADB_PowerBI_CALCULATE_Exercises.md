# Power BI DAX Exercises: Using `CALCULATE`

## Overview

This exercise set focuses on one of the most important DAX functions in Power BI: `CALCULATE()`.

`CALCULATE()` evaluates an expression under a modified filter context. It is commonly used to:

- Add filters
- Replace filters
- Remove filters
- Calculate percentages of totals
- Compare categories
- Evaluate values for specific dates
- Combine DAX measures with more advanced filtering logic

These exercises use the ADB Power BI clean dataset and are designed to progress from beginner to intermediate use of `CALCULATE()`.

---

# 1. Create the Base Measure

Before using `CALCULATE()`, create a reusable measure for total exposure.

```DAX
Total Exposure =
SUM(FactExposure[Outstanding Exposure USD])
```

## Exercise

1. Go to **Modeling > New Measure**.
2. Enter the measure above.
3. Add a **Card** visual.
4. Place `Total Exposure` in the card.

This measure will be reused in the succeeding exercises.

---

# 2. Filter Exposure by Risk Rating

Use `CALCULATE()` to evaluate total exposure only for records with a particular risk rating.

```DAX
High Risk Exposure =
CALCULATE(
    [Total Exposure],
    FactExposure[Risk Rating] = "High"
)
```

> Replace `"High"` with an actual value in the `Risk Rating` column if the dataset uses a different rating convention.

## Exercise

Create two card visuals:

- Total Exposure
- High Risk Exposure

## Learning Objective

Understand how `CALCULATE()` can add a filter to an existing measure.

---

# 3. Filter Exposure Using a Dimension Table

Filters do not have to come directly from the fact table.

For example:

```DAX
Loan Exposure =
CALCULATE(
    [Total Exposure],
    DimProduct[Product Type] = "Loan"
)
```

## Exercise

1. Create the measure.
2. Add it to a card.
3. Compare it with `Total Exposure`.

## Learning Objective

Understand how relationships allow filters from dimension tables to affect the fact table.

---

# 4. Filter by Instrument

Create another filtered measure using the instrument dimension.

```DAX
Specific Instrument Exposure =
CALCULATE(
    [Total Exposure],
    DimInstrument[Instrument] = "Loan"
)
```

> Replace `"Loan"` with an existing instrument value if necessary.

## Exercise

Create a table containing:

- Instrument
- Total Exposure
- Specific Instrument Exposure

## Learning Objective

Reinforce how filter propagation works in a star schema.

---

# 5. Latest Snapshot Exposure

The dataset contains snapshot dates. Use `CALCULATE()` together with a variable to return exposure for the latest available snapshot.

```DAX
Latest Snapshot Exposure =
VAR LatestDate =
    MAX(DimDate[Month End Date])
RETURN
    CALCULATE(
        [Total Exposure],
        DimDate[Month End Date] = LatestDate
    )
```

## Exercise

Create a card showing the latest snapshot exposure.

Add a slicer using:

```text
DimDate[Month End Date]
```

Observe how the result changes when the date context changes.

## Learning Objective

Use variables together with `CALCULATE()`.

---

# 6. Previous Snapshot Exposure

Create a measure that identifies the previous available snapshot date.

```DAX
Previous Snapshot Exposure =
VAR CurrentDate =
    MAX(DimDate[Month End Date])

VAR PreviousDate =
    CALCULATE(
        MAX(DimDate[Month End Date]),
        FILTER(
            ALL(DimDate),
            DimDate[Month End Date] < CurrentDate
        )
    )

RETURN
    CALCULATE(
        [Total Exposure],
        DimDate[Month End Date] = PreviousDate
    )
```

## Exercise

Create cards for:

- Latest Snapshot Exposure
- Previous Snapshot Exposure

## Learning Objective

Combine:

- `CALCULATE`
- `FILTER`
- `ALL`
- Variables

to evaluate values from another time period.

---

# 7. Exposure Change

Create a measure comparing the current and previous snapshot values.

```DAX
Exposure Change =
[Latest Snapshot Exposure] -
[Previous Snapshot Exposure]
```

Then calculate the percentage change.

```DAX
Exposure Change % =
DIVIDE(
    [Exposure Change],
    [Previous Snapshot Exposure]
)
```

## Exercise

Create a KPI area containing:

- Latest Snapshot Exposure
- Previous Snapshot Exposure
- Exposure Change
- Exposure Change %

Format `Exposure Change %` as a percentage.

## Learning Objective

Use previously created `CALCULATE()` measures as building blocks for new measures.

---

# 8. Ignore Country Filters

`CALCULATE()` can also remove filters.

Create the following measure:

```DAX
All Country Exposure =
CALCULATE(
    [Total Exposure],
    REMOVEFILTERS(DimCountry)
)
```

## Exercise

Create a table with:

- Country
- Total Exposure
- All Country Exposure

Observe that `All Country Exposure` remains the same for each country row.

## Learning Objective

Understand how `REMOVEFILTERS()` changes the filter context.

---

# 9. Country Share of Total Exposure

Use the previous measure to calculate each country's share of total exposure.

```DAX
Country Share % =
DIVIDE(
    [Total Exposure],
    [All Country Exposure]
)
```

## Visualization Exercise

Create a table containing:

- Country
- Total Exposure
- Country Share %

Then format `Country Share %` as a percentage.

You may also create a bar chart:

- **Axis:** Country
- **Values:** Total Exposure
- **Tooltip:** Country Share %

## Learning Objective

Learn how `CALCULATE()` is commonly used to calculate percent-of-total metrics.

---

# 10. Ignore Sector Filters

Create a similar measure for sector analysis.

```DAX
All Sector Exposure =
CALCULATE(
    [Total Exposure],
    REMOVEFILTERS(DimSector)
)
```

Then create:

```DAX
Sector Share % =
DIVIDE(
    [Total Exposure],
    [All Sector Exposure]
)
```

## Visualization Exercise

Create a bar chart:

- **Axis:** Sector
- **Values:** Total Exposure
- **Tooltip:** Sector Share %

## Learning Objective

Apply the same DAX pattern to another dimension.

---

# 11. Sector Share Within a Selected Country

Create the following measure:

```DAX
Exposure % of Selected Total =
DIVIDE(
    [Total Exposure],
    CALCULATE(
        [Total Exposure],
        REMOVEFILTERS(DimSector[Sector])
    )
)
```

## Exercise

Create a table containing:

- Sector
- Total Exposure
- Exposure % of Selected Total

Then add a slicer using:

```text
DimCountry[Country]
```

Select different countries.

## What to Observe

The denominator ignores the sector filter but still responds to the country slicer.

For example:

- Selecting Country A causes the denominator to become total exposure for Country A.
- Each sector is then shown as a percentage of Country A's total.

## Learning Objective

Understand that `CALCULATE()` can selectively remove filters while retaining other filter context.

This is one of the most important concepts in DAX.

---

# 12. Exposure Above a Threshold

For more complex conditions, combine `CALCULATE()` with `FILTER()`.

```DAX
Large Exposure =
CALCULATE(
    [Total Exposure],
    FILTER(
        FactExposure,
        FactExposure[Outstanding Exposure USD] >= 10000000
    )
)
```

## Exercise

Create cards for:

- Total Exposure
- Large Exposure

Change the threshold from:

```text
10,000,000
```

to:

```text
5,000,000
```

and observe the result.

## Learning Objective

Understand when to use `FILTER()` inside `CALCULATE()`.

---

# 13. Count Facilities

First create a base facility-count measure.

```DAX
Facility Count =
DISTINCTCOUNT(FactExposure[FacilityKey])
```

Then create:

```DAX
Large Exposure Facilities =
CALCULATE(
    [Facility Count],
    FactExposure[Outstanding Exposure USD] >= 10000000
)
```

## Exercise

Create two cards:

- Facility Count
- Large Exposure Facilities

## Learning Objective

Understand that `CALCULATE()` works with measures other than `SUM()`.

---

# 14. Percentage of Facilities With Large Exposure

Create:

```DAX
Large Exposure Facility % =
DIVIDE(
    [Large Exposure Facilities],
    [Facility Count]
)
```

## Exercise

Create a card showing the percentage.

Then add a country slicer.

Observe whether the percentage changes by country.

## Learning Objective

Combine conditional counts with percentage measures.

---

# 15. CALCULATE With Multiple Conditions

`CALCULATE()` can apply several filters at the same time.

Example:

```DAX
High Risk Loan Exposure =
CALCULATE(
    [Total Exposure],
    FactExposure[Risk Rating] = "High",
    DimProduct[Product Type] = "Loan"
)
```

## Exercise

Create:

- Total Exposure
- High Risk Exposure
- Loan Exposure
- High Risk Loan Exposure

Display all four as cards.

## Learning Objective

Understand that multiple filter arguments inside `CALCULATE()` are combined.

Conceptually:

```text
Risk Rating = High
AND
Product Type = Loan
```

---

# 16. Compare a Category Against the Overall Total

Create:

```DAX
Exposure % of Overall Total =
DIVIDE(
    [Total Exposure],
    CALCULATE(
        [Total Exposure],
        REMOVEFILTERS()
    )
)
```

## Exercise

Place the measure in a matrix with:

- Rows: Country
- Columns: Sector
- Values:
  - Total Exposure
  - Exposure % of Overall Total

## Learning Objective

Compare the current filter context with a completely unfiltered total.

---

# 17. Visualization Exercise: CALCULATE Dashboard

Create a report page called:

```text
Exposure Analysis
```

Add the following visuals.

## KPI Cards

Create cards for:

- Total Exposure
- Latest Snapshot Exposure
- Previous Snapshot Exposure
- Exposure Change %
- Facility Count

---

## Country Exposure Bar Chart

Use:

- **Axis:** Country
- **Values:** Total Exposure
- **Tooltip:** Country Share %

---

## Sector Exposure Bar Chart

Use:

- **Axis:** Sector
- **Values:** Total Exposure
- **Tooltip:** Sector Share %

---

## Risk Rating Chart

Use:

- **Axis:** Risk Rating
- **Values:** Total Exposure

---

## Snapshot Trend

Create a line chart:

- **X-axis:** Month End Date
- **Y-axis:** Total Exposure

---

## Slicers

Add slicers for:

- Country
- Sector
- Product Type
- Risk Rating
- Month End Date

---

# 18. Challenge Exercise: Dynamic Percentage Analysis

Create a matrix with:

- Rows: Sector
- Values:
  - Total Exposure
  - Exposure % of Selected Total

Add slicers for:

- Country
- Product
- Risk Rating

Experiment with different selections.

## Questions

1. Does `Total Exposure` respond to the slicers?
2. Does the percentage measure respond to the slicers?
3. Which filter is being removed by `REMOVEFILTERS()`?
4. Which filters remain active?
5. Why does each sector percentage change when a different country is selected?

---

# 19. Challenge Exercise: Risk Concentration

Create a measure that calculates the percentage of total exposure belonging to high-risk facilities.

```DAX
High Risk Exposure % =
DIVIDE(
    [High Risk Exposure],
    [Total Exposure]
)
```

## Visualization

Create:

- Card: High Risk Exposure
- Card: High Risk Exposure %
- Bar chart: High Risk Exposure by Country
- Bar chart: High Risk Exposure by Sector

Add Country and Sector slicers.

## Analysis Questions

1. Which country has the largest amount of high-risk exposure?
2. Which sector has the largest amount of high-risk exposure?
3. Does the country with the largest total exposure also have the largest high-risk percentage?
4. Does selecting a sector change the high-risk exposure percentage?

---

# 20. Challenge Exercise: Build Your Own CALCULATE Measure

Create your own measure using the pattern:

```DAX
My Measure =
CALCULATE(
    [Base Measure],
    Filter1,
    Filter2
)
```

Choose at least two of the following dimensions:

- Country
- Sector
- Product
- Instrument
- Risk Rating
- Date

Examples could include:

- Exposure for a particular country
- Exposure for a particular product
- High-risk exposure for one sector
- Facility count for one instrument
- Exposure for a selected snapshot

Create an appropriate visualization for your measure.

---

# CALCULATE Syntax Summary

The basic syntax is:

```DAX
CALCULATE(
    Expression,
    Filter1,
    Filter2,
    ...
)
```

For example:

```DAX
High Risk Exposure =
CALCULATE(
    [Total Exposure],
    FactExposure[Risk Rating] = "High"
)
```

Conceptually:

```text
Take the Total Exposure measure
        ↓
Change the current filter context
        ↓
Keep only Risk Rating = High
        ↓
Evaluate Total Exposure again
```

---

# CALCULATE Patterns to Remember

## Add a Filter

```DAX
CALCULATE(
    [Measure],
    Table[Column] = "Value"
)
```

---

## Apply Multiple Filters

```DAX
CALCULATE(
    [Measure],
    Table1[Column] = "Value",
    Table2[Column] = "Value"
)
```

---

## Remove a Filter

```DAX
CALCULATE(
    [Measure],
    REMOVEFILTERS(DimensionTable)
)
```

---

## Remove a Filter From One Column

```DAX
CALCULATE(
    [Measure],
    REMOVEFILTERS(DimensionTable[Column])
)
```

---

## Apply a Complex Filter

```DAX
CALCULATE(
    [Measure],
    FILTER(
        Table,
        Condition
    )
)
```

---

# Suggested Teaching Sequence

For a beginner Power BI class, teach `CALCULATE()` in this order:

1. Create a base measure using `SUM()`
2. Use `CALCULATE()` to add one filter
3. Filter through a dimension table
4. Add multiple filters
5. Use `REMOVEFILTERS()`
6. Calculate percent of total
7. Combine `CALCULATE()` with `DIVIDE()`
8. Combine `CALCULATE()` with `FILTER()`
9. Use `CALCULATE()` for snapshot comparisons
10. Build an interactive dashboard using slicers

---

# Key Concept

The most important idea to remember is:

> **`CALCULATE()` evaluates a measure after changing its filter context.**

This is why `CALCULATE()` is central to DAX.

A measure such as:

```DAX
Total Exposure =
SUM(FactExposure[Outstanding Exposure USD])
```

does not change.

Instead, `CALCULATE()` changes the context in which that measure is evaluated.

For example:

```DAX
CALCULATE(
    [Total Exposure],
    DimSector[Sector] = "Energy"
)
```

means:

> Calculate Total Exposure, but evaluate it as if the report were filtered to the Energy sector.

Understanding this concept makes it much easier to work with:

- Percent-of-total calculations
- Time intelligence
- Previous-period comparisons
- Risk concentration
- Category comparisons
- Interactive slicers
- Dynamic Power BI dashboards
