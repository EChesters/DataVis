---
id: litvis

narrative-schemas:
  - ../narrative-schemas/teaching.yml

elm:
  dependencies:
    gicentre/elm-vegalite: latest
    gicentre/elm-vega: latest
    gicentre/tidy: latest
---

@import "../css/datavis.less"

```elm {l=hidden}
import Tidy exposing (..)
import Vega as V
import VegaLite exposing (..)
```

```elm {l=hidden}
carOwnershipTable : Table
carOwnershipTable =
    """borough, carOwnership
       Barking and Dagenham,60.4
       Barnet,71.3
       Bexley,76.3
       Brent,57.0
       Bromley,76.5
       Camden,38.9
       City of London,30.6
       Croydon,66.5
       Ealing,64.7
       Enfield,67.5
       Greenwich,58.0
       Hackney, 35.4
       Hammersmith and Fulham,44.8
       Haringey,48.2
       Harrow,76.5
       Havering,77.0
       Hillingdon,77.3
       Hounslow,68.4
       Islington,35.3
       Kensington and Chelsea,44.0
       Kingston upon Thames,74.9
       Lambeth,42.2
       Lewisham,51.9
       Merton,67.4
       Newham,47.9
       Redbridge,72.1
       Richmond upon Thames,75.3
       Southwark,41.6
       Sutton,76.6
       Tower Hamlets,37.0
       Waltham Forest,58.1
       Wandsworth,54.7
       Westminster,37.1"""
        |> fromCSV
```

```elm {l=hidden v interactive }
londonCircles : Spec
londonCircles =
    let
        geoData =
            dataFromUrl "https://gicentre.github.io/data/geoTutorials/londonBoroughs.json"
                [ topojsonFeature "boroughs" ]

        carData =
            dataFromColumns []
                << dataColumn "borough" (strColumn "borough" carOwnershipTable |> strs)
                << dataColumn "carOwnership" (numColumn "carOwnership" carOwnershipTable |> nums)

        centroidData =
            dataFromUrl "https://gicentre.github.io/data/geoTutorials/londonCentroids.csv"

        backgroundSpec =
            asSpec [ geoData, geoshape [ maFill "lightgrey", maStroke "white" ] ]

        trans =
            transform
                << lookup "FID" (carData []) "borough" [ "borough", "carOwnership" ]

        enc =
            encoding
                << position Longitude [ pName "cx", pMType Quantitative ]
                << position Latitude [ pName "cy", pMType Quantitative ]
                << size [ mName "carOwnership", mMType Quantitative ]
                << tooltips
                    [ [ tName "FID", tMType Nominal ]
                    , [ tName "carOwnership", tMType Quantitative ]
                    ]

        circleSpec =
            asSpec [ centroidData [], trans [], circle [], enc [] ]

        textEnc =
            encoding
                << position Longitude [ pName "cx", pMType Quantitative ]
                << position Latitude [ pName "cy", pMType Quantitative ]
                << text [ tName "borough", tMType Nominal ]

        textSpec =
            asSpec [ centroidData [], trans [], textMark [ maDy -15 ], textEnc [] ]

        cfg =
            configure
                << configuration (coView [ vicoStroke Nothing ])
                << configuration (coLegend [ lecoOrient loBottomRight, lecoOffset 0 ])
    in
    toVegaLite
        [ width 500, height 400, cfg [], carData [], layer [ backgroundSpec, circleSpec, textSpec ] ]
```
