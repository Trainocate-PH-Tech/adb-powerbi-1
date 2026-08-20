# Parsing Integer to Date

Let's say you have a number `20260101` in column `DateNum`. To create an actual date out of it:

```dax
Date.FromText(
    Text.Start(Text.From([DateNum]), 4)
    & "-" &
    Text.Middle(Text.From([DateNum]), 4, 2)
    & "-" &
    Text.End(Text.From([DateNum]), 2)
)
```
