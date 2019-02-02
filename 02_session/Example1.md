---
follows: rootExample
---

## Design 1: Show as a time series

Aim to show the data as a time series so that we can easily see temporal trends and cycles:
![Time series design sketch](https://staff.city.ac.uk/~jwo/datavis2019/session02/images/sketch1.jpg)

```elm {v}
myBarchart : Spec
myBarchart =
  let
    enc =
      encoding
        << position X [ pName "Month", pMType Temporal ]
        << position Y [ pName "NumberOfHires", pMType Quantitative ]
        << color [ mName "AvHireTime", mMType Quantitative ]
  in
    toVegaLite [ width 540, data [], enc [], bar [] ]
```
