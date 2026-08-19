# Creating a Calendar Table

1. Click the Modeling tab on the top ribbon.
2. Click New table.
3. A formula bar will appear near the top.
4. Paste the Following:

```dax
DimDate =
ADDCOLUMNS(
    CALENDAR(
        DATE(2025, 1, 1),
        DATE(2025, 12, 31)
    ),
    "Year", YEAR([Date]),
    "Month Number", MONTH([Date]),
    "Month", FORMAT([Date], "MMMM"),
    "Month Short", FORMAT([Date], "MMM"),
    "Year-Month", FORMAT([Date], "YYYY-MM"),
    "Quarter", "Q" & FORMAT([Date], "Q"),
    "Day", DAY([Date]),
    "Day Name", FORMAT([Date], "dddd")
)
```
