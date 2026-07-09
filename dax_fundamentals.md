# DAX Exercise: Create and Organize a Measures Table

## Objective

In this exercise, you will prepare the semantic model for DAX calculations by:

1. Creating a dedicated `_Measures` table.
2. Creating your first DAX measures.
3. Organizing measures using Display Folders.

At the end of the exercise, your `_Measures` table should be organized like this:

```text
_Measures
├── 01 - Core
│   ├── Average Disbursement
│   ├── Average Processing Days
│   ├── Total Applications
│   └── Total Disbursement
│
├── 02 - SLA
│   ├── SLA Met Applications
│   └── SLA Met %
│
└── 03 - Operations
    └── Terminal Applications
```

---

# Part 1 — Why Create a Measures Table?

Look at the current semantic model.

You should have tables similar to:

```text
FactDisbursement
DimCountry
DimSector
DimDivision
DimOfficer
DimStatus
DimDate
```

These tables contain data.

For example:

* `FactDisbursement` contains withdrawal application records.
* `DimCountry` contains country information.
* `DimStatus` describes application statuses.
* `DimDate` contains calendar and fiscal date attributes.

DAX measures are calculations.

Examples include:

```text
Total Disbursement
Total Applications
SLA Met %
Average Processing Days
```

A measure can technically be stored under different tables.

However, as the number of measures grows, placing them in different tables can make the semantic model difficult to navigate.

For this model, we will create a dedicated table called:

```text
_Measures
```

This table will be used to organize the calculations in the model.

> The `_Measures` table does not need a relationship with the other tables.

The DAX expression, filter context, and relationships in the semantic model determine how a measure is calculated.

---

# Part 2 — Create the `_Measures` Table

## Step 1: Open Enter Data

From the Power BI Desktop ribbon:

```text
Home
→ Enter data
```

The **Create Table** window appears.

---

## Step 2: Create the Table

You do not need to enter business data into the table.

Leave the values blank.

Change the table name to:

```text
_Measures
```

Select:

```text
Load
```

Power BI creates a new table named:

```text
_Measures
```

---

## Step 3: Locate the `_Measures` Table

Look at the **Data pane**.

You should now see `_Measures` alongside the other tables in the model.

For example:

```text
_Measures
DimCountry
DimDate
DimDivision
DimOfficer
DimSector
DimStatus
FactDisbursement
```

The `_Measures` table should remain disconnected from the semantic model.

Do not create a relationship between `_Measures` and another table.

---

# Part 3 — Create the First Measure

We will begin with the total amount disbursed.

## Step 1: Select the `_Measures` Table

In the Data pane, select:

```text
_Measures
```

---

## Step 2: Create a New Measure

Right-click the `_Measures` table.

Select:

```text
New measure
```

Alternatively, select `_Measures` and use:

```text
Home
→ New measure
```

---

## Step 3: Enter the DAX Expression

Enter:

```DAX
Total Disbursement =
SUM(
    FactDisbursement[Disbursement Amount (USD)]
)
```

Press:

```text
Enter
```

---

## Step 4: Verify the Measure

Expand the `_Measures` table.

You should see:

```text
_Measures
└── Total Disbursement
```

Notice the calculator icon beside the measure.

This indicates that `Total Disbursement` is a measure.

---

# Part 4 — Create Additional Core Measures

Create the following measures under the `_Measures` table.

## Measure 1: Total Applications

```DAX
Total Applications =
COUNTROWS(
    FactDisbursement
)
```

### Discussion Question

What does one row in `FactDisbursement` represent?

If one row represents one withdrawal application, counting the rows gives us the number of applications.

---

## Measure 2: Average Disbursement

```DAX
Average Disbursement =
AVERAGE(
    FactDisbursement[Disbursement Amount (USD)]
)
```

---

## Measure 3: Average Processing Days

```DAX
Average Processing Days =
AVERAGE(
    FactDisbursement[Processing Days]
)
```

---

## Checkpoint

Your `_Measures` table should now contain:

```text
_Measures
├── Average Disbursement
├── Average Processing Days
├── Total Applications
└── Total Disbursement
```

At this point, all four measures appear directly under the `_Measures` table.

As more calculations are added, this list can become difficult to navigate.

We will organize the measures using **Display Folders**.

---

# Part 5 — Create the First Display Folder

The four measures created so far are basic aggregations.

We will place them in a folder called:

```text
01 - Core
```

## Step 1: Open Model View

On the left side of Power BI Desktop, open:

```text
Model view
```

---

## Step 2: Select the Measures

Locate the `_Measures` table.

Select the following measures:

```text
Total Disbursement
Total Applications
Average Disbursement
Average Processing Days
```

Hold:

```text
Ctrl
```

while selecting multiple measures.

---

## Step 3: Locate the Properties Pane

With the measures selected, locate the:

```text
Properties
```

pane.

Find the property:

```text
Display folder
```

---

## Step 4: Enter the Folder Name

Set the Display folder value to:

```text
01 - Core
```

Press:

```text
Enter
```

---

## Step 5: Verify the Result

Look at the Data pane and expand `_Measures`.

You should now see:

```text
_Measures
└── 01 - Core
    ├── Average Disbursement
    ├── Average Processing Days
    ├── Total Applications
    └── Total Disbursement
```

The measures have not moved to another table.

They are still measures under `_Measures`.

The Display Folder only changes how the measures are organized in the Data pane.

---

# Part 6 — Create SLA Measures

We will now create measures that analyze SLA performance.

## Measure 1: SLA Met Applications

Create the following measure:

```DAX
SLA Met Applications =
CALCULATE(
    [Total Applications],
    FactDisbursement[SLA Met] = TRUE()
)
```

This measure starts with:

```DAX
[Total Applications]
```

and changes the filter context so that only applications where:

```DAX
FactDisbursement[SLA Met] = TRUE()
```

are counted.

### Discussion Question

What is the difference between these two measures?

```DAX
Total Applications =
COUNTROWS(
    FactDisbursement
)
```

and:

```DAX
SLA Met Applications =
CALCULATE(
    [Total Applications],
    FactDisbursement[SLA Met] = TRUE()
)
```

The first measure counts all applications in the current filter context.

The second measure adds an additional filter:

```text
SLA Met = TRUE
```

---

## Measure 2: SLA Met %

Create another measure:

```DAX
SLA Met % =
DIVIDE(
    [SLA Met Applications],
    [Total Applications]
)
```

Select the `SLA Met %` measure.

Format it as:

```text
Percentage
```

---

# Part 7 — Create the SLA Display Folder

Open Model view.

Select:

```text
SLA Met Applications
SLA Met %
```

In the Properties pane, set:

```text
Display folder
```

to:

```text
02 - SLA
```

Verify the `_Measures` table.

It should now appear as:

```text
_Measures
├── 01 - Core
│   ├── Average Disbursement
│   ├── Average Processing Days
│   ├── Total Applications
│   └── Total Disbursement
│
└── 02 - SLA
    ├── SLA Met Applications
    └── SLA Met %
```

---

# Part 8 — Create an Operational Measure

Create the following measure:

```DAX
Terminal Applications =
CALCULATE(
    [Total Applications],
    DimStatus[Is Terminal] = TRUE()
)
```

Notice that the filter is applied to:

```DAX
DimStatus[Is Terminal]
```

but the rows being counted are in:

```text
FactDisbursement
```

### Discussion Question

How can a filter from `DimStatus` affect `FactDisbursement`?

Review the relationship between:

```text
DimStatus
    ↓
FactDisbursement
```

The filter from the dimension table propagates to the fact table through the relationship.

This is one reason relationships are important in a semantic model.

---

# Part 9 — Create the Operations Display Folder

Select:

```text
Terminal Applications
```

In the Properties pane, set:

```text
Display folder
```

to:

```text
03 - Operations
```

---

# Final Checkpoint

Your `_Measures` table should now appear similar to:

```text
_Measures

├── 01 - Core
│   ├── Average Disbursement
│   ├── Average Processing Days
│   ├── Total Applications
│   └── Total Disbursement
│
├── 02 - SLA
│   ├── SLA Met Applications
│   └── SLA Met %
│
└── 03 - Operations
    └── Terminal Applications
```

---

# Discussion Questions

## Question 1

Why does the `_Measures` table not require a relationship with `FactDisbursement`?

## Question 2

Does putting `Total Disbursement` in the `01 - Core` Display Folder change its DAX calculation?

## Question 3

Why can `Terminal Applications` filter using `DimStatus[Is Terminal]` even though the rows being counted are in `FactDisbursement`?

## Question 4

What is the difference between the `_Measures` table and a Display Folder?

---

# Key Takeaways

A **measure** is a calculation evaluated based on the current filter context.

The `_Measures` table provides a central location for organizing DAX measures.

A **Display Folder** groups related measures for easier navigation.

Display Folders do not change how measures are calculated.

The relationships in the semantic model allow filters from dimension tables to affect calculations performed against the fact table.

In this model, we will organize measures into the following groups:

```text
01 - Core
02 - SLA
03 - Operations
04 - Time Intelligence
05 - Date Relationships
```

As additional DAX measures are created, place each one in the appropriate Display Folder.

