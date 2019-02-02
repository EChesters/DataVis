---
id: litvis
elm:
  dependencies:
    gicentre/elm-vegalite: latest
---

```elm {l=hidden}
import VegaLite exposing (..)
```

```elm {l=hidden}
brexitData =
  dataFromColumns []
    << dataColumn "ageGroup" (strs [ "18-24", "25-34", "35-44", "45-54", "55-64", "65-74", "75+"])
    << dataColumn "leave" (nums [ 0.25, 0.4, 0.45, 0.56, 0.61, 0.66, 0.63 ])
```

```elm {l v}
vlChart : Spec
vlChart =
  let
    enc =
      encoding
        << position X [ pName "ageGroup", pMType Nominal ]
        << position Y [ pName "leave", pMType Quantitative ]
  in
    toVegaLite [ brexitData [], enc [], bar [] ]
```

<!-- ```elm{l v}
tufteChart : Spec
tufteChart =
  let
    cfg =
      configure
        << configuration (coView [vicoStroke Nothing])
    enc =
      encoding
        << position X [pName "ageGroup", pMType Nominal, pAxis[]]
        << position Y [pName "leave", pMType Quantitative, pAxis []]
  in
  toVegaLite [ cfg [] , brexitData [], enc [], bar []]

``` -->

<!--
## Bar Height Labels

```elm{l=hidden v}
barChart : Spec
barChart =
  let
    cfg =
      configure
        << configuration (coView [vicoStroke Nothing])
    enc =
      encoding
        << position X [pName "ageGroup", pMType Nominal, pAxis[]]
        << position Y [pName "leave", pMType Quantitative, pAxis [ axTitle "", axValues [0.5], axFormat "%"]]
  in
  toVegaLite [ cfg [] , brexitData [], enc [], bar []]

``` -->

## Golden Ratio

```elm{l=hidden}
goldenRatio : Float
goldenRatio =
  1.618
```

```elm{l=hidden v}
ratioChart : Spec
ratioChart =
  let
    cfg =
      configure
        << configuration (coView [vicoStroke Nothing])
        << configuration (coAxis [axcoTicks False, axcoDomain False, axcoLabelAngle 0])
    enc =
      encoding
        << position X [pName "ageGroup", pMType Nominal, pAxis[]]
        << position Y [pName "leave", pMType Quantitative, pAxis [ axTitle "", axValues [0.5], axFormat "%"]]
  in
  toVegaLite
    [ width 500
    , height (500/goldenRatio)
    , cfg []
    , brexitData []
    , enc []
    , bar []
    ]

```
