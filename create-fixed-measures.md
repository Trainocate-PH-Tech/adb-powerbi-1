# Creating Fixed Measures

1. Add a Card visual.
2. Drag Total Outstanding Exposure into the card.
3. Paste the following:

```dax
Fixed Total Exposure =
CALCULATE(
    SUM(FactExposure[Outstanding Exposure USD]),
    REMOVEFILTERS()
)
```
