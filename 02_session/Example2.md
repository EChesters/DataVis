---
follows: rootExample
---

## Design 2

![Scatterplot design sketch](https://staff.city.ac.uk/~jwo/datavis2019/session02/images/sketch2.jpg)

```elm {v}
myScatterplot : Spec
myScatterplot =
  let
    enc =
      encoding
        << position Y [pName "AvHireTime", pMType Quantitative]
        << position X [pName "NumberOfHires", pMType Quantitative]
        << color [mName "Month", mMType Temporal]
  in
    toVegaLite [width 500, height 500, data [], enc [], circle []]
```
