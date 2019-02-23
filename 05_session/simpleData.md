---
id: litvis

elm:
  dependencies:
    gicentre/elm-vegalite: latest
    gicentre/tidy: latest
---

@import "../css/datavis.less"

```elm {l=hidden}
import Tidy exposing (..)
import VegaLite exposing (..)
```

## Simple data example

You might provide a description of the data source / provenance here.

And a tabular summary here:

```elm {m}
showTable : List String
showTable =
    tableSummary 4 myDataTable
```

## Data Shaping

Details of any data shaping with an explanation could go here.

<!-- The raw data, imported with Tidy's fromCSV function: -->

```elm {l=hidden}
myDataTable : Table
myDataTable =
    """a,b
A,28
B,55
C,43
D,91
E,81
F,53
G,19"""
        |> fromCSV
```
