# Possible Visuals for ORM

| Use case                             | Visual                 | Dimension / Category field                         | Measure / Value field                                |
| ------------------------------------ | ---------------------- | -------------------------------------------------- | ---------------------------------------------------- |
| Total Outstanding Exposure           | Card                   | —                                                  | `FactExposure[Outstanding Exposure USD]`             |
| Total Commitment                     | Card                   | —                                                  | `FactExposure[Commitment Amount USD]`                |
| Number of Facilities                 | Card                   | —                                                  | `FactExposure[FacilityKey]` using **Distinct Count** |
| Exposure by Instrument               | Column chart           | `DimInstrument[Instrument]`                        | `FactExposure[Outstanding Exposure USD]`             |
| Exposure vs Commitment by Instrument | Clustered column chart | `DimInstrument[Instrument]`                        | `Outstanding Exposure USD` + `Commitment Amount USD` |
| Top 5 Countries by Exposure          | Bar chart              | `DimCountry[Country Code]`                         | `FactExposure[Outstanding Exposure USD]`             |
| Exposure by Country                  | Bar chart              | `DimCountry[Country Code]`                         | `FactExposure[Outstanding Exposure USD]`             |
| Exposure over Time                   | Line chart             | `DimDate[Month End Date]` or `DimDate[Year-Month]` | `FactExposure[Outstanding Exposure USD]`             |
| Exposure by Risk Rating              | Column chart           | `FactExposure[Risk Rating]`                        | `FactExposure[Outstanding Exposure USD]`             |
| Exposure by Sector                   | Bar chart              | `DimSector[Sector]`                                | `FactExposure[Outstanding Exposure USD]`             |
| Exposure by Subsector                | Bar chart              | `DimSector[Subsector Name (GICS L4)]`              | `FactExposure[Outstanding Exposure USD]`             |
| Exposure by Product Type             | Bar / Donut            | `DimProduct[Product Type]`                         | `FactExposure[Outstanding Exposure USD]`             |
| Exposure by Credit Vertical          | Bar chart              | `DimFacility[Credit Vertical]`                     | `FactExposure[Outstanding Exposure USD]`             |
| Exposure by Originating Division     | Bar chart              | `DimFacility[Originating Division]`                | `FactExposure[Outstanding Exposure USD]`             |
| Exposure by Currency                 | Bar / Donut            | `DimFacility[Currency]`                            | `FactExposure[Outstanding Exposure USD]`             |
| Facility details                     | Table                  | Fields from `DimFacility`, `DimObligor`, etc.      | Exposure / Commitment measures                       |
| Country × Instrument analysis        | Matrix                 | Rows: `Country Code`; Columns: `Instrument`        | `Outstanding Exposure USD`                           |

## Dax Measures

### Total Exposure

```Dax
Total Exposure =
SUM(FactExposure[Outstanding Exposure USD])
```

### Total Commitment

```Dax
Total Commitment =
SUM(FactExposure[Commitment Amount USD])
```

### Facility Count

```Dax
Facility Count =
DISTINCTCOUNT(FactExposure[FacilityKey])
```
