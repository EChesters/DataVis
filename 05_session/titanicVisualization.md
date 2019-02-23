---
id: litvis
follows: titanicData
---

@import "../css/datavis.less"

```elm {l=hidden}
fmColours =
    categoricalDomainMap [ ( "female", "rgb(180,90,90)" ), ( "male", "rgb(100,130,170)" ) ]
```

## Passengers who died

Gender and ticket class comparison:

```elm {l v}
barsByClass : Spec
barsByClass =
    let
        data =
            dataFromColumns []
                << dataColumn "pclass" (numColumn "pclass" deathsTable |> nums)
                << dataColumn "sex" (strColumn "sex" deathsTable |> strs)

        enc =
            encoding
                << position X [ pName "pclass", pMType Ordinal, pBin [ biExtent 1 4 ] ]
                << position Y [ pMType Quantitative, pAggregate opCount ]
                << color [ mName "sex", mMType Nominal, mScale fmColours, mLegend [ leTitle "" ] ]
    in
    toVegaLite [ width 180, data [], bar [], enc [] ]
```

Gender and age profile:

```elm {l v}
barsByAge : Spec
barsByAge =
    let
        data =
            dataFromColumns []
                << dataColumn "age" (numColumn "age" deathsTable |> nums)
                << dataColumn "sex" (strColumn "sex" deathsTable |> strs)

        enc =
            encoding
                << position X [ pName "age", pMType Ordinal, pBin [ biStep 5 ] ]
                << position Y [ pMType Quantitative, pAggregate opCount ]
                << color [ mName "sex", mMType Nominal, mScale fmColours, mLegend [ leTitle "" ] ]
    in
    toVegaLite [ width 300, data [], bar [], enc [] ]
```
