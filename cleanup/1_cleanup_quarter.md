# Transformation Activity: Clean the `Quarter` Column

## Objective

The current `Quarter` column contains values such as:

```text
Q1 2025
Q2 2025
Q3 2025
Q4 2025
```

Each value contains both the quarter number and the year.

Because the fiscal year will be stored separately, the `Quarter` column should contain only the quarter number:

```text
1
2
3
4
```

In this activity, you will:

1. Create a new column named `Quarter Clean`.
2. Use Power Query to extract the quarter number.
3. Convert the quarter number into a Whole Number.
4. Remove the original `Quarter` column.
5. Rename `Quarter Clean` to `Quarter`.

---

## Step 1: Create a New Column

In the **Power Query Editor**:

1. Select the `CTL_Disbursement_Dirty` query.
2. Go to the **Add Column** tab.
3. Select **Custom Column**.
4. Name the new column:

```text
Quarter Clean
```

Keep the original `Quarter` column for now.

---

## Step 2: Transform the Quarter Value

In the **Custom Column Formula** box, enter:

```powerquery
let
    d = Text.Trim([Quarter])
in
    Number.FromText(
        Text.BetweenDelimiters(
            d,
            "Q",
            " "
        )
    )
```

Select **OK**.

---

## Step 3: Understand the Power Query Expression

The expression begins with:

```powerquery
let
    d = Text.Trim([Quarter])
```

This creates a variable named:

```text
d
```

The variable stores the cleaned value of the `Quarter` column.

For example:

```text
" Q2 2025 "
```

becomes:

```text
"Q2 2025"
```

The `in` keyword identifies the final expression that Power Query should return:

```powerquery
in
    Number.FromText(...)
```

The general structure of a Power Query `let` expression is:

```powerquery
let
    variable = expression
in
    result
```

---

## Step 4: Extract the Quarter Number

The following function is used:

```powerquery
Text.BetweenDelimiters(
    d,
    "Q",
    " "
)
```

For the value:

```text
Q2 2025
```

Power Query extracts the value between:

```text
Q
```

and the first space.

The result is:

```text
"2"
```

The transformation is:

```text
Q2 2025
 ↑
 Start after Q

Q2 2025
  ↑
 Stop at the space
```

The extracted value is:

```text
2
```

---

## Step 5: Convert the Value to a Number

`Text.BetweenDelimiters` returns Text.

Therefore:

```text
"2"
```

must be converted into a number.

The expression:

```powerquery
Number.FromText(...)
```

converts:

```text
"2"
```

into:

```text
2
```

The complete transformation is:

```text
" Q2 2025 "
      ↓
Text.Trim
      ↓
"Q2 2025"
      ↓
Text.BetweenDelimiters
      ↓
"2"
      ↓
Number.FromText
      ↓
2
```

---

## Step 6: Set the Data Type to Whole Number

Select the `Quarter Clean` column.

From the column header, select the **Data Type** icon.

Choose:

```text
Whole Number
```

The column should contain only:

```text
1
2
3
4
```

---

## Step 7: Validate the Results

Review the `Quarter Clean` column.

Confirm that the original values have been transformed correctly.

| Original Quarter | Quarter Clean |
| ---------------- | ------------: |
| Q1 2025          |             1 |
| Q2 2025          |             2 |
| Q3 2025          |             3 |
| Q4 2025          |             4 |

The valid values for `Quarter Clean` are:

```text
1
2
3
4
```

Any value outside this range may indicate a data quality issue.

---

## Step 8: Remove the Original `Quarter` Column

After validating the new column:

1. Select the original `Quarter` column.
2. Right-click the column header.
3. Select **Remove**.

The original values such as:

```text
Q2 2025
```

should no longer be present.

---

## Step 9: Rename `Quarter Clean`

Rename:

```text
Quarter Clean
```

to:

```text
Quarter
```

The final `Quarter` column should use the **Whole Number** data type.

---

# Expected Final Result

The final table should contain a `Quarter` column similar to:

| Quarter |
| ------: |
|       1 |
|       2 |
|       3 |
|       4 |

The complete transformation process is:

```text
Q2 2025
    ↓
Trim the Value
    ↓
Extract the Value Between "Q" and the Space
    ↓
"2"
    ↓
Convert Text to Number
    ↓
2
    ↓
Remove Original Quarter Column
    ↓
Rename Quarter Clean to Quarter
```

---

# Reflection Questions

1. Why should the year be removed from the `Quarter` column?

2. What is the purpose of `Text.Trim`?

3. What does the variable `d` represent?

4. What is the purpose of the `in` keyword in a Power Query `let` expression?

5. What does `Text.BetweenDelimiters` extract from `Q2 2025`?

6. Why is `Number.FromText` required?

7. Why should the final `Quarter` column use the Whole Number data type?

8. What values are valid for the `Quarter` column?

