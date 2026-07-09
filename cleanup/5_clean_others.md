# Transformation Activities: Remaining Data Cleaning

These activities continue the preparation of `CTL_Disbursement_Dirty` for use in the Power BI semantic model.

The objectives are to:

* standardize numeric values,
* properly represent missing data,
* derive values from reliable source columns,
* assign appropriate data types,
* identify potential data-quality problems,
* distinguish valid duplicates from problematic duplicate identifiers.

---

# Activity 1: Clean `Disbursement Amount (USD)`

## Objective

The `Disbursement Amount (USD)` column contains monetary values written using different formats.

Examples include:

```text
813124.36
USD 1,008,449.15
$35,546.65
894,118.25
```

Although the values look different, they all represent a disbursement amount in US dollars.

In this activity, you will:

1. Create a new column named `Disbursement Amount Clean`.
2. Remove currency symbols and text.
3. Remove comma separators.
4. Convert the result into a numeric value.
5. Remove the original column.
6. Rename the cleaned column.

---

## Step 1: Create a New Column

In the **Power Query Editor**:

1. Select the `CTL_Disbursement_Dirty` query.
2. Go to **Add Column**.
3. Select **Custom Column**.
4. Name the new column:

```text
Disbursement Amount Clean
```

---

## Step 2: Clean the Monetary Value

Enter the following Power Query expression:

```powerquery
let
    d = Text.Trim(Text.From([Disbursement Amount (USD)])),
    removeUSD = Text.Replace(d, "USD", ""),
    removeDollar = Text.Replace(removeUSD, "$", ""),
    removeComma = Text.Replace(removeDollar, ",", "")
in
    Number.FromText(Text.Trim(removeComma))
```

Select **OK**.

---

## Step 3: Understand the Transformation

Consider:

```text
USD 1,008,449.15
```

The transformation is:

```text
USD 1,008,449.15
        ↓
Remove "USD"
        ↓
1,008,449.15
        ↓
Remove "$"
        ↓
1,008,449.15
        ↓
Remove ","
        ↓
1008449.15
        ↓
Number.FromText
        ↓
1008449.15
```

Another example:

```text
$35,546.65
```

becomes:

```text
35546.65
```

---

## Step 4: Set the Data Type

Select `Disbursement Amount Clean`.

Set the data type to:

```text
Decimal Number
```

Do not keep the final amount as Text.

---

## Step 5: Validate the Results

Confirm the following transformations:

| Original Value     | Clean Value |
| ------------------ | ----------: |
| `813124.36`        |   813124.36 |
| `USD 1,008,449.15` |  1008449.15 |
| `$35,546.65`       |    35546.65 |
| `894,118.25`       |   894118.25 |

Check the column for conversion errors.

---

## Step 6: Remove and Rename

1. Remove the original `Disbursement Amount (USD)` column.
2. Rename `Disbursement Amount Clean` to:

```text
Disbursement Amount (USD)
```

---

## Expected Result

The final column should:

* contain numeric values,
* use the Decimal Number data type,
* contain no currency symbols,
* contain no thousands separators stored as text.

### Reflection Questions

1. Why should `$` and `USD` not be stored as part of the numeric value?
2. Why does `Number.FromText` need the commas to be removed?
3. Why is Decimal Number more appropriate than Text?
4. How would storing the column as Text affect `SUM` calculations?

---

# Activity 2: Recreate `Processing Days`

## Objective

The original `Processing Days` column contains both numbers and non-numeric values.

Examples include:

```text
1.0
6.0
17.0
N/A
-
pending
blank
```

Rather than attempting to clean the original values, the processing duration can be recreated using the cleaned:

```text
Date Received
```

and:

```text
Date Processed
```

columns.

In this activity, you will:

1. Create a new `Processing Days Clean` column.
2. Calculate the difference between `Date Processed` and `Date Received`.
3. Preserve incomplete transactions as `null`.
4. Remove the original `Processing Days` column.
5. Rename the cleaned column.

---

## Step 1: Create a New Column

Go to:

**Add Column → Custom Column**

Name the new column:

```text
Processing Days Clean
```

---

## Step 2: Calculate Processing Days

Enter:

```powerquery
if [Date Processed] = null then
    null
else
    Duration.Days(
        [Date Processed] - [Date Received]
    )
```

Select **OK**.

---

## Step 3: Understand the Calculation

For example:

```text
Date Received  = 2025-04-10
Date Processed = 2025-04-16
```

The date difference is:

```text
6 days
```

The resulting value is:

```text
6
```

The expression:

```powerquery
[Date Processed] - [Date Received]
```

creates a Duration value.

The function:

```powerquery
Duration.Days(...)
```

extracts the number of days.

---

## Step 4: Handle Incomplete Processing

If:

```text
Date Processed = null
```

the result should remain:

```text
null
```

For example:

| Date Received | Date Processed | Processing Days |
| ------------- | -------------- | --------------: |
| 2025-04-10    | 2025-04-16     |               6 |
| 2025-05-01    | null           |            null |

Do not use the current date to calculate an artificial processing duration.

---

## Step 5: Set the Data Type

Set `Processing Days Clean` to:

```text
Whole Number
```

---

## Step 6: Validate the Results

Check that the final column contains:

```text
1
2
6
17
22
null
```

The column should no longer contain:

```text
N/A
-
pending
```

### Additional Validation

Check whether any calculated processing durations are negative.

A negative value would mean:

```text
Date Processed < Date Received
```

This may indicate a data-quality problem.

---

## Step 7: Remove and Rename

1. Remove the original `Processing Days`.
2. Rename `Processing Days Clean` to:

```text
Processing Days
```

---

## Expected Result

The final `Processing Days` column should:

* be calculated from the two cleaned date columns,
* use the Whole Number data type,
* contain `null` for transactions without a processed date.

### Reflection Questions

1. Why is recreating `Processing Days` preferable to cleaning values such as `pending`?
2. What does subtracting two Date values produce in Power Query?
3. What is the purpose of `Duration.Days`?
4. Why should an incomplete transaction have a null processing duration?
5. What could a negative processing duration indicate?

---

# Activity 3: Clean `Country`

## Objective

The `Country` column contains the text value:

```text
NULL
```

The value `"NULL"` is Text.

It is different from an actual Power Query missing value:

```text
null
```

In this activity, you will replace textual `NULL` values with real null values.

---

## Step 1: Examine the Column

Select the `Country` column.

Observe the distinct values in the column filter.

You should find valid country values such as:

```text
Nepal
Philippines
Sri Lanka
Uzbekistan
```

and:

```text
NULL
```

---

## Step 2: Replace the Textual NULL Value

Select the `Country` column.

Go to:

**Transform → Replace Values**

Set:

```text
Value to Find: NULL
```

Replace with a temporary blank value.

After the replacement, ensure the missing values are represented as:

```text
null
```

Alternatively, create a Custom Column using:

```powerquery
if Text.Upper(Text.Trim([Country])) = "NULL" then
    null
else
    Text.Trim([Country])
```

---

## Step 3: Set the Data Type

Set the `Country` column to:

```text
Text
```

---

## Step 4: Validate the Results

The column should contain valid country names or actual null values.

Correct:

```text
Nepal
Philippines
Sri Lanka
null
```

Incorrect:

```text
NULL
"null"
N/A
```

---

## Expected Result

Textual `NULL` values should no longer exist in the `Country` column.

### Reflection Questions

1. What is the difference between `"NULL"` and `null`?
2. Why is textual `NULL` considered a data-quality problem?
3. How could textual `NULL` affect a relationship with `DimCountry`?
4. Should a missing country automatically be assigned to another country?

---

# Activity 4: Clean `Sector`

## Objective

The `Sector` column contains the same textual `NULL` problem found in `Country`.

Valid sector values include:

```text
Education
Transport
Finance
Energy
Health
```

The value:

```text
NULL
```

should instead represent a missing value.

In this activity, you will convert textual `NULL` values into actual null values.

---

## Step 1: Examine the Column

Select the `Sector` column.

Review the distinct values.

Identify:

```text
NULL
```

---

## Step 2: Create a Clean Sector Column

Go to:

**Add Column → Custom Column**

Name the new column:

```text
Sector Clean
```

Enter:

```powerquery
let
    d = Text.Trim([Sector])
in
    if Text.Upper(d) = "NULL" then
        null
    else
        d
```

Select **OK**.

---

## Step 3: Understand the Transformation

For:

```text
NULL
```

the result is:

```text
null
```

For:

```text
 Education 
```

the result is:

```text
Education
```

The transformation performs two cleaning operations:

```text
Trim extra spaces
        ↓
Identify textual NULL
        ↓
Convert it to actual null
```

---

## Step 4: Set the Data Type

Set `Sector Clean` to:

```text
Text
```

---

## Step 5: Validate the Results

The final values should include:

```text
Education
Transport
Finance
Energy
null
```

There should be no textual:

```text
NULL
```

values.

---

## Step 6: Remove and Rename

1. Remove the original `Sector` column.
2. Rename `Sector Clean` to:

```text
Sector
```

---

## Expected Result

The final `Sector` column should contain valid sector names or actual null values.

### Reflection Questions

1. Why should `NULL` not be treated as a sector name?
2. What is the purpose of `Text.Trim`?
3. Why is `Text.Upper` useful when identifying the value `NULL`?
4. How could textual `NULL` affect `DimSector`?

---

# Activity 5: Set the Correct Data Type for `Fiscal Year`

## Objective

The `Fiscal Year` column contains values such as:

```text
2025
2026
```

The values are already logically correct.

The objective of this activity is to ensure the column uses the appropriate data type.

In this activity, you will convert `Fiscal Year` into a Whole Number.

---

## Step 1: Select the Column

In Power Query, select:

```text
Fiscal Year
```

---

## Step 2: Change the Data Type

Select the Data Type icon in the column header.

Choose:

```text
Whole Number
```

Power Query should add a **Changed Type** transformation step.

---

## Step 3: Validate the Values

Review the column.

The expected values are:

```text
2025
2026
```

The values should be numeric.

They should not be stored as:

```text
"2025"
"2026"
```

Text.

---

## Step 4: Compare Fiscal Year and Quarter

The semantic model should now contain separate columns:

| Fiscal Year | Quarter |
| ----------: | ------: |
|        2025 |       1 |
|        2025 |       2 |
|        2025 |       3 |
|        2025 |       4 |
|        2026 |       1 |
|        2026 |       2 |

The year should no longer need to be embedded in the `Quarter` column.

---

## Expected Result

`Fiscal Year` should use the:

```text
Whole Number
```

data type.

### Reflection Questions

1. Why is `Fiscal Year` stored separately from `Quarter`?
2. Why is Whole Number appropriate for this column?
3. What is the difference between the number `2025` and the text `"2025"`?
4. How could `Fiscal Year` and `Quarter` be combined later to create `Q2 FY2025`?

---

# Activity 6: Validate Missing `Processing Officer` Values

## Objective

The `Processing Officer` column contains missing values.

A missing officer should not automatically be replaced with a generic name or assumed to be valid.

In the dataset, blank officer values appear even in some records with the status:

```text
Processed
```

This creates a potential data-quality issue.

In this activity, you will identify and flag records with missing processing officers.

---

## Step 1: Create a Data Quality Flag

Go to:

**Add Column → Custom Column**

Name the new column:

```text
Officer Data Quality
```

Enter:

```powerquery
let
    officer =
        if [Processing Officer] = null then
            ""
        else
            Text.Trim([Processing Officer])
in
    if officer = "" and [Status] = "Processed" then
        "Missing Officer - Investigate"
    else if officer = "" then
        "No Officer Assigned"
    else
        "Valid"
```

Select **OK**.

---

## Step 2: Understand the Rule

The transformation applies the following logic:

```text
Processing Officer is blank
AND
Status = Processed
        ↓
Missing Officer - Investigate
```

For an incomplete transaction:

```text
Processing Officer is blank
AND
Status = Pending
        ↓
No Officer Assigned
```

If an officer exists:

```text
Processing Officer has a value
        ↓
Valid
```

---

## Step 3: Filter the Data Quality Flag

Filter `Officer Data Quality` to:

```text
Missing Officer - Investigate
```

Review the affected records.

Do not automatically assign an officer.

The correct officer cannot be inferred from the available data.

---

## Step 4: Preserve Missing Officers

Leave the original `Processing Officer` value as:

```text
null
```

when the officer is unknown.

Do not replace missing officers with:

```text
Unknown Officer
Admin
Unassigned
```

unless this is an approved business rule.

---

## Expected Result

The `Processing Officer` column remains unchanged, but missing values are now identifiable using a data-quality flag.

Possible flag values are:

```text
Valid
No Officer Assigned
Missing Officer - Investigate
```

### Reflection Questions

1. Why should a missing officer not automatically be replaced?
2. Why is a blank officer on a Processed record more concerning?
3. What is the difference between cleaning and data-quality validation?
4. Who should determine the correct officer for a problematic record?
5. Should the `Officer Data Quality` column remain in the final semantic model, or only be used during data preparation?

---

# Activity 7: Investigate Duplicate `Withdrawal Application No` Values

## Objective

The `Withdrawal Application No` column contains duplicate identifiers.

A duplicate value does not automatically mean that a row should be deleted.

Before removing duplicates, determine whether `Withdrawal Application No` is expected to uniquely identify one withdrawal application.

In this activity, you will identify duplicate withdrawal application numbers.

---

## Step 1: Create a Reference Query

Right-click the `CTL_Disbursement_Dirty` query.

Select:

```text
Reference
```

Rename the new query:

```text
Withdrawal Application Duplicate Check
```

---

## Step 2: Keep the Identifier Column

Keep only:

```text
Withdrawal Application No
```

Remove the other columns.

---

## Step 3: Group the Records

Go to:

**Home → Group By**

Group by:

```text
Withdrawal Application No
```

Create a new column:

```text
Row Count
```

Operation:

```text
Count Rows
```

The result should resemble:

| Withdrawal Application No | Row Count |
| ------------------------- | --------: |
| WA-2025-100236            |         1 |
| WA-2025-100088            |         2 |
| WA-2026-100023            |         2 |

---

## Step 4: Filter Duplicate Identifiers

Filter `Row Count` to values:

```text
greater than 1
```

The resulting table contains duplicate withdrawal application numbers.

---

## Step 5: Investigate the Original Rows

Return to the original disbursement query.

Filter for one of the duplicate withdrawal application numbers.

Compare fields such as:

```text
Loan/Grant No
Country
Disbursement Amount
Date Received
Status
```

Ask:

> Are the rows exact duplicates, or do they contain different transaction information?

---

## Step 6: Do Not Automatically Remove Duplicates

Do not immediately use:

```text
Remove Duplicates
```

If the identifier is expected to be unique, the duplicate rows require investigation.

If the records are legitimate separate events, the table may require a different unique identifier.

---

## Expected Result

Create a data-quality query containing only withdrawal application numbers with more than one record.

The objective is to:

```text
Identify
    ↓
Inspect
    ↓
Understand
    ↓
Decide
```

Do not use:

```text
Identify
    ↓
Delete
```

### Reflection Questions

1. Why is `Remove Duplicates` potentially dangerous?
2. What does `Group By → Count Rows` reveal?
3. What is a business key?
4. Should `Withdrawal Application No` uniquely identify a row?
5. What should be done if two valid records share the same application number?

---

# Activity 8: Validate Duplicate `Loan/Grant No` Values

## Objective

The `Loan/Grant No` column also contains repeated values.

However, a repeated Loan or Grant number may be legitimate.

A single loan or grant may be associated with multiple withdrawal applications.

For example:

```text
Loan/Grant
    ↓
Withdrawal Application 1
Withdrawal Application 2
Withdrawal Application 3
```

In this activity, you will examine repeated Loan/Grant numbers and determine whether the duplicates represent valid relationships.

---

## Step 1: Create a Reference Query

Right-click the cleaned disbursement query.

Select:

```text
Reference
```

Rename the query:

```text
Loan Grant Duplicate Check
```

---

## Step 2: Keep Two Columns

Keep:

```text
Loan/Grant No
Withdrawal Application No
```

Remove all other columns.

---

## Step 3: Group by Loan/Grant Number

Go to:

**Home → Group By**

Group by:

```text
Loan/Grant No
```

Create:

```text
Application Count
```

Operation:

```text
Count Rows
```

The result should resemble:

| Loan/Grant No | Application Count |
| ------------- | ----------------: |
| LN-4088-SF    |                 2 |
| LN-4474-REG   |                 2 |
| GR-4424-SF    |                 1 |

---

## Step 4: Filter Repeated Loan/Grant Numbers

Filter `Application Count` to:

```text
greater than 1
```

These are Loan/Grant numbers associated with multiple records.

---

## Step 5: Inspect the Associated Applications

Return to the original query.

Filter for one repeated `Loan/Grant No`.

Compare the:

```text
Withdrawal Application No
```

values.

For example:

```text
LN-XXXX
    ↓
WA-XXXX-001
WA-XXXX-002
```

If the withdrawal application numbers are different, the repeated Loan/Grant number may represent a valid one-to-many relationship.

---

## Step 6: Interpret the Duplicate Correctly

Do not remove the duplicate `Loan/Grant No` values simply because they are repeated.

The likely business structure is:

```text
One Loan/Grant
        ↓
Many Withdrawal Applications
```

Therefore:

```text
Loan/Grant No
```

may not be the unique identifier of the disbursement table.

---

## Expected Result

Repeated Loan/Grant numbers should be validated rather than automatically removed.

The exercise should demonstrate the distinction between:

```text
Expected duplicates
```

and:

```text
Problematic duplicates
```

### Reflection Questions

1. Why can one Loan/Grant number appear on multiple rows?
2. What is a one-to-many relationship?
3. Why is `Loan/Grant No` unlikely to be the unique key of the disbursement table?
4. How are the duplicate issues in `Loan/Grant No` different from duplicate `Withdrawal Application No` values?
5. Why should the meaning of a column be understood before removing duplicates?

---

# Summary of Expected Cleaning Results

| Column                    | Final Treatment                                                | Final Data Type |
| ------------------------- | -------------------------------------------------------------- | --------------- |
| Disbursement Amount (USD) | Remove currency text, symbols, and commas                      | Decimal Number  |
| Processing Days           | Recalculate from cleaned dates                                 | Whole Number    |
| Country                   | Convert textual `NULL` to null                                 | Text            |
| Sector                    | Convert textual `NULL` to null                                 | Text            |
| Fiscal Year               | Assign numeric data type                                       | Whole Number    |
| Processing Officer        | Flag missing values for investigation                          | Text            |
| Withdrawal Application No | Identify and investigate duplicates                            | Text            |
| Loan/Grant No             | Validate repeated values as possible one-to-many relationships | Text            |

## Overall Transformation Principle

When cleaning data, not every problem requires the same solution.

```text
Inconsistent representation
        ↓
Standardize

Incorrect data type
        ↓
Convert

Derived value
        ↓
Recalculate

Missing value
        ↓
Understand its meaning

Duplicate value
        ↓
Validate the business key
```

The objective of Power Query transformation is not simply to make the data look clean.

The objective is to create a dataset whose values and structure accurately represent the business process.

