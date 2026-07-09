# Transformation Activity: Standardize `Date Processed`

## Objective

The `Date Processed` column contains dates written using different date formats.

Because these values are inconsistent, directly changing the column data type to **Date** may result in conversion errors.

In this activity, you will:

1. Create a new column for the cleaned date.
2. Use Power Query to convert the different date formats into valid dates.
3. Ensure the new column uses the **Date** data type.
4. Remove the original `Date Processed` column.
5. Rename the cleaned column to `Date Processed`.

The expected date representation should be:

```text
yyyy-MM-dd
```

For example:

| Original `Date Processed` | Cleaned Date |
| ------------------------- | ------------ |
| May 03, 2025              | 2025-05-03   |
| 24-Jan-2025               | 2025-01-24   |
| 24/05/2025                | 2025-05-24   |
| 10-29-2025                | 2025-10-29   |
| 2025-03-23                | 2025-03-23   |

---

## Step 1: Create a New Column

In the **Power Query Editor**:

1. Select the `CTL_Disbursement_Dirty` query.
2. Go to the **Add Column** tab.
3. Select **Custom Column**.
4. Set the new column name to:

```text
Date Processed Clean
```

Keep the original `Date Processed` column for now.

---

## Step 2: Transform the Different Date Formats

In the **Custom Column Formula** box, enter the following Power Query expression:

```powerquery
let
    d = Text.Trim([Date Processed])
in
    try Date.FromText(d, [Format="yyyy-MM-dd", Culture="en-US"])
    otherwise try Date.FromText(d, [Format="dd/MM/yyyy", Culture="en-GB"])
    otherwise try Date.FromText(d, [Format="MM-dd-yyyy", Culture="en-US"])
    otherwise try Date.FromText(d, [Format="dd-MMM-yyyy", Culture="en-US"])
    otherwise try Date.FromText(d, [Format="MMM dd, yyyy", Culture="en-US"])
    otherwise null
```

Select **OK**.

---

## Step 3: Understand the Transformation

The expression first removes unnecessary spaces from the source value:

```powerquery
Text.Trim([Date Processed])
```

Power Query then attempts several known date formats.

```text
yyyy-MM-dd
dd/MM/yyyy
MM-dd-yyyy
dd-MMM-yyyy
MMM dd, yyyy
```

For example:

```text
2025-03-23
```

is interpreted using:

```text
yyyy-MM-dd
```

The value:

```text
24/05/2025
```

is interpreted using:

```text
dd/MM/yyyy
```

The value:

```text
10-29-2025
```

is interpreted using:

```text
MM-dd-yyyy
```

The value:

```text
24-Jan-2025
```

is interpreted using:

```text
dd-MMM-yyyy
```

The value:

```text
May 03, 2025
```

is interpreted using:

```text
MMM dd, yyyy
```

The `try ... otherwise` statements tell Power Query to attempt the next date format when a conversion fails.

If Power Query cannot interpret the value using any of the known formats, the expression returns:

```text
null
```

---

## Step 4: Change the New Column Data Type to Date

Select the `Date Processed Clean` column.

From the column header, select the **Data Type** icon and choose:

```text
Date
```

Verify that the column uses the **Date** data type.

### Important

The new column must remain a real **Date** column.

Do not convert the result into Text using `Date.ToText`.

Keeping the column as a Date allows Power BI to:

* sort dates chronologically,
* calculate processing duration,
* compare `Date Received` and `Date Processed`,
* analyze processing performance over time,
* establish relationships with a Date table,
* perform time-based DAX calculations.

---

## Step 5: Validate the Results

Review the `Date Processed Clean` column.

Check that the different source formats have been converted into valid dates.

Also check the column for:

```text
null
```

values.

A `null` value may indicate that:

* the original value is missing,
* the value contains invalid data, or
* the date uses a format that is not included in the transformation logic.

### Validation Question

Before removing the original `Date Processed` column, why should you check the new column for `null` values?

---

## Step 6: Remove the Original `Date Processed` Column

After validating the cleaned column:

1. Select the original `Date Processed` column.
2. Right-click the column header.
3. Select **Remove**.

The mixed-format source column should no longer be present.

---

## Step 7: Rename the Cleaned Column

Rename:

```text
Date Processed Clean
```

to:

```text
Date Processed
```

The final table should contain only the cleaned `Date Processed` column.

---

## Expected Transformation Process

```text
Mixed-Format Date Text
        ↓
Create New Column
        ↓
Trim Extra Spaces
        ↓
Try Known Date Formats
        ↓
Convert to Date
        ↓
Validate Results
        ↓
Remove Original Column
        ↓
Rename Cleaned Column
```

---

# Expected Final Result

The final `Date Processed` column should:

* contain valid date values,
* use the **Date** data type,
* have no conversion errors,
* replace the original mixed-format `Date Processed` column.

Dates should be represented consistently as:

```text
yyyy-MM-dd
```

while remaining a true **Date** column in the Power BI semantic model.

---

# Reflection Questions

1. Why might directly changing `Date Processed` to the Date data type result in errors?

2. What is the purpose of `Text.Trim`?

3. How does `try ... otherwise` help handle mixed date formats?

4. Why do we specify a different `Culture` for some date formats?

5. Why should the cleaned column remain a Date instead of Text?

6. How could the cleaned `Date Received` and `Date Processed` columns be used to calculate processing time?

