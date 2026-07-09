# Transformation Activity: Clean the `SLA Met` Column

## Objective

The `SLA Met` column contains several different representations of the same logical values.

For example, values representing **Yes** include:

```text
Yes
yes
Y
1
TRUE
```

Values representing **No** include:

```text
No
no
N
0
FALSE
```

The column also contains values such as:

```text
blank
-
na
```

These inconsistent representations make filtering and analysis more difficult.

In this activity, you will:

1. Create a new column named `SLA Met Clean`.
2. Use Power Query to standardize the different SLA values.
3. Preserve missing or unavailable SLA values as `null`.
4. Remove the original `SLA Met` column.
5. Rename `SLA Met Clean` to `SLA Met`.

The expected output should contain only:

```text
Yes
No
null
```

---

## Step 1: Create a New Column

In the **Power Query Editor**:

1. Select the `CTL_Disbursement_Dirty` query.
2. Go to the **Add Column** tab.
3. Select **Custom Column**.
4. Name the new column:

```text
SLA Met Clean
```

Keep the original `SLA Met` column for now.

---

## Step 2: Standardize the SLA Values

In the **Custom Column Formula** box, enter:

```powerquery
let
    d =
        if [SLA Met] = null then
            null
        else
            Text.Upper(Text.Trim(Text.From([SLA Met])))
in
    if List.Contains({"YES", "Y", "1", "TRUE"}, d) then
        "Yes"
    else if List.Contains({"NO", "N", "0", "FALSE"}, d) then
        "No"
    else
        null
```

Select **OK**.

---

## Step 3: Understand the Transformation

The first section prepares the original value:

```powerquery
let
    d =
        if [SLA Met] = null then
            null
        else
            Text.Upper(Text.Trim(Text.From([SLA Met])))
```

The cleaned value is stored in a variable named:

```text
d
```

### A. Handle Null Values

The expression first checks:

```powerquery
[SLA Met] = null
```

If the source value is missing, the value remains:

```text
null
```

This avoids treating a missing SLA value as either `Yes` or `No`.

---

### B. Convert the Value to Text

The expression:

```powerquery
Text.From([SLA Met])
```

converts the source value to Text.

This is useful because the column contains values such as:

```text
1
0
TRUE
FALSE
Y
N
```

The transformation allows the values to be evaluated consistently as text.

---

### C. Remove Extra Spaces

The expression:

```powerquery
Text.Trim(...)
```

removes unnecessary leading and trailing spaces.

For example:

```text
" Yes "
```

becomes:

```text
"Yes"
```

---

### D. Convert the Value to Uppercase

The expression:

```powerquery
Text.Upper(...)
```

standardizes the casing.

For example:

```text
yes
Yes
YES
```

all become:

```text
YES
```

Similarly:

```text
no
No
NO
```

all become:

```text
NO
```

This simplifies the remaining transformation logic.

---

## Step 4: Identify Values That Mean `Yes`

The expression:

```powerquery
List.Contains({"YES", "Y", "1", "TRUE"}, d)
```

checks whether the cleaned value is one of the known representations of `Yes`.

The following values are transformed:

| Cleaned Source Value | Result |
| -------------------- | ------ |
| YES                  | Yes    |
| Y                    | Yes    |
| 1                    | Yes    |
| TRUE                 | Yes    |

For example:

```text
yes
 ↓
Text.Upper
 ↓
YES
 ↓
List.Contains
 ↓
Yes
```

Another example:

```text
1
 ↓
Text.From
 ↓
"1"
 ↓
List.Contains
 ↓
Yes
```

---

## Step 5: Identify Values That Mean `No`

The expression:

```powerquery
List.Contains({"NO", "N", "0", "FALSE"}, d)
```

checks whether the value represents `No`.

The following values are transformed:

| Cleaned Source Value | Result |
| -------------------- | ------ |
| NO                   | No     |
| N                    | No     |
| 0                    | No     |
| FALSE                | No     |

For example:

```text
FALSE
 ↓
List.Contains
 ↓
No
```

---

## Step 6: Handle Unknown or Missing Values

The final part of the transformation is:

```powerquery
else
    null
```

This means that values not recognized as `Yes` or `No` are preserved as missing values.

For example:

```text
-
na
blank
```

become:

```text
null
```

Do not automatically convert these values to `No`.

A missing SLA result does not necessarily mean that the SLA was not met.

For example, a transaction that has not yet completed processing may not yet have an SLA result.

---

## Step 7: Set the Data Type to Text

Select the `SLA Met Clean` column.

From the column header, select the **Data Type** icon.

Choose:

```text
Text
```

The final values should be:

```text
Yes
No
null
```

---

## Step 8: Validate the Results

Review the `SLA Met Clean` column.

Confirm that the different source values have been standardized correctly.

| Original `SLA Met` | `SLA Met Clean` |
| ------------------ | --------------- |
| Yes                | Yes             |
| yes                | Yes             |
| Y                  | Yes             |
| 1                  | Yes             |
| TRUE               | Yes             |
| No                 | No              |
| no                 | No              |
| N                  | No              |
| 0                  | No              |
| FALSE              | No              |
| -                  | null            |
| na                 | null            |
| blank              | null            |

After cleaning, there should be no values such as:

```text
yes
Y
TRUE
1
no
N
FALSE
0
-
na
```

The only valid non-null values should be:

```text
Yes
No
```

---

## Step 9: Remove the Original `SLA Met` Column

After validating the new column:

1. Select the original `SLA Met` column.
2. Right-click the column header.
3. Select **Remove**.

The inconsistent source values should no longer be present in the table.

---

## Step 10: Rename `SLA Met Clean`

Rename:

```text
SLA Met Clean
```

to:

```text
SLA Met
```

The final `SLA Met` column should use the **Text** data type.

---

# Expected Final Result

The final column should contain:

| SLA Met |
| ------- |
| Yes     |
| No      |
| null    |

The complete transformation process is:

```text
Raw SLA Value
      ↓
Handle Null
      ↓
Convert to Text
      ↓
Trim Extra Spaces
      ↓
Convert to Uppercase
      ↓
Check Known Yes Values
      ↓
Check Known No Values
      ↓
Unknown / Missing → null
      ↓
Remove Original SLA Met
      ↓
Rename SLA Met Clean to SLA Met
```

---

# Reflection Questions

1. Why do `Yes`, `yes`, `Y`, `1`, and `TRUE` need to be standardized?

2. What is the purpose of `Text.From`?

3. Why do we use `Text.Trim`?

4. How does `Text.Upper` simplify the transformation?

5. What does `List.Contains` do?

6. Why should `-`, `na`, and blank values become `null` instead of `No`?

7. Why is consistent encoding important when calculating an SLA percentage?

8. What problems could occur if a report filters only for `SLA Met = "Yes"` while values such as `Y` and `TRUE` remain in the dataset?

