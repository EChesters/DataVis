---
id: litvis

narrative-schemas:
  - ../narrative-schemas/teaching.yml

elm:
  dependencies:
    gicentre/elm-vegalite: latest
---

@import "../css/datavis.less"

```elm {l=hidden}
import VegaLite exposing (..)
```

```elm {l v}
allDeaths : Spec
allDeaths =
  let
    data =
      dataFromUrl "Refugee_Death_Events.csv"

    cycleHireData =
        dataFromUrl "https://gicentre.github.io/data/bicycleHiresLondon.json"

    encDeaths =
      encoding
        << position X [ pName "Month", pMType Temporal]
        << position Y [ pName "NumberOfHires", pMType Quantitative]
          -- << position X [ pName "Year", pMType Temporal]
          -- << position Y [ pName "dead", pMType Quantitative]

  in
  -- toVegaLite [data [], encDeaths [], line []]
  toVegaLite [ width 600, cycleHireData [], encoding [], line [] ]


```
