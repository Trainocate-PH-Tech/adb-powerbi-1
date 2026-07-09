# Relationship Exercise: Build the Disbursement Star Schema

## Objective

Create relationships between the cleaned `FactDisbursement` table and the reference dimension tables.

The final model should follow a **star schema**, where `FactDisbursement` is the central fact table.

## Instructions

### 1. Load the Reference Tables

Import `CTL_Reference_Tables.xlsx` and load the following tables:

* `DimCountry`
* `DimSector`
* `DimDivision`
* `DimOfficer`
* `DimStatus`
* `DimDate`

Select **Transform Data** and verify that the key columns use compatible data types with the matching columns in `FactDisbursement`.

When finished, select **Close & Apply**.

---

### 2. Open Model View

Open **Model view** in Power BI.

Create the following relationships:

| Dimension Table | Dimension Column | Fact Table Column                    |
| --------------- | ---------------- | ------------------------------------ |
| DimCountry      | Country          | FactDisbursement[Country]            |
| DimSector       | Sector           | FactDisbursement[Sector]             |
| DimDivision     | Division         | FactDisbursement[Division]           |
| DimOfficer      | Officer Name     | FactDisbursement[Processing Officer] |
| DimStatus       | Status           | FactDisbursement[Status]             |

For each relationship, configure:

* **Cardinality:** One to many (`1:*`)
* **Cross-filter direction:** Single
* **Active relationship:** Yes

The dimension table should appear on the `1` side and `FactDisbursement` on the `*` side.

---

### 3. Create the Date Received Relationship

Create a relationship between:

```text
DimDate[Date]
        ↓
FactDisbursement[Date Received]
```

Configure:

* **Cardinality:** One to many (`1:*`)
* **Cross-filter direction:** Single
* **Active relationship:** Yes

The relationship should appear as a **solid line**.

This is the default date relationship used when `DimDate` filters `FactDisbursement`.

---

### 4. Create the Date Processed Relationship

Create another relationship between:

```text
DimDate[Date]
        ↓
FactDisbursement[Date Processed]
```

Configure:

* **Cardinality:** One to many (`1:*`)
* **Cross-filter direction:** Single
* **Active relationship:** No

The relationship should appear as a **dashed line**.

This relationship represents the date when a disbursement was processed.

---

### 5. Mark `DimDate` as the Date Table

In the **Data pane**:

1. Right-click `DimDate`.
2. Select **Mark as date table**.
3. Select `DimDate[Date]` as the date column.
4. Select **OK**.

---

## Expected Final Relationships

```text
DimCountry[Country]
        1 ───────── * FactDisbursement[Country]

DimSector[Sector]
        1 ───────── * FactDisbursement[Sector]

DimDivision[Division]
        1 ───────── * FactDisbursement[Division]

DimOfficer[Officer Name]
        1 ───────── * FactDisbursement[Processing Officer]

DimStatus[Status]
        1 ───────── * FactDisbursement[Status]

DimDate[Date]
        1 ───────── * FactDisbursement[Date Received]
              ACTIVE

DimDate[Date]
        1 - - - - - * FactDisbursement[Date Processed]
              INACTIVE
```

## Review Questions

1. Why is `FactDisbursement` on the `*` side of the relationships?
2. Why are the dimension tables on the `1` side?
3. Why is the cross-filter direction set to **Single**?
4. Why can `Date Received` and `Date Processed` not both be active relationships to `DimDate`?
5. What business event does `Date Received` represent?
6. What business event does `Date Processed` represent?
7. Does an inactive relationship mean that it cannot be used in Power BI?

### Expected Model

`FactDisbursement` should be at the center of the model and surrounded by:

* `DimCountry`
* `DimSector`
* `DimDivision`
* `DimOfficer`
* `DimStatus`
* `DimDate`

This creates the star schema required for the disbursement semantic model.

