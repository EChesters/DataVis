---
id: litvis

follows: tflBikeData
---

@import "../css/datavis.less"

## Mapping Damaged Bikes

A simple map that symbolises each station's damaged bikes at its London location.

_Hover pointer over a symbol to show its station name_

```elm {l v interactive}
sampleMap : Spec
sampleMap =
    let
        data =
            dataFromColumns []
                << dataColumn "station" (strColumn "commonName" combinedTable |> strs)
                << dataColumn "longitude" (numColumn "lon" combinedTable |> nums)
                << dataColumn "latitude" (numColumn "lat" combinedTable |> nums)
                << dataColumn "damaged" (numColumn "damaged" combinedTable |> nums)

        enc =
            encoding
                << position Longitude [ pName "longitude" ]
                << position Latitude [ pName "latitude" ]
                << size [ mName "damaged", mMType Quantitative ]
                << color [ mName "damaged", mMType Quantitative, mScale [ scScheme "reds" [] ] ]
                << tooltip [ tName "station", tMType Nominal ]
    in
    toVegaLite [ width 600, height 400, data [], enc [], circle [] ]
```
