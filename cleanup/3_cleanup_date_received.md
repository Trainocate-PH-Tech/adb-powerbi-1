# Transformation Activity: Standardize `Date Received`

## Objective

The `Date Received` column contains dates written in different formats. Because the values are inconsistent, directly changing the column data type to **Date** may result in errors.

In this activity, you will:

1. Create a new cleaned date column.
2. Use Power Query to convert the different date formats into valid dates.
3. Ensure the new column uses the **Date** data type.
4. Remove the original `Date Received` column.

The expected output should represent dates consistently as:

```text
yyyy-MM-dd
```

Example:

| Original `Date Received` | Cleaned Date |
| ------------------------ | ------------ |
| Apr 28, 2025             | 2025-04-28   |
| 17/01/2025               | 2025-01-17   |
| 10-28-2025               | 2025-10-28   |
| 2025-05-10               | 2025-05-10   |
| 30-Mar-2025              | 2025-03-30   |

---

## Step 1: Create a New Column

In the **Power Query Editor**:

1. Select the `CTL_Disbursement_Dirty` query.
2. Go to the **Add Column** tab.
3. Select **Custom Column**.
4. Set the new column name to:

```text
Date Received Clean
```

Do not replace the original `Date Received` column yet.

---

## Step 2: Transform the Different Date Formats

In the **Custom Column Formula** box, enter the following Power Query expression:

```powerquery
let
    d = Text.Trim([Date Received])
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

## Step 3: Examine the Transformation

The expression attempts to convert each value using several possible date formats.

```text
yyyy-MM-dd
dd/MM/yyyy
MM-dd-yyyy
dd-MMM-yyyy
MMM dd, yyyy
```

For example:

```text
2025-05-10
```

is interpreted using:

```text
yyyy-MM-dd
```

The value:

```text
17/01/2025
```

is interpreted using:

```text
dd/MM/yyyy
```

The value:

```text
10-28-2025
```

is interpreted using:

```text
MM-dd-yyyy
```

The value:

```text
30-Mar-2025
```

is interpreted using:

```text
dd-MMM-yyyy
```

The value:

```text
Apr 28, 2025
```

is interpreted using:

```text
MMM dd, yyyy
```

The `try ... otherwise` statements allow Power Query to attempt the next format when the previous format cannot convert the value.

If none of the defined formats match, the result will be:

```text
null
```

---

## Step 4: Change the New Column Data Type to Date

Select the `Date Received Clean` column.

From the column header, select the **Data Type** icon and choose:

```text
Date
```

Verify that the column data type icon displays the Date data type.

### Important

The objective is to keep the column as a real **Date**, not as Text.

Do not use `Date.ToText` to produce the final column.

The values should be stored as dates so that Power BI can:

* sort dates chronologically,
* calculate date differences,
* derive year, month, and quarter values,
* relate the column to a Date table,
* perform time-based analysis.

---

## Step 5: Validate the Results

Review the `Date Received Clean` column.

Check that the different source formats have been successfully converted to valid dates.

You should also check for:

```text
null
```

values.

A `null` value may indicate that the original date uses a format that was not included in the transformation logic.

### Validation Question

Why is checking for `null` values important before removing the original column?

---

## Step 6: Remove the Original `Date Received` Column

After validating the new column:

1. Select the original `Date Received` column.
2. Right-click the column header.
3. Select **Remove**.

The original mixed-format column should no longer be present.

---

## Step 7: Rename the Cleaned Column

Rename:

```text
Date Received Clean
```

to:

```text
Date Received
```

The final table should contain only the cleaned `Date Received` column.

The transformation process is:

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

The final `Date Received` column should:

* contain valid date values,
* use the **Date** data type,
* have no conversion errors,
* replace the original mixed-format `Date Received` column.

Conceptually, dates should be represented consistently as:

```text
yyyy-MM-dd
```

while remaining a true **Date** column in the Power BI semantic model.

---

# Reflection Questions

1. Why does directly changing `Date Received` to the Date data type result in errors?

2. What is the purpose of `Text.Trim` in the transformation?

3. What does `try ... otherwise` do?

4. Why do we attempt multiple date formats?

5. Why should the final column remain a Date instead of being converted to Text?

6. What should you investigate when the cleaned column contains `null` values?

