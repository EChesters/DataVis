---
id: litvis

follows: badTeethData
---

@import "../css/datavis.less"

# Mapping Global Data

The question we wish to ask of the data is, _does the global pattern of dental health reflect the general patterns of health and economic development in 2004? If not, what are the patterns of difference?_ For the moment, we will take 'general patterns of health and economic development' to be a general knowledge of global development.

To confirm we can process the data, let's first create a simple frequency histogram of the bad teeth table:

```elm {l v}
histogram : Spec
histogram =
    let
        data =
            dataFromColumns []
                << dataColumn "badTeeth" (numColumn "badTeethPerChild" badTeethTable |> nums)

        enc =
            encoding
                << position X [ pName "badTeeth", pMType Quantitative, pBin [] ]
                << position Y [ pMType Quantitative, pAggregate opCount ]
    in
    toVegaLite [ data [], bar [], enc [] ]
```

We can provide a simple map of the data by plotting circles sized in proportion to the number of bad teeth per child at their geographic locations:

```elm {l v}
circleMap : Spec
circleMap =
    let
        data =
            dataFromColumns []
                << dataColumn "lon" (numColumn "longitude" badTeethGeoTable |> nums)
                << dataColumn "lat" (numColumn "latitude" badTeethGeoTable |> nums)
                << dataColumn "badTeeth" (numColumn "badTeethPerChild" badTeethGeoTable |> nums)

        enc =
            encoding
                << position Latitude [ pName "lat", pMType Quantitative ]
                << position Longitude [ pName "lon", pMType Quantitative ]
                << size [ mName "badTeeth", mMType Quantitative ]
    in
    toVegaLite [ width 700, height 300, data [], circle [], enc [] ]
```

You may be able to see the implied continental outlines from the circles, but we could make things easier to interpret if we combined with a bit more geographic context.

_We will consider how to do this in session 8 on geographic layout_
